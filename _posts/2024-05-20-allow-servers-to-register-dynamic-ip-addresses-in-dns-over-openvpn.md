## Allowing servers to register with DNS over VPN

I had a scenario where I have multiple OVH virtual private servers (VPSs) that need some basic connectivity back to my HQ. I want my remote servers to register themselves in DNS to allow for easy management, patching, and monitoring, but I don't want to maintain a list of static IPs. Since the servers are "cost-effective" VPSs (rather than OVH "public cloud" instances), they are not eligible for vRack networking. Instead, I have to configure each server/VM as a VPN client terminating on my HQ OPNSense Firewall.

### VPN

For the initial setup, I created an OpenVPN instance called "Remote Infrastructure" alongside a few users with certificates. OPNSense makes it easy enough to assign a DHCP pool for OpenVPN clients using the "Server (IPv4)" setting. 

Once the client configuration is exported, it's just a simple matter of storing it on the remote servers at `/etc/openvpn`, ensuring the connection is established every time the servers boot.

In my case, I require *both* username/password and certificate authentication, so placed the credentials into a TXT file and referenced in the VPN config using the `auth-user-pass` parameter. Naturally, the firewall is configured to only allow VPN connections from these servers anyway.

### DNS

Getting the servers to register themselves in DNS required a little extra work. The OPNSense GUI has options for "Register DNS" and "push register-dns" however these did not work in my testing (the documentation suggests these are Windows-only options). 

First, I created a separate DNS zone named `ovh.internal.X.co.uk` and configured non-secure dynamic updates (as the VPSs are not domain-joined at this stage, although that might be an option in the future).

After some time experimenting with different options, I eventually wrote a small script to force each server to register itself in DNS each time the VPN connection is made. The quirk I found with this is that OpenVPN UP scripts see to prevent the VPN tunnel connecting until they successfully complete. But as `nsupdate` needs connectivity to a DC to register itself in DNS, I found myself in a Catch-22. 
The solution was to background the script and schedule it to run in a few minutes time, which seems to allow the VPN connection to complete and then register the server in DNS afterwards.

    /etc/openvpn/register-dns.sh:-
    
    #!/bin/bash
    ip=$(ip addr show dev tun0 | awk '$1 == "inet" {gsub(/\/.*$/, "", $2); print $2}')
    echo "server 192.168.1.Y" > register-dns-nsupdatecmd.txt
    echo "update add $(hostname).ovh.internal.X.co.uk. 600 a $ip" >> register-dns-nsupdatecmd.txt
    echo "send" >> register-dns-nsupdatecmd.txt
    nsupdate register-dns-nsupdatecmd.txt | at now + 1 minute &
    
The script must be included in the client VPN config to be run each time the tunnel connects

    /etc/openvpn/vpn.conf:-
    
    script-security 2
    up /etc/openvpn/register-dns.sh

![dns.png](/img/2024-05-20-allow-servers-to-register-dynamic-ip/dns.png)

I couldn't find loads about this online (presumably because it's an unusual scenario), so thought I'd share the experience to potentially save others time and to remind myself how this works when it inevitably breaks!
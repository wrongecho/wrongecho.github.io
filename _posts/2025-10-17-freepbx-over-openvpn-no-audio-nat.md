## Audio issues using FreePBX over VPN

I was running into no audio/one-way transmission issues where remote SIP clients could register with the PBX over the VPN, but not hear each other. No traffic was being blocked on the Firewall, and I had the remote VPN network trusted by FreePBX in Firewall > Networks.

It seems that you also have to tell FreePBX/Asterisk about your remote VPN network for the purposes of its own NAT.

Navigate to Settings > Asterisk SIP Settings > NAT Settings > Add local network field

![asterisk-nat.png](/img/2025-10-17-freepbx-over-openvpn-no-audio-nat/asterisk-nat.png)

Once complete, calls worked fine!

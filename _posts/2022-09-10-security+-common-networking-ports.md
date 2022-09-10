## Security+ Common Ports

### File Transfer

| Service    | TCP    | UDP |
|------------|--------|-----|
| FTP        | 20, 21 |     |
| SCP, SFTP  | 22     |     |
| TFTP       |        | 69  |
| FTPS (SSL) | 443    |     |
| SMB        | 445    |     |

### E-mail

| Service | TCP | UDP |
|---------|-----|-----|
| SMTP    | 25  |     |
| POP3    | 110 |     |
| IMAP4   | 143 |     |

### Remote Access/Connections

| Service                                                                  | TCP  | UDP        |
|--------------------------------------------------------------------------|------|------------|
| SSH                                                                      | 22   |            |
| Telnet                                                                   | 23   |            |
| TACACS (+) (Terminal Access Controller Access-Control System)            | 49   |            |
| SSL VPN                                                                  | 443  |            |
| ISAKMP (Internet Security Association & Key Management Protocol (VPN))   |      | 500        |
| L2TP (Layer 2 Tunneling Protocol)                                        |      | 1701       |
| PPTP (Point-to-Point Tunneling Protocol)                                 | 1723 | 1723       |
| RADIUS (Remote Access Dial In User Service)                              |      | 1812, 1813 |
| RDP (Remote Desktop Protocol)                                            | 3389 | 3389       |
| Diameter (AAA protocol, can replace RADIUS)                              | 3868 |            |

### Miscellaneous

| Service                                      | TCP             | UDP             |
|----------------------------------------------|-----------------|-----------------|
| DNS                                          | 53              | 53              |
| DHCP                                         |                 | 67, 68          |
| HTTP                                         | 80              |                 |
| HTTPS                                        | 443             |                 |
| Kerberos                                     | 88              | 88              |
| NNTP (Network News Transfer Protocol)        | 119             |                 |
| MS-SQL-S (Microsoft SQL Server)              | 1433            |                 |
| SNMP (Simple Network Management Protocol)    |                 | 161             |
| SNMP Trap                                    |                 | 162             |
| LDAP (Lightweight Directory Access Protocol) | 389,  636 (TLS) | 389,  636 (TLS) |
| Syslog                                       |                 | 514             |

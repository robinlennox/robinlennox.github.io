# Playbooks Examples

| Type                | Rule                                                            | Description |
|---------------------|-----------------------------------------------------------------|-------------|
| Malware             | M001 - Malware cleanup failed                                   |             |
| Malware             | M002 - Malware detected                                         |             |
| Login               | L001 - Superuser domain account used                            |             |
| Login               | L002 - Superuser local account used                             |             |
| Login               | L003 - Excessive number of accounts failed login                |             |
| Login               | L004 - Excessive number of failed logins from one asset         |             |
| Login               | L005 - Local account created on critical asset                  |             |
| Login               | L006 - One account successfully logged onto multiple assets     |             |
| Login               | L007 - Excessive number of accounts lockouts                    |             |
| Login               | L008 - Excessive number of failed logins on gateway asset       |             |
| Suspicious Activity | S001 - successful inbound remote access suspicious protocol     |             |
| Suspicious Activity | S002 - successful inbound remote access suspicious port         |             |
| Suspicious Activity | S003 - Outbound connection With suspicious port                 |             |
| Suspicious Activity | S004 - Outbound connection With suspicious protocol             |             |
| Suspicious Activity | S005 - Inbound successful connection from suspicious IP address |             |
| Suspicious Activity | S006 - Outbound connection to suspicious IP address             |             |
| Suspicious Activity | S007 - Outbound connection to TOR exit node                     |             |
| Suspicious Activity | S008 - Multiple inbound connection from TOR exit node           |             |
| Suspicious Activity | S009 - Ping outbound                                            |             |
| Suspicious Activity | S010 - Critical assets remotely accessed by non jumphost        |             |
| Suspicious Activity | S011 - Honeypot user triggered                                  |             |
| Suspicious Activity | S012 - Proxy bypass                                             |             |
| Suspicious Activity | S013 - Non jumphost user accessed jumphost                      |             |
| Suspicious Activity | S014 - Service account logged on interactively                  |             |
| Suspicious Activity | S015 - Endpoint protection disabled                             |             |
| Recon               | R001 - Internal network scan                                    |             |
| Recon               | R002 - External network scan                                    |             |
| Recon               | R003 - Internal service discovery                               |             |
| Recon               | R004 - External service discovery                               |             |

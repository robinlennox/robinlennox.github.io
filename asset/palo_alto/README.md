# Palo Alto
## Field Mapping

| Log Type | PAN-OS Field         | ECS Field                              | Non-standard field               |
|----------|----------------------|----------------------------------------|----------------------------------|
| TRAFFIC  | Receive Time         | event.created                          |                                  |
| TRAFFIC  | Serial Number        | observer.serial_number                 |                                  |
| TRAFFIC  | Type                 | event.category                         |                                  |
| TRAFFIC  | Subtype              | event.action                           |                                  |
| TRAFFIC  | Generated Time       | @timestamp                             |                                  |
| TRAFFIC  | Source IP            | client.ip source.ip                    |                                  |
| TRAFFIC  | Destination IP       | server.ip destination.ip               |                                  |
| TRAFFIC  | NAT Source IP        |                                        | panw.panos.source.nat.ip         |
| TRAFFIC  | NAT Destination IP   |                                        | panw.panos.destination.nat.ip    |
| TRAFFIC  | Rule Name            |                                        | panw.panos.ruleset               |
| TRAFFIC  | Source User          | client.user.name source.user.name      |                                  |
| TRAFFIC  | Destination User     | server.user.name destination.user.name |                                  |
| TRAFFIC  | Application          | network.application                    |                                  |
| TRAFFIC  | Source Zone          |                                        | panw.panos.source.zone           |
| TRAFFIC  | Destination Zone     |                                        | panw.panos.destination.zone      |
| TRAFFIC  | Ingress Interface    |                                        | panw.panos.source.interface      |
| TRAFFIC  | Egress Interface     |                                        | panw.panos.destination.interface |
| TRAFFIC  | Session ID           |                                        | panw.panos.flow_id               |
| TRAFFIC  | Source Port          | client.port source.port                |                                  |
| TRAFFIC  | Destination Port     | destination.port server.port           |                                  |
| TRAFFIC  | NAT Source Port      |                                        | panw.panos.source.nat.port       |
| TRAFFIC  | NAT Destination Port |                                        | panw.panos.destination.nat.port  |
| TRAFFIC  | Flags                | labels                                 |                                  |
| TRAFFIC  | Protocol             | network.transport                      |                                  |
| TRAFFIC  | Action               | event.outcome                          |                                  |
| TRAFFIC  | Bytes                | network.bytes                          |                                  |
| TRAFFIC  | Bytes Sent           | client.bytes destination.bytes         |                                  |
| TRAFFIC  | Bytes Received       | server.bytes source.bytes              |                                  |
| TRAFFIC  | Packets              | network.packets                        |                                  |
| TRAFFIC  | Start Time           | event.start                            |                                  |
| TRAFFIC  | Elapsed Time         | event.duration                         |                                  |
| TRAFFIC  | Category             |                                        | panw.panos.url.category          |
| TRAFFIC  | Sequence Number      |                                        | panw.panos.sequence_number       |
| TRAFFIC  | Packets Sent         | server.packets destination.packets     |                                  |
| TRAFFIC  | Packets Received     | client.packets source.packets          |                                  |
| TRAFFIC  | Device Name          | observer.hostname                      |                                  |
| THREAT   | Receive Time         | event.created                          |                                  |
| THREAT   | Serial Number        | observer.serial_number                 |                                  |
| THREAT   | Type                 | event.category                         |                                  |
| THREAT   | Subtype              | event.action                           |                                  |
| THREAT   | Generated Time       | @timestamp                             |                                  |
| THREAT   | Source IP            | client.ip source.ip                    |                                  |
| THREAT   | Destination IP       | server.ip destination.ip               |                                  |
| THREAT   | NAT Source IP        |                                        | panw.panos.source.nat.ip         |
| THREAT   | NAT Destination IP   |                                        | panw.panos.destination.nat.ip    |
| THREAT   | Rule Name            |                                        | panw.panos.ruleset               |
| THREAT   | Source User          | client.user.name source.user.name      |                                  |
| THREAT   | Destination User     | server.user.name destination.user.name |                                  |
| THREAT   | Application          | network.application                    |                                  |
| THREAT   | Source Zone          |                                        | panw.panos.source.zone           |
| THREAT   | Destination Zone     |                                        | panw.panos.destination.zone      |
| THREAT   | Ingress Interface    |                                        | panw.panos.source.interface      |
| THREAT   | Egress Interface     |                                        | panw.panos.destination.interface |
| THREAT   | Session ID           |                                        | panw.panos.flow_id               |
| THREAT   | Source Port          | client.port source.port                |                                  |
| THREAT   | Destination Port     | destination.port server.port           |                                  |
| THREAT   | NAT Source Port      |                                        | panw.panos.source.nat.port       |
| THREAT   | NAT Destination Port |                                        | panw.panos.destination.nat.port  |
| THREAT   | Flags                | labels                                 |                                  |
| THREAT   | Protocol             | network.transport                      |                                  |
| THREAT   | Action               | event.outcome                          |                                  |
| THREAT   | Miscellaneous        | url.original                           | panw.panos.threat.resource       |
| THREAT   | Threat ID            |                                        | panw.panos.threat.id             |
| THREAT   | Category             |                                        | panw.panos.url.category          |
| THREAT   | Severity             | log.level                              |                                  |
| THREAT   | Direction            | network.direction                      |                                  |
| THREAT   | Source Location      | source.geo.name                        |                                  |
| THREAT   | Destination Location | destination.geo.name                   |                                  |
| THREAT   | PCAP_id              |                                        | panw.panos.network.pcap_id       |
| THREAT   | Filedigest           |                                        | panw.panos.file.hash             |
| THREAT   | User Agent           | user_agent.original                    |                                  |
| THREAT   | File Type            | file.type                              |                                  |
| THREAT   | X-Forwarded-For      | network.forwarded_ip                   |                                  |
| THREAT   | Referer              | http.request.referer                   |                                  |
| THREAT   | Sender               | source.user.email                      |                                  |
| THREAT   | Subject              |                                        | panw.panos.subject               |
| THREAT   | Recipient            | destination.user.email                 |                                  |
| THREAT   | Device Name          | observer.hostname                      |                                  |

The source of this infomration was from [elastic.co](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-panw.html)
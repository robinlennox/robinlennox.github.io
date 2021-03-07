# NetScaler
NetScaler uses [RFC 5424](https://tools.ietf.org/rfc/rfc5426.txt); if your log is missing a date in the field section, this could you or an intermediary log aggregator is configured to parse the log under [RFC 3164] (https://tools.ietf.org/rfc/rfc3164.txt). This date could be in field call XYZ

# Log Format
<135> 12/04/2017:17:21:00 GMT citrix.netscaler.test 0-PPE-1 : SSLLOG SSL_HANDSHAKE_SUCCESS 5743593 0 :  SPCBId 87630 - ClientIP 172.25.184.157 - ClientPort 19849 - VserverServiceIP 10.254.14.94 - VserverServicePort 443 - ClientVersion TLSv1.2 - CipherSuite "RC4-MD5 TLSv1.2 Non-Export 128-bit" - Session ReuseCopy code

| NetScaler Field  | Example   |
|------------------|-------------------------|
| Event ID         | SSL_HANDSHAKE_SUCCESS   |
| Source IP        | 172.25.184.157          |
| Source Port      | 19849                   |
| Destination IP   | 10.254.14.94            |
| Destination Port | 443                     |
| Device Time      | 12/04/2017:17:21:00 GMT |

<https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_Citrix_NetScaler_sample_event_msg.html>

## Parsing/Normalizing

### Grok
> <%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{IP:source_ip}:%{POSINT:source_port} - %{DATA} %{IP:vserver_ip}:%{POSINT:vserver_port} - %{DATA} %{IP:nat_ip}:%{POSINT:nat_port} - %{DATA} %{IP:destination_ip}:%{POSINT:destination_port} - %{DATA} %{DATE_US:DELINK_DATE}:%{TIME:DELINK_TIME} GMT - %{DATA} %{POSINT:total_bytes_sent} - %{DATA} %{POSINT:total_bytes_recv}"

> <%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{IP:source_ip}:%{POSINT:source_port} - %{DATA} %{IP:destination_ip}:%{POSINT:destination_port} - %{DATA} %{DATE_US:START_DATE}:%{TIME:START_TIME} GMT - %{DATA} %{DATE_US:END_DATE}:%{TIME:END_TIME} GMT - %{DATA} %{POSINT:total_bytes_sent} - %{DATA} %{POSINT:total_bytes_recv}"

> "<%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{INT:netscaler_spcbid} - %{DATA} %{IP:clientip} - %{DATA} %{INT:netscaler_client_port} - %{DATA} %{IP:netscaler_vserver_ip} - %{DATA} %{INT:netscaler_vserver_port} %{GREEDYDATA:netscaler_message} - %{DATA} %{WORD:netscaler_session_type}"

>"<%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message}"

### Logstash
```yml
input {
  syslog {
    type => "netscaler"
    port => "5560"
  }
}

filter {

  # Set tags for ASAs
  if [host] == "192.168.174.23" {
    mutate {
      add_field => [ "@netscaler", "REY" ]
      add_tag => "netscaler"
    }
  }

  # Parse the date
  date {
    match => ["timestamp",
      "MMM dd HH:mm:ss",
      "MMM  d HH:mm:ss",
      "MMM dd yyyy HH:mm:ss",
      "MMM  d yyyy HH:mm:ss"
    ]
  }
}

# Setting up Citrix Netscaler parsing
filter {
        if "netscaler" in [tags] {
                grok {
                        break_on_match => true
                        match => [
                                "message", "<%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{IP:source_ip}:%{POSINT:source_port} - %{DATA} %{IP:vserver_ip}:%{POSINT:vserver_port} - %{DATA} %{IP:nat_ip}:%{POSINT:nat_port} - %{DATA} %{IP:destination_ip}:%{POSINT:destination_port} - %{DATA} %{DATE_US:DELINK_DATE}:%{TIME:DELINK_TIME} GMT - %{DATA} %{POSINT:total_bytes_sent} - %{DATA} %{POSINT:total_bytes_recv}",
                                "message", "<%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{IP:source_ip}:%{POSINT:source_port} - %{DATA} %{IP:destination_ip}:%{POSINT:destination_port} - %{DATA} %{DATE_US:START_DATE}:%{TIME:START_TIME} GMT - %{DATA} %{DATE_US:END_DATE}:%{TIME:END_TIME} GMT - %{DATA} %{POSINT:total_bytes_sent} - %{DATA} %{POSINT:total_bytes_recv}",
                                "message", "<%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{INT:netscaler_spcbid} - %{DATA} %{IP:clientip} - %{DATA} %{INT:netscaler_client_port} - %{DATA} %{IP:netscaler_vserver_ip} - %{DATA} %{INT:netscaler_vserver_port} %{GREEDYDATA:netscaler_message} - %{DATA} %{WORD:netscaler_session_type}",
                                "message", "<%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message}"
                        ]
                }
                syslog_pri { }
                geoip {
                        database => "/etc/logstash/geoip/GeoLiteCity.dat"
                        source => "source_ip"
                        target => "geoip"
                        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
                        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
                        add_field => [ "[geoip][country_name]", "%{[geoip][country_name]}"  ]
                        add_field => [ "[geoip][city_name]", "%{[geoip][city_name]}"  ]
                }
                mutate {
                        add_field => [ "src_ip", "%{source_ip}" ]
                        convert => [ "[geoip][coordinates]", "float" ]
                        replace => [ "@source_host", "%{host}" ]
                        replace => [ "@message", "%{netscaler_message}" ]
                }
        }
}

output {
 elasticsearch { 
   host => "xxx.xxxx.is"
   cluster => "xxxxx.logging"
 }
}
```
<https://gist.github.com/haukurk/95a7dad58ff475fbb987#file-logstash-netscaler-conf>

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

The source of this information was from [elastic.co](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-panw.html)

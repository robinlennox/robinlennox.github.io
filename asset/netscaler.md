# NetScaler
NetScaler uses [RFC 5424](https://tools.ietf.org/rfc/rfc5426.txt); if your log is missing a date in the field section, this could you or an intermediary log aggregator is configured to parse the log under [RFC 3164] (https://tools.ietf.org/rfc/rfc3164.txt). This date could be in field call XYZ

## Log Format
### Delink
> SYSLOG_PRIORITY DATE_US TIME GMT SYSLOGHOST NETSCALER_MESSAGE : DATA SOURCE_IP:SOURCE_PORT - DATA VIRTUAL_IP:VIRTUAL_PORT - DATA NATL_IP:NAT_PORT - DATA DESTINATION_IP:DESTINATION_PORT - DATA DELINK_DATE:DELINK_TIME GMT - DATA TOTAL_BYTES_SENT - DATA TOTAL_BYTES_RECEIVED

### Start/End Date
> SYSLOG_PRIORITY DATE_US TIME GMT SYSLOGHOST NETSCALER_MESSAGE : DATA SOURCE_IP:SOURCE_PORT - DATA DESTINATION_IP:DESTINATION_PORT - DATA START_DATE:START_TIME GMT - DATA END_DATE:END_TIME GMT - DATA TOTAL_BYTES_SENT - DATA TOTAL_BYTES_RECEIVED

### SPCBId VServer
> SYSLOG_PRIORITY DATE_US TIME GMT SYSLOGHOST NETSCALER_MESSAGE : DATA NETSCALER_SPCBID - DATA CLIENT_IP - DATA CLIENT_PORT - DATA VIRTUAL_IP - DATA VIRTUAL_PORT NETSCALER_MESSAGE - DATA SESSION_TYPE"

### Catch All
><135> 12/04/2017:17:21:00 GMT citrix.netscaler.test 0-PPE-1 : SSLLOG SSL_HANDSHAKE_SUCCESS 5743593 0 :  SPCBId 87630 - ClientIP 172.25.184.157 - ClientPort 19849 - VserverServiceIP 10.254.14.94 - VserverServicePort 443 - ClientVersion TLSv1.2 - CipherSuite "RC4-MD5 TLSv1.2 Non-Export 128-bit" - Session ReuseCopy code

## Log Sample

### Delink
> <134> May 20 23:35:25 GMT vm-server01 10.217.245.231 08/27/2012:20:00:47 GMT  0-PPE-0 : TCP CONN_DELINK 4389 0 :  Source 10.210.224.177:59839 - Vserver 10.217.245.233:80 - NatIP 10.217.245.232:17708 - Destination 192.168.10.1:80 - Delink Time 08/27/2012:20:00:47 GMT - Total_bytes_send 464 - Total_bytes_recv 64248

### Start/End Date
> <134> May 20 23:35:25 GMT vm-server01 05/20/2014:21:35:25 GMT server01 0-PPE-0 : TCP CONN_TERMINATE 809689 0 : Source 127.0.0.1:7776 - Destination 127.0.0.2:59919 - Start Time 05/20/2014:21:34:42 GMT - End Time 05/20/2014:21:35:25 GMT - Total_bytes_send 1 - Total_bytes_recv 1


### SPCBId VServer
> <134> May 20 23:35:25 GMT vm-server01 0-PPE-0 : SSLLOG SSL_HANDSHAKE_SUCCESS 6103955 0 :  SPCBId 59218 - ClientIP 142.28.165.235 - ClientPort 56308 - VserverServiceIP 192.168.1.200 - VserverServicePort 443 - ClientVersion TLSv1.0 - CipherSuite "RC4-MD5 TLSv1  Non-Export 128-bit" - Session Reuse

### Catch All
> <134> May 20 23:35:25 GMT vm-server01 0-PPE-0 : default AAA Message 2266162 0 :  " In receive_ldap_user_bind_event: ldap_bind user failed for user user1"
>"<%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message}

## Parsing/Normalizing

### Grok
#### Delink
> <%{INT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{IP:source_ip}:%{INT:source_port} - %{DATA} %{IP:vserver_ip}:%{INT:vserver_port} - %{DATA} %{IP:nat_ip}:%{INT:nat_port} - %{DATA} %{IP:destination_ip}:%{INT:destination_port} - %{DATA} (?<delink_time>%{DATE}:%{TIME}) GMT %{DATA} %{INT:total_bytes_sent} - %{DATA} %{INT:total_bytes_recv}"

#### Start/End Date
> <%{INT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{IP:source_ip}:%{INT:source_port} - %{DATA} %{IP:destination_ip}:%{INT:destination_port} - %{DATA} (?<start_time>%{DATE}%{TIME}) GMT - %{DATA} (?<end_time>%{DATE}%{TIME}) GMT - %{DATA} %{INT:total_bytes_sent} - %{DATA} %{INT:total_bytes_recv}"

#### SPCBId VServer
> "<%{INT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{INT:netscaler_spcbid} - %{DATA} %{IP:source_ip} - %{DATA} %{INT:source_port} - %{DATA} %{IP:vserver_ip} - %{DATA} %{INT:vserver_port} - %{GREEDYDATA:netscaler_message} - %{DATA} %{WORD:netscaler_session_type}"

#### Catch All
>"<%{POSINT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message}"

### Logstash
```python
input {
  syslog {
    type => "netscaler"
    port => "5560"
  }
}

filter {

  # Set tags for ASAs
  if [host] == "192.168.1.10" {
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

| NetScaler Field      | ECS Field                              | Non-standard field               |
|----------|----------------------|----------------------------------------|----------------------------------|
| SYSLOG_PRIORITY          | log.syslog.priority                    |                                  |
| DATE_US TIME GMT         | @timestamp                             |                                  |
| SYSLOGHOST               | observer.hostname                      |                                  |
| NETSCALER_MESSAGE        | message                                |                                  |
| SOURCE_IP                | source.ip                              |                                  |
| SOURCE_PORT              | client.port                            |                                  |
| VIRTUAL_IP               | server.nat.ip                          |                                  |
| VIRTUAL_PORT             | server.nat.port                        |                                  |
| NAT_IP                   | client.nat.ip                          |                                  |
| NAT_PORT                 | client.nat.port                        |                                  |
| DESTINATION_IP           | destination.ip                         |                                  |
| DESTINATION_PORT         | destination.port                       |                                  |
| START_DATE:START_TIME    | event.start                            |                                  |
| END_DATE:END_TIME        | event.start                            |                                  |
| TOTAL_BYTES_SENT         | client.bytes destination.bytes         |                                  |
| TOTAL_BYTES_RECEIVED     | server.bytes source.bytes              |                                  |
| DELINK_DATE:DELINK_TIME  |                                        | netscaler.delink                 |

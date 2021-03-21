# Microsoft OMS Agent

The OMS (Operations Management Suite) Agent is used on Azure to send real-time analytics for operational data (Syslog, performance, alerts, inventory) from Linux servers, Docker containers and monitoring tools like Nagios, Zabbix and System Center to [Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/agents/log-analytics-agent).

THe logs should be in the standard syslog format.

https://docs.microsoft.com/en-us/azure/azure-monitor/agents/agent-linux-troubleshoot
<https://github.com/KTH/oms-to-elk>

Extact on log type?

## Log Format
### Delink
> SYSLOG_PRIORITY DATE_US TIME GMT SYSLOGHOST NETSCALER_MESSAGE : DATA SOURCE_IP:SOURCE_PORT - DATA VIRTUAL_IP:VIRTUAL_PORT - DATA NATL_IP:NAT_PORT - DATA DESTINATION_IP:DESTINATION_PORT - DATA DELINK_DATE:DELINK_TIME GMT - DATA TOTAL_BYTES_SENT - DATA TOTAL_BYTES_RECEIVED

## Parsing/Normalizing

### Grok
#### Delink
> <%{INT:syslog_pri}> %{DATE_US}:%{TIME} GMT %{SYSLOGHOST:syslog_hostname} %{GREEDYDATA:netscaler_message} : %{DATA} %{IP:source_ip}:%{INT:source_port} - %{DATA} %{IP:vserver_ip}:%{INT:vserver_port} - %{DATA} %{IP:nat_ip}:%{INT:nat_port} - %{DATA} %{IP:destination_ip}:%{INT:destination_port} - %{DATA} (?<delink_time>%{DATE}:%{TIME}) GMT %{DATA} %{INT:total_bytes_sent} - %{DATA} %{INT:total_bytes_recv}"

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

| NetScaler Field         | ECS Field                      | Non-standard field |
|-------------------------|--------------------------------|--------------------|
| SYSLOG_PRIORITY         | log.syslog.priority            |                    |
| DATE_US TIME GMT        | @timestamp                     |                    |
| SYSLOGHOST              | observer.hostname              |                    |
| NETSCALER_MESSAGE       | message                        |                    |
| SOURCE_IP               | source.ip                      |                    |
| SOURCE_PORT             | client.port                    |                    |
| VIRTUAL_IP              | server.nat.ip                  |                    |
| VIRTUAL_PORT            | server.nat.port                |                    |
| NAT_IP                  | client.nat.ip                  |                    |
| NAT_PORT                | client.nat.port                |                    |
| DESTINATION_IP          | destination.ip                 |                    |
| DESTINATION_PORT        | destination.port               |                    |
| START_DATE:START_TIME   | event.start                    |                    |
| END_DATE:END_TIME       | event.end                      |                    |
| TOTAL_BYTES_SENT        | client.bytes destination.bytes |                    |
| TOTAL_BYTES_RECEIVED    | server.bytes source.bytes      |                    |
| DELINK_DATE:DELINK_TIME |                                | netscaler.delink   |
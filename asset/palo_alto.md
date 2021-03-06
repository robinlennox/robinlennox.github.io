# Palo Alto

## Log format
### Threat (V10.0)
>  FUTURE_USE, Receive Time, Serial Number, Type, Threat/Content Type, FUTURE_USE, Generated Time, Source Address, Destination Address, NAT Source IP, NAT Destination IP, Rule Name, Source User, Destination User, Application, Virtual System, Source Zone, Destination Zone, Inbound Interface, Outbound Interface, Log Action, FUTURE_USE, Session ID, Repeat Count, Source Port, Destination Port, NAT Source Port, NAT Destination Port, Flags, IP Protocol, Action, URL/Filename, Threat ID, Category, Severity, Direction, Sequence Number, Action Flags, Source Location, Destination Location, FUTURE_USE, Content Type, PCAP_ID, File Digest, Cloud, URL Index, User Agent, File Type, X-Forwarded-For, Referer, Sender, Subject, Recipient, Report ID, Device Group Hierarchy Level 1, Device Group Hierarchy Level 2, Device Group Hierarchy Level 3, Device Group Hierarchy Level 4, Virtual System Name, Device Name, FUTURE_USE, Source VM UUID, Destination VM UUID, HTTP Method, Tunnel ID/IMSI, Monitor Tag/IMEI, Parent Session ID, Parent Start Time, Tunnel Type, Threat Category, Content Version, FUTURE_USE, SCTP Association ID, Payload Protocol ID, HTTP Headers, URL Category List, Rule UUID, HTTP/2 Connection, Dynamic User Group Name, XFF Address, Source Device Category, Source Device Profile, Source Device Model, Source Device Vendor, Source Device OS Family, Source Device OS Version, Source Hostname, Source MAC Address, Destination Device Category, Destination Device Profile, Destination Device Model, Destination Device Vendor, Destination Device OS Family, Destination Device OS Version, Destination Hostname, Destination MAC Address, Container ID, POD Namespace, POD Name, Source External Dynamic List, Destination External Dynamic List, Host ID, Serial Number, Domain EDL, Source Dynamic Address Group, Destination Dynamic Address Group, Session Owner, Partial Hash, High Resolution Timestamp, Reason, Justification, A Slice Service Type

<https://docs.paloaltonetworks.com/pan-os/10-0/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html>

### Traffic (V10.0)
> FUTURE_USE, Receive Time, Serial Number, Type, Threat/Content Type, FUTURE_USE, Generated Time, Source Address, Destination Address, NAT Source IP, NAT Destination IP, Rule Name, Source User, Destination User, Application, Virtual System, Source Zone, Destination Zone, Inbound Interface, Outbound Interface, Log Action, FUTURE_USE, Session ID, Repeat Count, Source Port, Destination Port, NAT Source Port, NAT Destination Port, Flags, Protocol, Action, Bytes, Bytes Sent, Bytes Received, Packets, Start Time, Elapsed Time, Category, FUTURE_USE, Sequence Number, Action Flags, Source Country, Destination Country, FUTURE_USE, Packets Sent, Packets Received, Session End Reason, Device Group Hierarchy Level 1, Device Group Hierarchy Level 2, Device Group Hierarchy Level 3, Device Group Hierarchy Level 4, Virtual System Name, Device Name, Action Source, Source VM UUID, Destination VM UUID, Tunnel ID/IMSI, Monitor Tag/IMEI, Parent Session ID, Parent Start Time, Tunnel Type, SCTP Association ID, SCTP Chunks, SCTP Chunks Sent, SCTP Chunks Received, Rule UUID, HTTP/2 Connection, App Flap Count, Policy ID, Link Switches, SD-WAN Cluster, SD-WAN Device Type, SD-WAN Cluster Type, SD-WAN Site, Dynamic User Group Name, XFF Address, Source Device Category, Source Device Profile, Source Device Model, Source Device Vendor, Source Device OS Family, Source Device OS Version, Source Hostname, Source Mac Address, Destination Device Category, Destination Device Profile, Destination Device Model, Destination Device Vendor, Destination Device OS Family, Destination Device OS Version, Destination Hostname, Destination Mac Address, Container ID, POD Namespace, POD Name, Source External Dynamic List, Destination External Dynamic List, Host ID, Serial Number, Source Dynamic Address Group, Destination Dynamic Address Group, Session Owner, High Resolution Timestamp, A Slice Service Type, A Slice Differentiator

<https://docs.paloaltonetworks.com/pan-os/10-0/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/traffic-log-fields.html>

### System (V10.0)
>  FUTURE_USE, Receive Time, Serial Number, Type, Content/Threat Type, FUTURE_USE, Generated Time, Virtual System, Event ID, Object, FUTURE_USE, FUTURE_USE, Module, Severity, Description, Sequence Number, Action Flags, Device Group Hierarchy Level 1, Device Group Hierarchy Level 2, Device Group Hierarchy Level 3, Device Group Hierarchy Level 4, Virtual System Name, Device Name, FUTURE_USE, FUTURE_USE, High Resolution Timestamp

<https://docs.paloaltonetworks.com/pan-os/10-0/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/system-log-fields.html>
## Log sample

### Threat
> Oct 30 09:46:12 1,2012/10/30 09:46:12,01606001116,THREAT,url,1,2012/04/10 04:39:57,192.168.0.2,204.232.231.46,0.0.0.0,0.0.0.0,rule1,crusher,,web-browsing,vsys1,trust,untrust,ethernet1/2,ethernet1/1,forwardAll,2012/04/10 04:39:58,25848,1,59317,80,0,0,0x208000,tcp,alert,"lsiu.info/evo/exploits/x19.php?o=2&t=1241403746&i=1365814122",(9999),not-resolved,informational,client-to-server,0,0x0,192.168.0.0-192.168.255.255,United States,0,text/html

<https://github.com/PaloAltoNetworks/Splunk-App-Data-Generator/blob/master/logs/pan_inc.log>

### Traffic
> Oct 30 09:46:17 1,2012/10/30 09:46:17,01606001116,TRAFFIC,end,1,2012/04/10 04:39:57,192.168.0.2,204.232.231.46,0.0.0.0,0.0.0.0,rule1,crusher,,web-browsing,vsys1,trust,untrust,ethernet1/2,ethernet1/1,forwardAll,2012/04/10 04:39:57,26161,1,59206,80,0,0,0x200000,tcp,allow,1355,549,806,10,2012/04/10 04:39:27,0,private-ip-addresses,0,0,0x0,192.168.0.0-192.168.255.255,United States,0,6,4

<https://github.com/PaloAltoNetworks/Splunk-App-Data-Generator/blob/master/logs/pan_inc.log>

### System
> 

## Parsing/Normalizing

### Grok
Not used as the logs are in CSV format.

### Logstash
```
filter {
  if "paloalto" in [tags] {
    if [type] == "url" {
      grok {
      match => { "message" => "%{MONTH} %{MONTHDAY} %{TIME} %{IPV4:panIP} %{HOSTNAME:firewall}:%{IPV4:sourceIP} %{IPV4:destinationIP} %{NOTSPACE:application} %{NOTSPACE:category} \"%{URIHOST:URIHost}%{URIPATH:URIPath}" }
      }
    date {
      timezone => "UTC"
      match => [ "GenerateTime", "YYYY/MM/dd HH:mm:ss" ]
    }
    if [destinationIP] and [destinationIP] !~ "(^127\.0\.0\.1)|(^10\.)|(^172\.1[6-9]\.)|(^172\.2[0-9]\.)|(^172\.3[0-1]\.)|(^192\.168\.)|(^169\.254\.)" {
      geoip {
           database => "/etc/logstash/GeoLiteCity.dat"
           source => "destinationIP"
           target => "DestinationGeo"
      }
      #Delete 0,0 in DestinationGeo.location if equal to 0,0
      if ([DestinationGeo.location] and [DestinationGeo.location] =~ "0,0") {
        mutate {
          replace => [ "destinationIP.location", "" ]
        }
      }
    }
        mutate {
        remove_field => ["message"]
      }
    }
  if [type] == "traffic" {
    grok {
      #strips timestamp and host off of the front of the syslog message leaving the raw message generated by the syslog client and saves it as "raw_message"
      #patterns_dir => "/opt/logstash/patterns"
      match => { "message" => "%{MONTH} %{MONTHDAY} %{TIME} %{HOSTNAME:syslog_host} %{GREEDYDATA:raw_message}" }
    }
    csv {
      source => "raw_message"
      columns => [ "PaloAltoDomain","ReceiveTime","SerialNum","Type","Threat-ContentType","ConfigVersion","GenerateTime","SourceAddress","DestinationAddress","NATSourceIP","NATDestinationIP","Rule","SourceUser","DestinationUser","Application","VirtualSystem","SourceZone","DestinationZone","InboundInterface","OutboundInterface","LogAction","TimeLogged","SessionID","RepeatCount","SourcePort","DestinationPort","NATSourcePort","NATDestinationPort","Flags","IPProtocol","Action","Bytes","BytesSent","BytesReceived","Packets","StartTime","ElapsedTimeInSec","Category","Padding","seqno","actionflags","SourceCountry","DestinationCountry","cpadding","pkts_sent","pkts_received" ]
    }
    date {
      timezone => "UTC"
      match => [ "GenerateTime", "YYYY/MM/dd HH:mm:ss" ]
    }
    #convert fields to proper format
    mutate {
      convert => [ "Bytes", "integer" ]
      convert => [ "BytesReceived", "integer" ]
      convert => [ "BytesSent", "integer" ]
      convert => [ "ElapsedTimeInSec", "integer" ]
      convert => [ "geoip.area_code", "integer" ]
      convert => [ "geoip.dma_code", "integer" ]
      convert => [ "geoip.latitude", "float" ]
      convert => [ "geoip.longitude", "float" ]
      convert => [ "NATDestinationPort", "integer" ]
      convert => [ "NATSourcePort", "integer" ]
      convert => [ "Packets", "integer" ]
      convert => [ "pkts_received", "integer" ]
      convert => [ "pkts_sent", "integer" ]
      convert => [ "seqno", "integer" ]
      gsub => [ "Rule", " ", "_",
                "Application", "( |-)", "_" ]
      remove_field => [ "message", "raw_message" ]
    }

 #Geolocate logs that have SourceAddress and if that SourceAddress is a non-RFC1918 address
  if [SourceAddress] and [SourceAddress] !~ "(^127\.0\.0\.1)|(^10\.)|(^172\.1[6-9]\.)|(^172\.2[0-9]\.)|(^172\.3[0-1]\.)|(^192\.168\.)|(^169\.254\.)" {
      geoip {
           database => "/etc/logstash/GeoLiteCity.dat"
           source => "SourceAddress"
           target => "SourceGeo"
      }
      #Delete 0,0 in SourceGeo.location if equal to 0,0
      if ([SourceGeo.location] and [SourceGeo.location] =~ "0,0") {
        mutate {
          replace => [ "SourceGeo.location", "" ]
        }
      }
    }

  #Geolocate logs that have DestinationAddress and if that DestinationAddress is a non-RFC1918 address
  if [DestinationAddress] and [DestinationAddress] !~ "(^127\.0\.0\.1)|(^10\.)|(^172\.1[6-9]\.)|(^172\.2[0-9]\.)|(^172\.3[0-1]\.)|(^192\.168\.)|(^169\.254\.)" {
      geoip {
           database => "/etc/logstash/GeoLiteCity.dat"
           source => "DestinationAddress"
  }
      #Delete 0,0 in DestinationGeo.location if equal to 0,0
      if ([DestinationGeo.location] and [DestinationGeo.location] =~ "0,0") {
        mutate {
          replace => [ "DestinationAddress.location", "" ]
        }
      }
    }
  #Takes the 5-tuple of source address, source port, destination address, destination port, and protocol and does a SHA1 hash to fingerprint the flow.  This is a useful
  #way to be able to do top N terms queries on flows, not just on one field.
  if [SourceAddress] and [DestinationAddress] {
    fingerprint {
      concatenate_sources => true
      method => "SHA1"
      key => "logstash"
      source => [ "SourceAddress", "SourcePort", "DestinationAddress", "DestinationPort", "IPProtocol" ]
        }
      }
    }
  }
}
```

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

# Microsoft OMS Agent

The OMS (Operations Management Suite) Agent is used on Azure to send real-time analytics for operational data (Syslog, performance, alerts, inventory) from Linux servers, Docker containers and monitoring tools like Nagios, Zabbix and System Center to [Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/agents/log-analytics-agent).

The logs should be in the standard syslog format.

https://docs.microsoft.com/en-us/azure/azure-monitor/agents/agent-linux-troubleshoot
<https://github.com/KTH/oms-to-elk>

## Log Format
### Message with Type
> DATE TIME [TYPE] MESSAGE

### Message without type
> DATE: MESSAGE

## Log Format
### Message with Type
> 2000-01-01 00:00:00 +0000 [error]: unexpected error error_class=ThreadError error=#<ThreadError: can't create Thread: Resource temporarily unavailable>

### Message without type
> 2000-01-01T00:00:00.0000000Z: warn DataReader.cc:230 tag ‘1’ is not found in backup cache

## Parsing/Normalizing

### Grok
#### Message with Type
> "%{DATE}.*\[%{WORD:[log][level]}]: %{GREEDYDATA:message}"

#### Message without type
> ": %{WORD:type} %{GREEDYDATA:message}"

### Logstash

```ruby

filter {
  grok {
        break_on_match => true
        match => [
                "[event][original]", "%{DATE}.*\[%{WORD:[log][level]}]: %{GREEDYDATA:message}",": %{WORD:type} %{GREEDYDATA:message}"
        ]
  }              
}
```

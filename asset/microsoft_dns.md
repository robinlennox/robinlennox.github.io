# Microsoft DNS 

* Open an elevated Windows PowerShell prompt on the DNS server.
* Run `Set-DnsServerDiagnostics -All $true`
* Run `Set-DnsServerDiagnostics – EnableLogFileRollover $true`
* Valid everything is set to `true` by running `Get-DnsServerDiagnostics`

# Audit events

## DNS Server Audit Events
The source of this information was from [microsoft.com](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn800669(v=ws.11)#audit-events)

| Event ID | Type | Category | Level | Event text |
|--|--|--|--|--|
| 513 | Zone delete | Zone operations | Informational | The zone %1 was deleted. |
| 514 | Zone updated | Zone operations | Informational | The zone %1 was updated. The %2 setting has been set to %3. |
| 515 | Record create | Zone operations | Informational | A resource record of type %1, name %2, TTL %3 and RDATA %5 was created in scope %7 of zone %6. |
| 516 | Record delete | Zone operations | Informational | A resource record of type %1, name %2 and RDATA %5 was deleted from scope %7 of zone %6. |
| 517 | RRSET delete | Zone operations | Informational | All resource records of type %1, name %2 were deleted from scope %4 of zone %3. |
| 518 | Node delete | Zone operations | Informational | All resource records at Node name %1 were deleted from scope %3 of zone %2. |
| 519 | Record create - dynamic update | Dynamic update | Informational | A resource record of type %1, name %2, TTL %3 and RDATA %5 was created in scope %7 of zone %6 via dynamic update from IP Address %8. |
| 520 | Record delete - dynamic update | Dynamic update | Informational | A resource record of type %1, name %2 and RDATA %5 was deleted from scope %7 of zone %6 via dynamic update from IP Address %8. |
| 521 | Record scavenge | Aging | Informational | A resource record of type %1, name %2, TTL %3 and RDATA %5 was scavenged from scope %7 of zone %6. |
| 522 | Zone scope create | Zone operations | Informational | The scope %1 was created in zone %2. |
| 523 | Zone scope delete | Zone operations | Informational | The scope %1 was deleted in zone %2. |
| 525 | Zone sign | Online signing | Informational | The zone %1 was signed with following properties: DenialOfExistence=%2; DistributeTrustAnchor=%3; DnsKeyRecordSetTtl=%4; DSRecordGenerationAlgorithm=%5; DSRecordSetTtl=%6; EnableRfc5011KeyRollover=%7; IsKeyMasterServer=%8; KeyMasterServer=%9; NSec3HashAlgorithm=%10; NSec3Iterations=%11; NSec3OptOut=%12; NSec3RandomSaltLength=%13; NSec3UserSalt=%14; ParentHasSecureDelegation=%15; PropagationTime=%16; SecureDelegationPollingPeriod=%17; SignatureInceptionOffset=%18. |
| 526 | Zone unsign | Online signing | Informational | The zone %1 was unsigned. |
| 527 | Zone re-sign | Online signing | Informational | The zone %1 was re-signed with following properties: DenialOfExistence=%2; DistributeTrustAnchor=%3; DnsKeyRecordSetTtl=%4; DSRecordGenerationAlgorithm=%5; DSRecordSetTtl=%6; EnableRfc5011KeyRollover=%7; IsKeyMasterServer=%8; KeyMasterServer=%9; NSec3HashAlgorithm=%10; NSec3Iterations=%11; NSec3OptOut=%12; NSec3RandomSaltLength=%13; NSec3UserSalt=%14; ParentHasSecureDelegation=%15; PropagationTime=%16; SecureDelegationPollingPeriod=%17; SignatureInceptionOffset=%18. |
| 528 | Key rollover start | DNSSEC operations | Informational | Rollover was started on the type %1 with GUID %2 of zone %3. |
| 529 | Key rollover end | DNSSEC operations | Informational | Rollover was completed on the type %1 with GUID %2 of zone %3. |
| 530 | Key retire | DNSSEC operations | Informational | The type %1 with GUID %2 of zone %3 was marked for retiral. The key will be removed after the rollover completion. |
| 531 | Key rollover triggered | DNSSEC operations | Informational | Manual rollover was triggered on the type %1 with GUID %2 of zone %3. |
| 533 | Key poke rollover | DNSSEC operations | Warning | The keys signing key with GUID %1 on zone %2 that was waiting for a Delegation Signer(DS) update on the parent has been forced to move to rollover completion. |
| 534 | Export DNSSEC | DNSSEC operations | Informational | DNSSEC setting metadata was exported %1 key signing key metadata from zone %2. |
| 535 | Import DNSSEC | DNSSEC operations | Informational | DNSSEC setting metadata was imported on zone %1. |
| 536 | Cache purge | Cache operations | Informational | A record of type %1, QNAME %2 was purged from scope %3 in cache. |
| 537 | Forwarder reset | Configuration | Informational | The forwarder list on scope %2 has been reset to %1. |
| 540 | Root hints | Configuration | Informational | The root hints have been modified. |
| 541 | Server setting | Configuration | Informational | The setting %1 on scope %2 has been set to %3. |
| 542 | Server scope create | Configuration | Informational | The scope %1 of DNS server was created. |
| 543 | Server scope delete | Configuration | Informational | The scope %1 of DNS server was deleted. |
| 544 | Add trust point DNSKEY | DNSSEC operations | Informational | The DNSKEY with Key Protocol %2, Base64 Data %4 and Crypto Algorithm %5 has been added at the trust point %1. |
| 545 | Add trust point DS | DNSSEC operations | Informational | The DS with Key Tag: %2, Digest Type: %3, Digest: %5 and Crypto Algorithm: %6 has been added at the trust point %1. |
| 546 | Remove trust point | DNSSEC operations | Informational | The trust point at %1 of type %2 has been removed. |
| 547 | Add trust point root | DNSSEC operations | Informational | The trust anchor for the root zone has been added. |
| 548 | Restart server | Server operations | Informational | A request to restart the DNS server service has been received. |
| 549 | Clear debug logs | Server operations | Informational | The debug logs have been cleared from %1 on DNS server. |
| 550 | Write dirty zones | Server operations | Informational | The in-memory contents of all the zones on DNS server have been flushed to their respective files. |
| 551 | Clear statistics | Server operations | Informational | All the statistical data for the DNS server has been cleared. |
| 552 | Start scavenging | Server operations | Informational | A resource record scavenging cycle has been started on the DNS Server. |
| 553 | Enlist directory partition | Server operations | Informational | 1% |
| 554 | Abort scavenging | Server operations | Informational | The resource record scavenging cycle has been terminated on the DNS Server. |
| 555 | Prepare for demotion | Server operations | Informational | The DNS server has been prepared for demotion by removing references to it from all zones stored in the Active Directory. |
| 556 | Write root hints | Server operations | Informational | The information about the root hints on the DNS server has been written back to the persistent storage. |
| 557 | Listen address | Server operations | Informational | The addresses on which DNS server will listen has been changed to %1. |
| 558 | Active refresh trust points | DNSSEC operations | Informational | An immediate RFC 5011 active refresh has been scheduled for all trust points. |
| 559 | Pause zone | Zone operations | Informational | The zone %1 is paused. |
| 560 | Resume zone | Zone operations | Informational | The zone %1 is resumed. |
| 561 | Reload zone | Zone operations | Informational | The data for zone %1 has been reloaded from %2. |
| 562 | Refresh zone | Zone operations | Informational | The data for zone %1 has been refreshed from the master server %2. |
| 563 | Expire zone | Zone operations | Informational | The secondary zone %1 has been expired and new data has been requested from the master server %2. |
| 564 | Update from DS | Zone operations | Informational | The zone %1 has been reloaded from the Active Directory. |
| 565 | Write and notify | Zone operations | Informational | The content of the zone %1 has been written to the disk and the notification has been sent to all the notify servers. |
| 566 | Force aging | Zone operations | Informational | All DNS records at the node %1 in the zone %2 will have their aging time stamp set to the current time.%3 |
| 567 | Scavenge servers | Zone operations | Informational | The Active Directory-integrated zone %1 has been updated. Only %2 can run scavenging. |
| 568 | Transfer key master | DNSSEC operations | Informational | The key master role for zone %1 has been %2.%3 |
| 569 | Add SKD | DNSSEC operations | Informational | A %1 singing key (%2) descriptor has been added on the zone %3 with following properties: KeyId=%4; KeyType=%5; CurrentState=%6; KeyStorageProvider=%7; StoreKeysInAD=%8; CryptoAlgorithm=%9; KeyLength=%10; DnsKeySignatureValidityPeriod=%11; DSSignatureValidityPeriod=%12; ZoneSignatureValidityPeriod=%13; InitialRolloverOffset=%14; RolloverPeriod=%15; RolloverType=%16; NextRolloverAction=%17; LastRolloverTime=%18; NextRolloverTime=%19; CurrentRolloverStatus=%20; ActiveKey=%21; StandbyKey=%22; NextKey=%23. The zone will be resigned with the %2 generated with these properties. |
| 570 | Modify SKD | DNSSEC operations | Informational | A %1 singing key (%2) descriptor with GUID %3 has been updated on the zone %4. The properties of this %2 descriptor have been set to: KeyId=%5; KeyType=%6; CurrentState=%7; KeyStorageProvider=%8; StoreKeysInAD=%9; CryptoAlgorithm=%10; KeyLength=%11; DnsKeySignatureValidityPeriod=%12; DSSignatureValidityPeriod=%13; ZoneSignatureValidityPeriod=%14; InitialRolloverOffset=%15; RolloverPeriod=%16; RolloverType=%17; NextRolloverAction=%18; LastRolloverTime=%19; NextRolloverTime=%20; CurrentRolloverStatus=%21; ActiveKey=%22; StandbyKey=%23; NextKey=%24. The zone will be resigned with the %2 generated with these properties. |
| 571 | Delete SKD | DNSSEC operations | Informational | A %1 singing key (%2) descriptor %4 has been removed from the zone %3. |
| 572 | Modify SKD state | DNSSEC operations | Informational | The state of the %1 signing key (%2) %3 has been modified on zone %4. The new active key is %5, standby key is %6 and next key is %7. |
| 573 | Add delegation | Zone operations | Informational | A delegation for %1 in the scope %2 of zone %3 with the name server %4 has been added. |
| 574 | Create client subnet record | Policy operations | Informational | The client subnet record with name %1 value %2 has been added to the client subnet map. |
| 575 | Delete client subnet record | Policy operations | Informational | The client subnet record with name %1 has been deleted from the client subnet map. |
| 576 | Update client subnet record | Policy operations | Informational | The client subnet record with name %1 has been updated from the client subnet map. The new client subnets that it refers to are %2. |
| 577 | Create server level policy | Policy operations | Informational | A server level policy %6 for %1 has been created on server %2 with following properties: ProcessingOrder:%3; Criteria:%4; Action:%5. |
| 578 | Create zone level policy | Policy operations | Informational | A zone level policy %8 for %1 has been created on zone %6 on server %2 with following properties: ProcessingOrder:%3; Criteria:%4; Action:%5; Scopes:%7. |
| 579 | Create forwarding policy | Policy operations | Informational | A forwarding policy %6 has been created on server %2 with following properties: ProcessingOrder:%3; Criteria:%4; Action:%5; Scope:%1. |
| 580 | Delete server level policy | Policy operations | Informational | The server level policy %1 has been deleted from server %2. |
| 581 | delete zone level policy | Policy operations | Informational | The zone level policy %1 has been deleted from zone %3 on server %2. |
| 582 | Delete forwarding policy | Policy operations | Informational | The forwarding policy %1 has been deleted from server %2. |  |  |  |

# Analytic events


## DNS Server Analytic Events
The source of this information was from [microsoft.com](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn800669(v=ws.11)#analytic-events)

| Event ID | Type | Category | Level | Event text |
|--|--|--|--|--|
| 257 | Response success | Lookup | Informational | RESPONSE_SUCCESS: TCP=%1; InterfaceIP=%2; Destination=%3; AA=%4; AD=%5; QNAME=%6; QTYPE=%7; XID=%8; DNSSEC=%9; RCODE=%10; Port=%11; Flags=%12; Scope=%13; Zone=%14; PolicyName=%15; PacketData=%17 |
| 258 | Response failure | Lookup | Error | RESPONSE_FAILURE: TCP=%1; InterfaceIP=%2; Reason=%3; Destination=%4; QNAME=%5; QTYPE=%6; XID=%7; RCODE=%8; Port=%9; Flags=%10; Zone=%11; PolicyName=%12; PacketData=%14 |
| 259 | Ignored query | Lookup | Error | IGNORED_QUERY: TCP=%1; InterfaceIP=%2; Reason=%3; QNAME=%4; QTYPE=%5; XID=%6; Zone=%7; PolicyName=%8 |
| 260 | Query out | Recursive query | Informational | RECURSE_QUERY_OUT: TCP=%1; Destination=%2; InterfaceIP=%3; RD=%4; QNAME=%5; QTYPE=%6; XID=%7; Port=%8; Flags=%9; ServerScope=%10; CacheScope=%11; PolicyName=%12; PacketData=%14 |
| 261 | Response in | Recursive query | Informational | RECURSE_RESPONSE_IN: TCP=%1; Source=%2; InterfaceIP=%3; AA=%4; AD=%5; QNAME=%6; QTYPE=%7; XID=%8; Port=%9; Flags=%10; ServerScope=%11; CacheScope=%12; PacketData=%14 |
| 262 | Recursive query timeout | Recursive query | Error | RECURSE_QUERY_TIMEOUT: TCP=%1; InterfaceIP=%2; Destination=%3; QNAME=%4; QTYPE=%5; XID=%6; Port=%7; Flags=%8; ServerScope=%9; CacheScope=%10 |
| 263 | Update in | Dynamic update | Informational | DYN_UPDATE_RECV: TCP=%1; InterfaceIP=%2; Source=%3; QNAME=%4; XID=%5; Port=%6; Flags=%7; SECURE=%8; PacketData=%10 |
| 264 | Update response | Dynamic update | Informational | DYN_UPDATE_RESPONSE: TCP=%1; InterfaceIP=%2; Destination=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; RCODE=%8; PolicyName=%9; PacketData=%11 |
| 265 | IXFR request out | Zone XFR | Informational | IXFR_REQ_OUT: TCP=%1; InterfaceIP=%2; Source=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; PacketData=%9 |
| 266 | IXFR request in | Zone XFR | Informational | IXFR_REQ_RECV: TCP=%1; InterfaceIP=%2; Source=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; PacketData=%9 |
| 267 | IXFR response out | Zone xfr | Informational | IXFR_RESP_OUT: TCP=%1; InterfaceIP=%2; Destination=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; RCODE=%8; PacketData=%10 |
| 268 | IXFR response in | Zone xfr | Informational | IXFR_RESP_RECV: TCP=%1; InterfaceIP=%2; Destination=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; RCODE=%8; PacketData=%10 |
| 269 | AXFR request out | Zone XFR | Informational | AXFR_REQ_OUT: TCP=%1; Source=%2; InterfaceIP=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; PacketData=%9 |
| 270 | AXFR request in | Zone XFR | Informational | AXFR_REQ_RECV: TCP=%1; Source=%2; InterfaceIP=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; PacketData=%9 |
| 271 | AXFR response out | Zone XFR | Informational | AXFR_RESP_OUT: TCP=%1; InterfaceIP=%2; Destination=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; RCODE=%8 |
| 272 | AXFR response in | Zone XFR | Informational | AXFR_RESP_RECV: TCP=%1; InterfaceIP=%2; Destination=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; RCODE=%8 |
| 273 | XFR notification in | Zone XFR | Informational | XFR_NOTIFY_RECV: Source=%1; InterfaceIP=%2; QNAME=%3; ZoneScope=%4; Zone=%5; PacketData=%7 |
| 274 | XFR notification out | Zone XFR | Informational | XFR_NOTIFY_OUT: Destination=%1; InterfaceIP=%2; QNAME=%3; ZoneScope=%4; Zone=%5; PacketData=%7 |
| 275 | XFR notify ACK in | Zone XFR | Informational | XFR_NOTIFY_ACK_IN: Source=%1; InterfaceIP=%2; PacketData=%4 |
| 276 | XFR notify ACK out | Zone XFR | Informational | XFR_NOTIFY_ACK_OUT: Destination=%1; InterfaceIP=%2; Zone=%3; PacketData=%5 |
| 277 | Update forward | Dynamic update | Informational | DYN_UPDATE_FORWARD: TCP=%1; ForwardInterfaceIP=%2; Destination=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; RCODE=%8; PacketData=%10 |
| 278 | Update response in | Dynamic update | Informational | DYN_UPDATE_RESPONSE_IN: TCP=%1; InterfaceIP=%2; Source=%3; QNAME=%4; XID=%5; ZoneScope=%6; Zone=%7; RCODE=%8; PacketData=%10 |
| 279 | Internal lookup CNAME | Lookup | Informational | INTERNAL_LOOKUP_CNAME: TCP=%1; InterfaceIP=%2; Source=%3; RD=%4; QNAME=%5; QTYPE=%6; Port=%7; Flags=%8; XID=%9; PacketData=%11 |
| 280 | Internal lookup additional | Lookup | Informational | INTERNAL_LOOKUP_ADDITIONAL: TCP=%1; InterfaceIP=%2; Source=%3; RD=%4; QNAME=%5; QTYPE=%6; Port=%7; Flags=%8; XID=%9; PacketData=%11 |  |

# Azure event hub

## Log format
```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```
<https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-diagnostic-logs>

## Parsing/Normalizing

### Grok

#### Resource ID
```The basic format of the resource ID returned by this function is:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```
The scope segment varies by the resource being extended.

When the extension resource is applied to a resource, the resource ID is returned in the following format:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```
When the extension resource is applied to a resource group, the format is:


```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```
When the extension resource is applied to a subscription, the format is:


```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```
When the extension resource is applied to a management group, the format is:


```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}```
```

<https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/template-functions-resource>

Information on ingesting resource ids into elastic can be found at: <https://github.com/elastic/integrations/blob/03d83099135c4e2200a64253bcaf1c7b8441df3e/packages/azure/data_stream/activitylogs/elasticsearch/ingest_pipeline/azure-shared-pipeline.yml>

## Field Mapping

| Log Type | Azure Field         | ECS Field                              | Non-standard field               |
|----------|----------------------|----------------------------------------|----------------------------------|
| azure  | /subscriptions/{subscriptionId}         |                           | azure.subscription_id                                 |
| azure  | azure.correlation_id        |                  | azure.correlation_id                                 | 
| azure  | azure.tenant_id                 |                         | azure.tenant_id                                  | 
| resource  | azure.resource_id              |                            | azure.resource.id                                |
| resource  | /resourceGroups/{resourceGroupName}              |                            | azure.resource.group                                 |
| resource  | /providers/{extensionResourceProviderNamespace}       |                              | azure.resource.provider                                |
| resource  | /namespaces/{extensionResourceName}            |                     | azure.resource.namespace                                 |
| resource  | /providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}       |                | azure.resource.name                                 |
| resource  | /authorizationrules/{authorizationRules}        |                                        | azure.resource.authorization_rule         |

The source of this information was from [elastic.co](https://www.elastic.co/guide/en/beats/filebeat/current/exported-fields-azure.html)
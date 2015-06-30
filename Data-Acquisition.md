Data Acquisition Service (DAS) is responsible for fetching and processing external data sources as well as exposing the required data for consumption in PaaS through its data catalog.

At its basic implementation, the DAS process can be divided into four independent steps:

* **Fetch:** Download of queued data source requests. 
* **Process:** Postprocess of downloaded files to extract metadata.
* **Store:** Save metadata and raw object of the fetched content. 
* **Query:** Search and delivery of data objects. 

This is an outline of the data acquisition functionality. The DAS repository can be found at [https://github.com/trustedanalytics/data-acquisition](https://github.com/trustedanalytics/data-acquisition).

![](DAS/Das.png)

## Kafka
All internal communication in DAS will use Kafka.

**Kafka topics:**
* **Statuses:** Topic for the request’s status updates. This is the only topic that can't afford to lose messages. It will have “at least once” delivery semantics. Also, for simplification reasons, it will have only one partition. This limits active status manager instances to one at a time.
* **Validation:** Topic for requests that will be processed by the validation/requests parser.
* **Downloader:** Topic for requests that will be processed by Downloader uService.
* **Metaparser:** Topic for requests that will be processed by MetaParser uService.


#### Messages flow
![](DAS/downloader.png)

**Description**
* **Kafka client:** Communication API with Kafka. Consuming messages – looks like high level consumer will do: https://cwiki.apache.org/confluence/display/KAFKA/Consumer+Group+Example. Thanks to idempotent services and Status manager, we don’t need to worry about request retransmissions/request loss.
* **Downloader client:** REST client for Downloader uService.
* **Status manager:** Updating statuses: Status updates will be pulled from a specific Kafka topic and updated in the Request Store. Note: If that topic has more than one partition, status updates may be reordered, so we need a mechanism that will prevent that (something like Request lifecycle that prevents progress moving backwards, timestamps, or our own partitioner in Kafka producer).
* **Stale/missing requests manager:** All requests in the Request Store will have a timestamp of the last status change. Long-running operations will send heartbeats. (For example, Downloader may piggyback download progress information over heartbeats.) Because of this, we can find out about stale/missing requests and reschedule them. Note: There may be more than one active instance at a time. RequestStore should provide some locking functionality (may be optimistic). 

#### Idempotence and atomicity of uServices 
uServices should be atomic and idempotent. Because of this, they could be invoked as many times as needed to obtain the desired results. 

#### Request’s status lifecycle
Status should have some kind of lifecycle. It could be implemented as FSM.
![](DAS/status_fsm.png)




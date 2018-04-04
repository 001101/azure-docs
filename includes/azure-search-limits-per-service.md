Storage is constrained by disk space or by a hard limit on the *maximum number* of indexes, document, or other high-level resources, whichever comes first. 

The following table documents storage limits. For maximum limits on indexes, documents, and other objects, see [Services limits (Azure Search)](../articles/search/search-limits-quotas-capacity.md/).

| Resource | Free | Basic <sup>1</sup> | S1 | S2 | S3 | S3 HD <sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Service Level Agreement (SLA) <sup>3</sup>  |No |Yes |Yes |Yes |Yes |Yes |
| Storage per partition |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partitions per service |N/A |1 |12 |12 |12 |3 |
| Partition size |N/A |2 GB |25 GB |100 GB |200 GB |200 GB |
| Replicas |N/A |3 |12 |12 |12 |12 |

<sup>1</sup> Basic has one fixed partition. At this tier, additional SUs are used for allocating more replicas for increased query workloads.

<sup>2</sup> S3 HD has a hard limit of 3 partitions, which is lower than the partition limit for S3. The lower partition limit is imposed because the index count for S3 HD is substantially higher. Given that service limits exist for both computing resources (storage and processing) and content (indexes and documents), the content limit is reached first.

<sup>3</sup> Service level agreements (SLAs) are offered for billable services on dedicated resources. Free services and preview features have no SLA. For billable services, SLAs take effect when you provision sufficient redundancy for your service. Two or more replicas are required for query (read) SLA. Three or more replicas are required for query and indexing (read-write) SLA. The number of partitions is not an SLA consideration. 
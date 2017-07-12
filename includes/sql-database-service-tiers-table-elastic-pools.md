<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->
 
### Basic elastic pool limits

| Pool size (eDTUs)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Storage included per pool* | 5 GB | 10 GB | 20 GB | 29 GB | 39 GB | 78 GB | 117 GB | 156 GB |
| Storage max per pool* | 5 GB | 10 GB | 20 GB | 29 GB | 39 GB | 78 GB | 117 GB | 156 GB |
| Max In-Memory OLTP storage per pool | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| Max number DBs per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Max concurrent workers (requests) per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Max concurrent logins per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Min eDTUs per database | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Max eDTUs per database | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Storage max per database* | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 2 GB | 
||||||||

### Standard elastic pool limits

| Pool size (eDTUs)  | **50** | **100** | **200**** | **300**** | **400**** | **800****| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Storage included per pool* | 50 GB | 100 GB| 200 GB | 300 GB| 400 GB | 800 GB | 
| Storage max per pool* | 500 GB| 750 GB| 1 TB | 1.25 TB | 1.5 TB | 2 TB | 
| Max In-Memory OLTP storage per pool | N/A | N/A | N/A | N/A | N/A | N/A | 
| Max number DBs per pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Max concurrent workers (requests) per pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Max concurrent logins per pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Min eDTUs per database** | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Max eDTUs per database** | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Storage max per database* | 500 GB | 750 GB | 1 TB | 1 TB | 1 TB | 1 TB |
||||||||

### Standard elastic pool limits (continued) 

| Pool size (eDTUs)  |  **1200**** | **1600**** | **2000**** | **2500**** | **3000**** |
|:---|---:|---:|---:| ---: | ---: |
| Storage included per pool* | 1.2 TB | 1.6 TB | 2 TB | 2.4 TB | 2.9 TB | 
| Storage max per pool* | 2.5 TB | 3 TB | 3.5 TB | 4 TB | 4 TB |
| Max In-Memory OLTP storage per pool | N/A | N/A | N/A | N/A | N/A | 
| Max number DBs per pool | 500 | 500 | 500 | 500 | 500 | 
| Max concurrent workers (requests) per pool |  2400 | 3200 | 4000 | 5000 | 6000 |
| Max concurrent logins per pool |  2400 | 3200 | 4000 | 5000 | 6000 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Min eDTUs per database** | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max eDTUs per database** | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Storage max per database* | 1 TB | 1 TB | 1 TB | 1 TB | 1 TB | 
||||||||

### Premium elastic pool limits

| Pool size (eDTUs)  | **125** | **250** | **500** | **1000** | **1500*****| 
|:---|---:|---:|---:| ---: | ---: | 
| Storage included per pool* | 250 GB | 500 GB | 750 GB | 1 TB | 1.5 TB | 
| Storage max per pool* | 1 TB | 1 TB | 1 TB | 1 TB | 1.5 TB |
| Max In-Memory OLTP storage per pool | 1 GB| 2 GB| 4 GB| 10 GB| 12 GB| 
| Max number DBs per pool | 50 | 100 | 100 | 100 | 100 |  
| Max concurrent workers per pool (requests) | 200 | 400 | 800 | 1600 |  2400 | 
| Max concurrent logins per pool | 200 | 400 | 800 | 1600 |  2400 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Min eDTUs per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Max eDTUs per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Storage max per database* | 1 TB | 1 TB | 1 TB | 1 TB | 1 TB | 
||||||||

### Premium elastic pool limits (continued) 

| Pool size (eDTUs) | **2000***** | **2500***** | **3000***** | **3500***** | **4000*****|
|:---|---:|---:|---:| ---: | ---: | 
| Storage included per pool* | 2 TB | 2.5 TB | 3 TB | 3.5 TB | 4 TB |
| Storage max per pool* | 2 TB | 2.5 TB | 3 TB | 3.5 TB | 4 TB |
| Max In-Memory OLTP storage per pool | 16 GB | 20 GB | 24 GB | 28 GB | 32 GB |
| Max number DBs per pool | 100 | 100 | 100 | 100 | 100 | 
| Max concurrent workers (requests) per pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Max concurrent logins per pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Min eDTUs per database | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 |  0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Max eDTUs per database | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Storage max per database* | 1 TB | 1 TB | 1 TB | 1 TB | 1 TB | 
||||||||

### Premium RS elastic pool limits

| Pool size (eDTUs)  | **125** | **250** | **500** | **1000** |
|:---|---:|---:|---:| ---: | ---: | 
| Storage included per pool* | 250 GB| 500 GB | 750 GB | 750 GB |
| Storage max per pool* | 1 TB | 1 TB | 1 TB | 1 TB | 
| Max In-Memory OLTP storage per pool | 1 GB | 2 GB | 4 GB | 10 GB |
| Max number DBs per pool | 50 | 100 | 100 | 100 |
| Max concurrent workers (requests) per pool | 200 | 400 | 800 | 1600 |
| Max concurrent logins per pool | 200 | 400 | 800 | 1600 |
| Max concurrent sessions per pool | 30000 | 30000 | 30000 | 30000 |
| Min eDTUs per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 |
| Max eDTUs per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 
| Storage max per database* | 1 TB | 1 TB | 1 TB | 1 TB | 
||||||||

> [!IMPORTANT]
>\* Storage limits only refer to the data in the database. Pooled databases share pool storage, so data storage in an elastic pool is limited to the smaller of the remaining pool storage or max storage per database. Storage max sizes per pool greater than the storage included per pool are in preview.
>
>
>\*\* Min/max eDTUs per database starting at 200 eDTUs and higher is in public preview.
>
>\*\*\* Premium pools with more than 1 TB of storage is currently in public preview in the following regions: US East2, West US, US Gov Virginia, West Europe, Germany Central, South East Asia, Japan East, Australia East, Canada Central, and Canada East. The max storage per pool for all other regions is currently limited to 1 TB.
>

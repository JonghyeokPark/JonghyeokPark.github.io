---
title: "NV-SQL: Boosting OLTP Performance with Non-Volatile DIMMs"
collection: publications
permalink: /publication/0-nvsql-vldb23
excerpt: 'When running OLTP workloads, relational DBMSs with flash SSDs still suffer from the durability overhead. Heavy writes to SSD not only limit the performance but also shorten the storage lifespan. 
To mitigate the durability overhead, this paper proposes a new database architecture, NV-SQL. NV-SQL aims at absorbing a large fraction of writes written from DRAM to SSD by introducing NVDIMM into the memory hierarchy as a durable write cache. 
On the new architecture, NV-SQL makes two technical contributions. 
First, it proposes the re-update interval-based admission policy that determines which write-hot pages qualify for being cached in NVDIMM.
It is novel in that the page hotness is based solely on pages’ LSN.
Second, this study finds that NVDIMM-resident pages can violate the page action consistency upon crash and proposes how to detect inconsistent pages using per-page in-update flag and how to rectify them using the redo log. 
NV-SQL demonstrates how the ARIES-like logging and recovery techniques can be elegantly extended to support the caching and recovery for NVDIMM data.
Additionally, by placing write-intensive redo buffer and DWB in NVDIMM, NV-SQL eliminates the log-force-at-commit and WAL protocols and further halves the writes to the storage. 
Our NV-SQL prototype running with a real NVDIMM device outperforms the same-priced vanilla MySQL with larger DRAM by several folds in terms of transaction throughput for write-intensive OLTP benchmarks. This confirms that NV-SQL is a cost-performance efficient solution to the durability problem.'
date: 2023-03-01
venue: 'Proceeding of Very Large Database 2023'
paperurl: 'https://www.vldb.org/pvldb/vol16/p1453-lee.pdf'
citation: 'Mijin An, Jonghyek Park, Tianzheng Wang, Bomeseok Nam, Sang-Won Lee, NV-SQL: Boosting OLTP Performance with Non-Volatile DIMMs'
---
When running OLTP workloads, relational DBMSs with flash SSDs still suffer from the durability overhead. Heavy writes to SSD not only limit the performance but also shorten the storage lifespan. 
To mitigate the durability overhead, this paper proposes a new database architecture, NV-SQL. NV-SQL aims at absorbing a large fraction of writes written from DRAM to SSD by introducing NVDIMM into the memory hierarchy as a durable write cache. 
On the new architecture, NV-SQL makes two technical contributions. 
First, it proposes the re-update interval-based admission policy that determines which write-hot pages qualify for being cached in NVDIMM.
It is novel in that the page hotness is based solely on pages’ LSN.
Second, this study finds that NVDIMM-resident pages can violate the page action consistency upon crash and proposes how to detect inconsistent pages using per-page in-update flag and how to rectify them using the redo log. 
NV-SQL demonstrates how the ARIES-like logging and recovery techniques can be elegantly extended to support the caching and recovery for NVDIMM data.
Additionally, by placing write-intensive redo buffer and DWB in NVDIMM, NV-SQL eliminates the log-force-at-commit and WAL protocols and further halves the writes to the storage. 
Our NV-SQL prototype running with a real NVDIMM device outperforms the same-priced vanilla MySQL with larger DRAM by several folds in terms of transaction throughput for write-intensive OLTP benchmarks. This confirms that NV-SQL is a cost-performance efficient solution to the durability problem.

[Download paper here](http://academicpages.github.io/files/paper1.pdf)

Recommended citation: Mijin An, Jonghyek Park, Tianzheng Wang, Bomeseok Nam, Sang-Won Lee. NV-SQL: Boosting OLTP Performance with Non-Volatile DIMMs.
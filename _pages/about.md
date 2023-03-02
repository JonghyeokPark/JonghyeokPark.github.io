---
permalink: /
title: "Overview"
excerpt: "About me"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

- Jong-Hyeok Park received the B.S. degree in software from Sungkyunkwan University, Suwon, Korea, in 2016, where he is currently pursuing the Ph.D. degree with the Department of Software. 
His research interests include Flash-based database technology and Persistent Memory DBMS and computational storages.

Contact
======
Office: HUFS Eng Build. 511 (공학관 511호)

Email: jonghyeok.park@hufs.ac.kr

Tel: 031-290-????


Research #1. NVM-based Database Engine Optimizations
======
When running OLTP workloads, relational DBMSs with flash SSDs still suffer from the durability overhead. Heavy writes to SSD not only limit the performance but also shorten the storage lifespan. 
To mitigate the durability overhead, this paper proposes a new database architecture, NV-SQL. NV-SQL aims at absorbing a large fraction of writes written from DRAM to SSD by introducing NVDIMM into the memory hierarchy as a durable write cache. 
On the new architecture, NV-SQL makes two technical contributions. 
First, it proposes the re-update interval-based admission policy that determines which write-hot pages qualify for being cached in NVDIMM.
It is novel in that the page hotness is based solely on pages’ LSN.
Second, this study finds that NVDIMM-resident pages can violate the page action consistency upon crash and proposes how to detect inconsistent pages using per-page in-update flag and how to rectify them using the redo log. 
NV-SQL demonstrates how the ARIES-like logging and recovery techniques can be elegantly extended to support the caching and recovery for NVDIMM data.
Additionally, by placing write-intensive redo buffer and DWB in NVDIMM, NV-SQL eliminates the log-force-at-commit and WAL protocols and further halves the writes to the storage. 
Our NV-SQL prototype running with a real NVDIMM device outperforms the same-priced vanilla MySQL with larger DRAM by several folds in terms of transaction throughput for write-intensive OLTP benchmarks. This confirms that NV-SQL is a cost-performance efficient solution to the durability problem.

Research #2. SSD as SQL Database Engine
======
Every database engine runs on top of an operating system in the host, strictly separated with the storage. This more-than-half-century-old IHDE (In-Host-Database-Engine) architecture, however, reveals its limitations when run on fast flash memory SSDs. In particular, the IO stacks incur significant run-time overhead and also hinder vertical optimizations between database engines and SSDs. In this paper, we envisage a new database architecture, called SaS (SSD as SQL database engine), where a full-blown SQL database engine runs inside SSD, tightly integrated with SSD architecture without intervening kernel stacks. As IO stacks are removed, SaS is free from their run-time overhead and further can explore numerous vertical optimizations between database engine and SSD. SaS evolves SSD from dummy block device to database server with SQL as its primary interface. The benefit of SaS will be more outstanding in the data centers where the distance between database engine and the storage is ever widening because of virtualization, storage disaggregation, and open software stacks. The advent of computational SSDs with more compute resource will enable SaS to be more viable and attractive database architecture.


Research #3. Flash-based Database Engine Optimizations
======
For a write request, today flash storage cannot distinguish the logical object it comes from. In such object-oblivious flash devices, concurrent writes from different objects are simply packed in their arrival order to flash memory blocks; hence objects with different lifetimes are multiplexed onto the same flash blocks. This multiplexing incurs write amplification, worsening the performance. Tackling the multiplexing problem, we propose a novel interface for flash storage, FlashAlloc. It is used to pass the logical address ranges of logical objects to the flash storage and thus enlighten the storage to stream writes by objects. The object-aware flash storage can de-multiplex writes from different objects with distinct deathtimes into per-object dedicated flash blocks. Given that popular data stores separate writes using objects (e.g., SSTables in RocksDB), we can achieve, unlike the existing solutions, transparent write streaming just by calling FlashAlloc upon object creation. Our experimental results using an open-source SSD prototype demonstrate that FlashAlloc can reduce write amplification factor (WAF) in RocksDB, F2FS, and MySQL by 1.5, 2.5, and 0.3, respectively and thus improve throughput by 2x, 1.8x, and 1.2x, respectively. In particular, FlashAlloc will mitigate the interference among multitenants. When RocksDB and MySQL were run together on the same SSD, FlashAlloc decreased WAF from 4.2 to 2.5 and doubled their throughputs.


News
======
- Our paper "NV-SQL: Boosting OLTP Performance with Non-Volatile DIMMs" was accepted by VLDB 2023


Recent Papers
======
NV-SQL: Boosting OLTP Performance with Non-Volatile DIMMs. (to appear)
Mijin An, Jonghyeok Park, Tianzheng Wang, Beomseok Nam and Sang-Won Lee.
[VLDB 2023](https://vldb.org/2023/)

[SaS: SSD as SQL Database System](http://vldb.org/pvldb/vol14/p1481-lee.pdf)
Jong-Hyeok Park, Soyee Choi, Gihwan Oh, and Sang-Won Lee.
[VLDB 2021](https://vldb.org/2021/)

[SQL Statement Logging for Making SQLite Truly Lite](http://www.vldb.org/pvldb/vol11/p513-park.pdf)
Jong-Hyeok Park, Gihwan Oh, and Sang-Won Lee.
[VLDB 2021](https://vldb.org/2021/)

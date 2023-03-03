---
title: "SQL Statement Logging for Making SQLite Truly Lite"
collection: publications
permalink: /publication/2-ssl-vldb18
excerpt: 'xxx'
date: 20217-12-01
venue: 'Proceedings of the VLDB Endowment'
paperurl: 'http://www.vldb.org/pvldb/vol11/p513-park.pdf'
citation: 'Jong-Hyeok Park, Gihwan Oh, and Sang-Won Lee. 2017. SQL statement logging for making SQLite truly lite'
---
The lightweight codebase of SQLite was helpful in making it become the de-facto standard database in most mobile devices, but, at the same time, forced it to take less-complicated transactional schemes, such as physical page logging, journaling, and force commit, which in turn cause excessive write amplification. Thus, the write IO cost in SQLite is not lightweight at all.
In this paper, to make SQLite truly lite in terms of IO efficiency for the transactional support, we propose SQLite/SSL, a per-transaction SQL statement logging scheme: when a transaction commits, SQLite/SSL ensures its durability by storing only SQL statements of small size, thus writing less and performing faster at no compromise of transactional solidity. Our main contribution is to show that, based on the observation that mobile transactions tend to be short and exhibit strong update locality, logical logging can, though long discarded, become an elegant and perfect fit for SQLite-based mobile applications. Further, we leverage the WAL journal mode in vanilla SQLite as a transaction-consistent checkpoint mechanism which is indispensable in any logical logging scheme. In addition, we show for the first time that byte-addressable NVM (non-volatile memory) in host-side can realize the full potential of logical logging because it allows to store fine-grained logs quickly.
We have prototyped SQLite/SSL by augmenting vanilla SQLite with a transaction-consistent checkpoint mechanism and a redo-only recovery logic, and have evaluated its performance using a set of synthetic and real workloads. When a real NVM board is used as its log device, SQLite/SSL can outperform vanilla SQLite's WAL mode by up to 300x and also outperform the state-of-the-arts SQLite/PPL scheme by several folds in terms of IO time.

[Download paper here](http://www.vldb.org/pvldb/vol11/p513-park.pdf)

Recommended citation: Jong-Hyeok Park, Gihwan Oh, and Sang-Won Lee. 2017. SQL statement logging for making SQLite truly lite. Proc. VLDB Endow. 11, 4 (December 2017), 513–525. https://doi.org/10.1145/3186728.3164146
---
layout: post
title:  "Build zero and zapps"
date:   2020-06-08 08:15:36 +09:00
categories: zero zapps Shore-MT 
---

이번 포스트에서는 오픈소스 데이터베이스 엔진, 프토토타입용 트랜잭션 저장 관리자인 `zero`와 TPC-C 등의 벤치마크를 수행할 수 있는 `zapps`를 빌드 및 실행하는 방법에 대해 소개한다. 
`zero`는 오픈 소스 프로젝트인 `shore-MT`에서 포크된 버전으로, `Foster B+Tree` 기반으로 동작하며, ACID 트랜잭션을 지원한다. 
`shore-MT`는 multi-core CPU 상에서의 확장성에 초점을 맞춘 반면, 


## Build

### dependencies
```
sudo apt-get install git cmake build-essential
sudo apt-get install libboost-dev libboost-thread-dev libboost-program-options-dev \
                     libboost-random-dev libboost-filesystem-dev
```


### How to compile
```
git clone https://github.com/caetanosauer/zero
cd zero
mkdir build && cd build

cmake .. 
```

Alternatively, a debug version without optimizations is also supported:

```
cmake -DCMAKE_BUILD_TYPE=Debug ..
```

Build libsm and zapps

```
# Target bulid library  (static storage manager library) libsm
make -j <# of cores> sm

# To use benchmark (TPC-C)
make -j <# of cores> zapps

```


### How to Use

### Options
```
--b , --benchmark: Benchmark to execute
--t , --threads : # of threads
--duration : Run benchmark for the given number of second

... 

See, /src/cmd/kits/kits_cmd.cpp and src/cmd/base/command.cpp

```


### TPC-C
```
src/cmd/zapps kits -b tpcc --load --t 32 --duration 60
```

### YCSB
```$xslt
src/cmd/zapps kits -b ycsb --load --t 32 --duration 60
```
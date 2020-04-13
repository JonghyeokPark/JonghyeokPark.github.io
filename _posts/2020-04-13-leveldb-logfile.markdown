---
layout: post
title:  "How to read LOG file in LevelDB"
date:   2020-04-13 22:15:36 +09:00
categories: LevelDB
---

이번 포스트에서는 LevelDB에 있는 로그파일을 해석하는 방법에 대해 다룬다.
로그 파일을 보면, LevelDB가 데이터를 어떻게 관리하는지 추적할 수 있다 따라서, LevelDB의 동작과정을 이해하는 데 큰 도움이 된다.
이번에는 LevelDB의 다양한 동작과정 중, ```Start up``` , ```Log rotation```, 그리고 ```Compaction``` 동작 과정에 대한 로그파일을 해석하는 방법에 대해 다룬다.
이번 블로그는 SenX의 기술블로그 포스트인 ["Demystifying LevelDB"](https://blog.senx.io/demystifying-leveldb/)의 내용과 예시를 참고하여 작성하였다.

## Overview
* LevelDB는 key-value 데이터를 SST (Sorted String Table) 파일에 저장한다. SST 파일은 key-value 쌍의 정렬된 리스트임.
SST 파일은 immutable 즉, 값이 더이상 바뀌지 않으며, 각 SST 파일은 level 마다 특정한 갯수만큼 존재함.
* LevelDB 최신 버전에서는 SST 파일의 확장자 이름이 ```.sst``` 가 아닌, ```.ldb```로 [변경](https://groups.google.com/forum/#!topic/leveldb/u9izbG-pDis)됨. 
* LevelDB에 데이터를 작성하면 바로 데이터베이스 ```.ldb``` 파일에 쓰여지는 것이 아니라, 새로운 key-value pair를 생성하여 ```memtable```에 작성하기 전
 ```.log``` 파일에 작성함. 
* ```memtable```의 용량이 threshold에 도달하면 (예: 4MB), ```.log``` 파일에 있는 내용을  새로운 SST 파일에 쓰고, 새로운 로그 파일을 생성, 그리고 이전에 있던 memtable은 없어짐. 
새로 생성되는 SST 파일은 level 0 에 위치하며, level 0는 유일하게 key 값의 중복을 허용함.
* level 0 에 있는 파일 갯수가 특정 갯수 (예: 10개)에 도달하면, compaction 이 동작함. 
```compaction```은 level 0에 있는 key-value 쌍 중 중복된 키를 ```merge``` 하여 level 1에 새로운 파일을 만들고 쓰기를 수행함. 
기존에 level 1에 있는 중복된 파일은 삭제함.
* LevelDB는 각 레벨에 있는 파일들의 갯수 threshold를 확인하고, `compaction` 을 수행함.
LevelDB는 기본적으로 총 7개의 level을 관리하며, `sst` 파일 목록은  `MANIFEST` 파일에 저장하며, 현재 `MANIFEST` 파일은 `CURRENT` 파일에 저장됨.
* 데이터를 읽을 때 먼저 `MANIEST` 파일에서 읽어야하는 파일셋을 검색하고, 필요한 파일을 열고, 현재 `memtable` 에서 읽을 데이터를 조정하여 덮어 쓰기 및 삭제를 관리함.
* `.ldb`  파일과 `.log`파일, 그리고 `MANIFEST` 파일에 대한 업데이트들은 모두 `LOG` 파일에 기록됨.
 

## LevelDB start up
- LevelDB database file이 열리면, `LOG` 파일이 복구되어 `.ldb` 파일로 변환되며, 새로운 `LOG` 파일이 초기화됨.
현재 존재하는 모든 `SST` 파일들에 대한 정보가 포함된 새로운 `MANIFEST` 파일을 작성함. `.ldb` 파일들을 스캔하면서 참조되지 않은 불필요한 파일들을 삭제함.
- LevelDB는 데이터베이스를 오픈할 때 마다 반드시 복구 함수를 호출함. 
- 예시 : LevelDB database directory

```bash
CURRENT
LOG
MANIFEST-000839
... 
000837.ldb
000841.log
000842.ldb
```

- 예시 : Recovery 동작 후 LOG 파일 상태

```
2019/09/05-09:54:01.235885 7000045d0000 Recovering log  #841
2019/09/05-09:54:01.237443 7000045d0000 Delete type=0   #841
2019/09/05-09:54:01.237509 7000045d0000 Delete type=3   #839
```

- `type = 0` 은 해당 SST 파일 (여기서는, `000841.ldb` 삭제를 의미함)
- `type = 3` 은 Manifest 파일 삭제되고, 해당 버전 (숫자)의 MANIFEST 파일이 생성됨을 의미함. (`MANIFEST-843`)

```
000837.sst
000842.sst
LOG.old
CURRENT
000844.log
MANIFEST-000843
LOG
```


## Log rotation 
- `.log` 파일에 로그 데이터를 넣다가 특정 한계가 도달하면, LevelDB에서 log를 rotate함. log rotation이 발생할 때, LevelBD LOG 파일에서 다음과 같은 entry를 확인할 수 있음.

```
2019/09/05-10:00:07.976574 7000043c7000 Level-0 table #846: started
2019/09/05-10:00:08.022811 7000043c7000 Level-0 table #846: 3264502 bytes OK
2019/09/05-10:00:08.024013 7000043c7000 Delete type=0 #844
```

- line#1 : 새로운 sst 파일인 `0000846.sst` 는 level-0에 새롭게 초기화 되었음을 의미함.
- line#2 : 3,264,502 byte 에 해당하는 로그가 로그파일에서 level-0로 전송이 되었음을 의미함.
- line#3 : 새로운 sst파일 `0000846.sst` 가 생성되기 전에 새로운 로그 파일이 생성됨 (`0000845.log` )


## Compaction 

- level 0에 있는 파일 갯수가 한계점에 도달하면, `compaction` 이 수행됨. level 0에 있는 파일들을 골라서 level 1에 있는 파일과 `merge`를 수행한 다음, level 1에 새로운 sst파일을 생성함.
- (예시) compaction 수행시 로그 파일

```
2019/09/05-10:12:29.932735 7000043c7000 Compacting 3@0 + 5@1 files
2019/09/05-10:12:29.935664 7000043c7000 Generated table #855: 1 keys, 299 bytes
2019/09/05-10:12:29.953873 7000043c7000 Level-0 table #858: started
2019/09/05-10:12:29.971680 7000043c7000 Level-0 table #858: 1836221 bytes OK
2019/09/05-10:12:29.973850 7000043c7000 Delete type=0 #853
2019/09/05-10:12:30.004067 7000043c7000 Generated table #856: 181829 keys, 2120146 bytes
2019/09/05-10:12:30.063737 7000043c7000 Generated table #859: 181828 keys, 2120137 bytes
2019/09/05-10:12:30.117959 7000043c7000 Generated table #860: 181825 keys, 2120084 bytes
2019/09/05-10:12:30.137805 7000043c7000 Generated table #861: 58498 keys, 682097 bytes
2019/09/05-10:12:30.238360 7000043c7000 Generated table #862: 13 keys, 153 bytes
2019/09/05-10:12:30.238379 7000043c7000 Compacted 3@0 + 5@1 files => 7042916 bytes
2019/09/05-10:12:30.238592 7000043c7000 compacted to: files[ 2 8 56 69 0 0 0 ]
```
       
- compaction은 `level 0`에 위치한 3개의 파일과 (`3@0`)  `level 1` 에 위치한 5개의 파일에서 발생함 (`5@1`)
- 해당 compaction 수행 결과, 5개의 새로운 파일이 생성됨 (`856` , `859` , `860` ,`861` , `862`)
- 로그파일에서 해당 파일마다 key가 몇 개 있는지, 몇 byte인지 확인할 수 있음. 따라서 footprint 확인 가능
- 로그파일의 마지막 2줄은 compaction 수행 후 결과 요약을 의미함.
    - 새롭게 생성된 sst file size  그리고 각 레벨에 있는 파일들의 갯수를 알려줌.
    - `[2 , 8 , 56 , 69 , 0 , 0 , 0 ]` : `level 0` 에는 2개, `level 1` 에는 8개, `level 2` 에는 56개, `level 3`  에는 69개가 있음을 알려줌.

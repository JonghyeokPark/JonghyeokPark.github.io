---
layout: post
title:  "[Paper Review] The Periodic Table of Data Structures"
date:   2020-04-30 08:15:36 +09:00
categories: Paper 
---

이번 포스트에서는 최근 시스템 아키텍처의 변화와 데이터 자료구조에 대해 다룬 논문에 대해 정리하려고 한다.
논문은 "The Periodic Table of Data Structures, (Stratos Idreos Kostas et.al)", 
"Bulletin of the IEEE Computer Society Technical Committee on Data Engineering"에 출판되었다.
논문은 [여기](https://stratos.seas.harvard.edu/publications/periodic-table-data-structures) 확인할 수 있다.

## Summary

- **Problem definition :** 모든 조건에서 완벽한 자료구조는 존재 하지 않으며, 워크로드 특성과 실험 환경에 최적화된 자료구조를 선택하는 것이 중요함. 
하지만, 다양한 워크로드 환경의 변화, 새로운 하드웨어의 등장 등의 이유로 매번 설계 및 구현해서 성능을 확인하는 것은 힘듦. 그리고,  
설계가 가능한 자료구조 (기존의 design concept들의 조합 등)을 모두 확인할 수 있는 좋은 방법이 아직 존재하지 않음.

- **Goals:** 마치 화학 원소 주기율표를 보고 각각의 특성을 확인하는 것처럼, 자료구조의 더이상 쪼갤 수 없는 `First Principle` 를 정의하고, Data sturucture Design space를 완성하여, 자동으로 자료구조를 설계할 수 있도록 함.
    1. `Periodic Table of Data sturecure` 설계 ⇒ researcher/engineer에게 직접 구현하지 않고 다양한 자료구조를 자동적으로 설계할 수 있도록 함.
    2. (궁극적 목표) `self-driving system` 설계 ⇒ machine learning을 통해, 자료구조의 cost model을 학습을 통해 runtime중 self-desiging 시스템을 설계하는 것

### Observations

- 모든 조건에 완벽한 자료구조는 존재하지 않음.
- 자료구조의 중요성 : data-driven application 증가, data analysis가 더욱 중요해짐, 하드웨어 가속화로 인해 `computation` 속도가  `data movement` 속도보다 빠르다. 따라서, 자료구조의 중요성이 높아짐.
- 워크로드 특성의 변화, 새로운 하드웨어의 등장으로 자료구조를 설계하기 힘듦.

- 각각의 자료구조는 `design concept`의 집합으로 구성됨 (예: partioning , pointers, direct addressing)
- 새로운 자료구조는 다음의 세가지로 분류됨. (1,2 가 대부분임)
    1. 기 존재하는 design concept들의 새로운 조합
    2. 기 존재하는 design concept에 대해 새로운 튜닝을 적용함.
    3. 새로운 design concept

### Design space of Data Structure

- `First principle` : 자료구조에서 더이상 쪼갤 수 없는 design concept을 의미함. (예: 포인터로 두 노드를 연결하는 자료구조에서, 포인터의 크기, 포인터 구조 등이 아닌 단순히 "포인터인지 아닌지")
- `Layout` 우선 : data layout (i.e., first principle들의 집합) 을 결정하면, access 패턴은 자동으로 layout형태에 따라 결정된다고 가정함.
- Layout을 결정하면, 자료구조 모델은 `logical block` 으로 정의할 수 있음; `logical block` 은 자료구조를 정의하는 노드의 타입, 데이터를 적용하는 함수를 의미함. (예: B+Tree의 경우, internal , leaf node type; HashTable의 경우, hash bucket 과 hashing 함수 등)
- Desigin sapace 정의를 완료하면, First principle들의 조합으로 생성할 수 있는 자료구조를 쉽게 조직적으로 확인할 수 있음. (`Data Calculator` 도 이러한 design space내에서 동작함.)

### Periodic Table of Data Structure

- Provide insights and explanation on exsisting designs but also points to exciting research opportunities
- **RUM** : `Read`  `Update`  `Memory`  **amplification** 
자세한 내용 확인 : ([http://daslab.seas.harvard.edu/rum-conjecture/](http://daslab.seas.harvard.edu/rum-conjecture/))
- 아래 표에서 `Done` 이 표시가 안되었다고, 반드시 이전에 연구되지 않은 항목을 의미하는 것이 아니며 반대로, `Done` 으로 표시가 되어도, 추가 작업이 가능하지 않다는 것이 아님.

![Periodic Table of Data Structure](/assets/post_images/post200430_periodic_table_of_data_structure.png)


### Design continuums

- 동일한 deisng concept로 정의되어 있는 unified design space의 일부분이며, 동일한 design concept을 활용하여 튜닝을 할 수 있음.
- (예시) LSM Tree : log , LSM Tree , 그리고 Sorted array 간의 design continuums
    - `Log`: (highly) write-optimized data structure (순서 상관 없이, append) 하지만 point-lookup , range (read  성능 낮음)
    - `Sorted Array`: (highly) read-optimized data structure; binary search 를 통해 read 빠르게 할 수 있지만, write cost 큼.
    - `LSM Tree` : write cost 와 read cost 양 극단 (Log와 Sorted Array)사이에 존재하는 LSM Tree의 경우, 이 둘 사이의 균형을 유지하도록 함. (예: merge policy 튜닝을 통해)
    
 ![Design Continuums](/assets/post_images/post200430_design_continuums.png)
 
 (참고: "Dostoevsky: Better Space-Time Trade-Offs for LSM-Tree Based Key-Value Stores via Adaptive Removal of Superfluous Merging" 에서 가져옴)
  
### Review

- 워크로드 환경, 서버 환경 (스토리지 디바이스 성능, 물리코어 갯수 등) 등의 조건이 주어지면, Machine Learning을 통해 최적의 자료구조를 찾을 수 있도록 하는 방식은 흥미로웠다.
특히, 계속 해서 변하는 워크로드 환경 (예, 애플리케이션 기능이 변화하면서 access pattern이 달라지는 경우), 서버 환경 (예, 디바이스 및 CPU의 발전 등) 에서 
직접 구현을 하지 않아도 최적의 자료구조를 찾을 수 있도록 하는 것이 인상 깊음.
 
- 하지만, **최적의** 자료 구조를 찾기 위한 모델을 설계하기 위해서는 조금 더 구체적인 매개변수들이 필요하다. 
예를 들어, 하드웨어의 성능 (seq/rand IO bandwidth, latency) 등 만을 고려하는 것이 아니라,
기저 파일 시스템과의 조화 등 세부적인 항목에 대한 분석이 필요할 것 같다. 다
그리고, 추가적으로 관련 논문을 읽어봐야겠지만, 설계한 모델이 실제로 최적의 결과 값을 제공하는지에 대한 검증 방법에 대한 이해가 필요할 것 같음.
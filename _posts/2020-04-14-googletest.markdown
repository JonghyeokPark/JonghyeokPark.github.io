---
layout: post
title:  "Make your project more solid with Googletest"
date:   2020-04-14 08:15:36 +09:00
categories: Tools
---

이번 포스트에서는 GoogleTest를 활용하여, unittest를 하는 방법과 GoogleTest에서 제공하는 함수에 대해 알아본다.
추가적인 예제 코드는 GoogleTest [Github repository](https://github.com/google/googletest/blob/master/googletest/docs/samples.md)에서 확인 가능하다.

# Google Test setup
GoogleTest를 셋업하는 방법 중 다음과 같은 2가지 방법에 대해 알아본다.
여기서 소개한 방법 이외의 다양한 방법은 [다음](https://cliutils.gitlab.io/modern-cmake/chapters/testing/googletest.html)에서 찾아볼 수 있다.

## CMake FetchContent
Cmake 3.11 버전 이상의 버전에서 지원하는 `FetchContent` 기능을 활용하여, 쉽게 GoogleTest 모듈을 사용할 수 있다.

1. `CmakeList.txt`를 다음과 같이 수정한다.

```
cmake_minimum_required(VERSION 3.15)
project(googletest_example)
set(CMAKE_CXX_STANDARD 14)

include(FetchContent)
FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG        release-1.8.0
)

FetchContent_GetProperties(googletest)
if(NOT googletest_POPULATED)
  FetchContent_Populate(googletest)
  add_subdirectory(${googletest_SOURCE_DIR} ${googletest_BINARY_DIR})
endif()

add_executable(googletest_example main.cpp account.cc)
target_link_libraries(googletest_example gtest)
```

* googletest git repository에서 `release-1.8.0` 버전의 tag를 찾은 후 해당 프로젝트에서 fetch해 온다.
* googletest 라이브러리를 추가한다. 앞으로 소개할 예제에서는 `main.cpp`에서 `account.h`에 있는 함수들에 대한 unittest를 수행한다.


## Git Submodule 
1. 다음 명령어를 입력하여, Google test submodule을 checkout 한다.
```
git submodule add --branch=release-1.8.0 ../../google/googletest.git extern/googletest
```

2. `CmakeList.txt`에 아래의 내용을 추가한다.
```
option(PACKAGE_TESTS "Build the tests" ON)
if(PACKAGE_TESTS)
    enable_testing()
    include(GoogleTest)
    add_subdirectory(tests)
endif()
```

* 반드시, `enable_test()`을 호출해야 한다.


---


# Googletest usage

googletest 예제에서는 `account.h`에 정의된 함수에 대한 unittest를 수행한다.
* `main.cpp : `

```
#include <iostream>
#include <gtest/gtest.h>
#include "account.h"

TEST(AccountTest, initializationTest) {
  // Arrange
  BankAccount * account = new BankAccount;

  // Act
  account->initialize();

  // Assert
  const int expected_value = 0;
  EXPECT_EQ(expected_value, account->getValue());

  // Don't forget to release resources
  delete account;
}


int main(int argc, char* argv[]) {
  std::cout << "Hello, Google Test World!" << std::endl;
  testing::InitGoogleTest(&argc, argv);
  return RUN_ALL_TESTS();
}
```

* 먼저, `#include <gtest/gtest.h>` 헤더 파일 선언을 통해, googletest 모듈을 사용할 수 있도록 한다.
* `testing::InitGoogleTest()` 함수 호출을 통해, Google framework를 초기화하며, 반드시 `RUN_ALL_TESTS()` 함수 이전에 호출해야 한다.
* `RUN_ALL_TESTS()` 함수는 자동으로 TEST 매크로로 정의한 모든 테스트 케이스들을 감지하고 각각의 테스트 케이스들을 수행한다.
이 함수는 반드시 한번만 호출이 되어야 하며, 반복 호출하게 되는 경우 충돌이 발생한다.
* `TEST(AccountTest, initializeationTest)` 이는 googletest에서 사용하는 테스트 케이스를 정의하는 매크로이다. 
TEST 매크로의 첫번째 매개변수는 "테스트 케이스 이름" 이고, 두번째 매개변수는 "테스트 이름"을 의미한다. 
해당 매크로는 다음과 같은 `테스트 케이스 이름_테스트 이름`의 클래스를 생성한다  (예제의 경우, `AccountTest_initializationTest`)
* TEST 매크로 안에서, 테스트를 수행할 코드를 작성한다. 예제의 경우, account 객체를 생성한 후, 초기 `balance` 값이 0으로 되었는지 확인하는 간단한 예제이다.


---


# Assertions
googletest가 지원하는 assertion의 종류는 2가지이다 : `ASSERT_*` , `EXPECT_*`.
두가지 모두 테스트 케이스 결과 값의 상태를 확인하는 것이며, 차이점은 `ASSERT_*`의 경우 실패시 해당 케이스를 바로 종료하지만, 
`EXPECT_*`의 경우 실패해도 계속 테스트를 진행한다.

* 각각의 값들은 한번씩 평가가 되며, side effect는 고려안해도 됨.
* 어떤 값이 먼저 실행될 것인지는 컴파일러에 따라 다르며, 테스트 케이스에서는 어떤 값이 먼저 실행이 될지 정해놓고 작성하면 안됨.

## 참/거짓 판별 

| Assert               | Expect              | 검증 내용           |
|:--------------------:|:-------------------:|:-----------------:|
| ASSERT_TRUE(status)  &nbsp;&nbsp;| EXPECT_TRUE(status) &nbsp;&nbsp;| status가 참인지     |
| ASSERT_FALSE(status) &nbsp;&nbsp;| EXPECT_TRUE(status) &nbsp;&nbsp;| status가 거짓인지    |

## 이항 비교 

|         Assert        |         Expect        |   검증 내용  |
|:---------------------:|:---------------------:|:------------:|
|  ASSERT_EQ(exp, real) &nbsp;&nbsp; | EXPECT_EQ(exp, real) &nbsp;&nbsp; |  exp == real |
| ASSERT_NE(val1, val2) &nbsp;&nbsp; | EXPECT_NE(val1, val2) &nbsp;&nbsp; | val1 != val2 |
| ASSERT_LT(val1, val2) &nbsp;&nbsp; | EXPECT_LT(val1, val2) &nbsp;&nbsp; |  val1 < val2 |
| ASSERT_LE(val1, val2) &nbsp;&nbsp; | EXPECT_LE(val1, val2) &nbsp;&nbsp; | val1 <= val2 |
| ASSERT_GT(val1, val2) &nbsp;&nbsp; | EXPECT_GT(val1, val2) &nbsp;&nbsp; |  val1 > val2 |
| ASSERT_GE(val1, val2) &nbsp;&nbsp; | EXPECT_GE(val1, val2) &nbsp;&nbsp; | val1 >= val2 |

## 문자열 비교 

|            Assert            |            Expect           |              검증 내용             |
|:----------------------------:|:---------------------------:|:----------------------------------:|
|    ASSERT_STREQ(exp, real)   &nbsp;&nbsp; |   EXPECT_STREQ(exp, real)  &nbsp;&nbsp; |       두 문자열이 같은지 확인      |
|    ASSERT_STRNE(exp, real)   &nbsp;&nbsp; |   EXPECT_STRNE(exp, real)  &nbsp;&nbsp; |       두 문자열이 다른지 확인      |
| ASSERT_STRCASEEQ(val1, val2) &nbsp;&nbsp; | EXPECT_STRCASEQ(val1, val2) &nbsp;&nbsp; | 두 문자열이 같은지 (대소문자 무시) |
| ASSERT_STRCASENE(val1, val2) &nbsp;&nbsp; | EXPECT_STRCASNE(val1, val2) &nbsp;&nbsp; | 두 문자열이 다른지 (대소문자 무시) |  


---


# Customizing Failure message
기본적으로 googletest는 ASSERT 테스트 케이스를 실패할 경우, 실패한 코드 파일, 행 번호, 그리고 실패 메지를 출력한다.
실패 메시지의 경우, 다음과 같이 커스터마이징을 할 수 있다.

```
ASSERT_EQ(x.num(), y.num()) << "[FAIL] x와 y의 값이 다릅니다.";

for (int i = 0; i < x.size(); ++i) {
EXPECT_EQ(x{i}, y{i}) << "[FAIL] x와 y의 " << i << "번째 원소가 다름.";
}

```


## 테스트 결과 출력 화면 (예시) 

![이미지](/assets/post_images/post200414_googletest_screenshot.png)


### References
* [Googletest Git repository](https://github.com/google/googletest/tree/master/googletest)
* [Googletest FAQ](https://github.com/google/googletest/blob/master/googletest/docs/faq.md)
* [Googletest 설명 블로그](http://stinkfist.egloos.com/2262578)
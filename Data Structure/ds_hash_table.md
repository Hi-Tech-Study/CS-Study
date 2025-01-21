# 해싱, 해시 함수 란?

**해시 함수** 은 **임의의 길이를 가진 키를 입력** 으로 받아, **고정된 길이의 값인 해시**(또는 다이제스트)로 변환하는 단방향 함수(혹은 알고리즘) 를 의미한다.

**해싱** 은 **해시 함수를 통해 해시를 생성**하는 과정을 의미한다.

## 해시 함수의 특징

- **단방향성**. 입력 데이터 -> 해시 는 쉽지만, 해시 -> 입력데이터 로 역변환은 거의 불가능하다.

- 단방향성으로 인해 **데이터의 무결성을 보장**하고, **보안** 용도로 활용 가능하게 한다.

  - 블록체인
  - 비밀번호 저장 시
  - Git
  - 디지털 서명 (SHA 알고리즘, MD5 등)

    - SHA-256 의 경우, 32비트 크기의 해시를 생성하는 해시 알고리즘이다. 즉, 항상 32개의 고정된 크기의 해시를 생성한다.

    ![](/Data%20Structure/img/ds_hash_table_sha.png)
    출처 : 나

- **같은 입력 값에 대해 어떤 경우에도 동일한 출력 값이 보장**된다.

# Hash Table (해시 테이블)

- 해싱 을 통해 생성한 **해시(인덱스)** 와 **값** 으로 구성된 **연관 배열** 로 구성된 데이터 구조

  > 연관 배열은 키 하나와 값 하나가 연관되어 있는 형태의 데이터 구조. 키를 통해 연관되는 값을 얻을 수 있다. 맵 혹은 딕셔너리 라고도 부른다.

  ![](/Data%20Structure/img/ds_hash_table_overview.png)
  출처 : [Hash Tables, Hashing and Collision Handling](https://medium.com/codex/hash-tables-hashing-and-collision-handling-8e4629506572)

## 저장, 검색, 삭제

- 해쉬 테이블의 저장, 검색, 삭제의 평균 시간 복잡도는 모두 O(1)이다. 이는 데이터를 검색하기 위해 데이터를 모두 선회해야하는 선형 자료구조인 배열 혹은, 연결 리스트에 비해 **빠르다**.

  - 다만, 검색 및 삭제의 최악의 경우는 O(n) 이 될 수 있다. 이는 해시가 중복되어 테이블의 공간을 모두 찾는 경우이다. 후술할 해시가 중복되는 해시 충돌로 인해 발생할 수 있다.

- 또한, 배열, 트리와 같은 데이터 구조는 삭제 혹은 저장하기위해 해당 데이터와 모든 데이터를 비교해보아야하지만, 해시 테이블은 해시만 알면되기때문에 이러한 **불필요한 과정이 생략된다.**

# 해시 테이블의 해시 함수

해시 함수는 굉장히 다양하게 만들 수 있다. 해시 테이블에서 자주 사용하는 해시 함수의 형태는 다음과 같다.

```
h(x) = x mod n
```

`x` : 키 에 해당하며, 보통 정수 이다.
`n` : 보통 저장소의 길이를 나타낸다. 예를 들어 10 의 길이를 가지고 있는 해시 테이블이라면, `n` 은 10 이된다.

## 좋은 해시 함수의 조건

- 계산이 간단해야한다. 계산이 복잡하다면, **데이터 접근 속도가 저하** 될 것이다.
- 입력 키가 해시 테이블 전체에 **고루 저장** 되어, **해시 충돌을 최소화** 해야한다.

# 해시 충돌

해시 함수가 서로 다른 두개의 입력(키) 값에 대해 같은 출력(해시) 값을 변환하는 상황.

다양한 해결방법들이 있지만, 어떤 해결방법이든 연산 비용이 증가하기에 해시 테이블을 설계하는 초기단계에서 해시 충돌을 최대한 일어나지않도록 하는 것이 중요하다.

예를 들어, 앞서 살펴본 해시 알고리즘인 SHA 는 SHA-0, SHA-1 까지 해시 충돌 가능성이 존재했지만, SHA-256 의 경우 사실상 해시 충돌 가능성이 0에 수렴한다.

![](/Data%20Structure/img/ds_hash_table_collision.png)
출처 : [[10분 테코톡] 👩‍🏫코니의 #️⃣Hash Function](https://www.youtube.com/watch?v=Rpbj6jMYKag)

```
h(x) = x mod 13
h(7) = 7
h(20) = 7
```

## 해결 방법 1 : 체이닝 (chaining)

- 같은 해시로 해싱되는 데이터를 **모두 하나의 연결리스트로 연결하여 관리**.
- 따라서, 데이터를 검색할 때는, 해당 연결 리스트의 데이터를 차례로 지나가며 검색하게 된다.
  - 하지만 이는, 해시 함수의 해시값만 알면 데이터를 바로 검색할 수 있다는 장점이 없어지는 부분. 체인이 길어지면 그만큼 해시 테이블에서는 생략되는 데이터를 대조하는 과정이 길어진다.

## 해결 방법 2 : 개방 주소 (open addressing)

- 체이닝 과 달리, 어떻게든 **주어진 공간 내에서 해결**한다.
- 따라서, 모든 데이터가 반드시 자신의 해시와 일치하는 공간에 저장된다는 보장이 없다.

### 선형 조사

- 가장 간단한 방법
- **충돌이 일어난 자리에서 일차함수 폭으로(바로 다음 자리 부터)** 찾는다.
  - 계속 찾다가 테이블의 경계를 넘어가면, 맨 앞으로 돌아간다.
- 찾으려고 하는 위치가 멀수록 연산 횟수가 늘어난다.

### 이차원 조사

- **바로 다음 자리 부터가 아닌, 이차함수 폭**으로 찾는다.
- 특정 영역에 데이터가 몰려도, 그 영역을 빨리 벗어날 수 있다.
- 아래와 같은 경우, 이전에 지나왔던 경로를 모두 똑같이 지나와야 할 수 도 있다.
  ![](/Data%20Structure/img/ds_hash_table_quadra_problem.png)

![](/Data%20Structure/img/ds_hash_table_linear_probing.png)
출처 : [Linear Probing in Hashing](https://prepinsta.com/data-structures-and-algorithms-in-python/linear-probing-in-hashing/)

## 해결 방법 3 : 더블 해싱 (double hashing)

- 두 개의 해시 함수를 사용한다.
- **하나는 최초의 해시값을 얻을 때만, 나머지 하나는 해시 충돌이 일어났을 때 이동할 폭을 구하기 위해** 사용된다.
- 첫번째 해시는 같더라도 두번째 해시함수로 구한 해시까지 같은 확률은 매우 작으므로 서로 다른 보폭으로 이동할 가능성이 크다.

# 프로그래밍 언어 단계의 해시 테이블 과 연관 배열 구조(딕셔너리, 맵)

- Python 의 `dict` 클래스 는 매우 최적화된 **해시 테이블** 로 구현되어있다.
- JAVA 에서 `Dictionary` 클래스는 추상 클래스이며, `HashTable` 의 상위 클래스이다.
- C# 의 `Dictionary` 클래스 또한, **해시 테이블** 기반으로 구현되어있다.
- Javascript 의 Map 은 경우에 따라 해시 테이블로 구현될 수 있다.

  > _Map의 명세는 "평균적으로 집합 내 요소의 수에 따라 하위 선형인 접근 시간을 제공하는" 맵을 구현해야 한다고 기술되어 있습니다. 따라서 복잡성이 O(N)보다 더 나은 경우 내부적으로 해시 테이블(O(1) 룩업), 검색 트리(O(log(N)) 룩업) 또는 기타 데이터 구조로 표현될 수 있습니다._ https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Map

- Golang 의 map 은 해시 테이블로 구현되어있다.

다양한 프로그래밍 언어에서 딕셔너리, 혹은 맵을 내부적으로는 해시 테이블로 구현하고 있음을 확인할 수 있다.

데이터 구조 단계에서는 딕셔너리의 부분집합 으로서 해시 테이블이 존재하지만, 실제로 사용하는 프로그래밍 언어 단계에서는 거의 같은 위계로 사용되고 있는 듯하다.

# 가상 메모리 페이지 테이블

[가상 메모리의 페이지 테이블](https://github.com/Hi-Tech-Study/CS-Study/blob/main/OS/os_memory.md#%EC%9E%91%EB%8F%99-%EC%9B%90%EB%A6%AC) 에서도 해시 테이블을 사용할 수 있다.

이 경우, 키에 해당하는 논리 주소를 해싱 한다.

해시 함수를 어떻게 정의하냐에 따라 전체적인 페이지 테이블의 사이즈를 줄일 수 있다.

# 출처

- [해싱과 암호화는 어떻게 다른가요?](https://docs.tosspayments.com/blog/hashing-and-encryption-difference#%ED%95%B4%EC%8B%B1%EA%B3%BC-%EC%95%94%ED%98%B8%ED%99%94%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%8B%A4%EB%A5%B8%EA%B0%80%EC%9A%94)
- [[10분 테코톡] 👩‍🏫코니의 #️⃣Hash Function](https://www.youtube.com/watch?v=Rpbj6jMYKag)
- [Basics of Hash Tables](https://www.hackerearth.com/practice/data-structures/hash-tables/basics-of-hash-tables/tutorial/)
- [블록체인 해시함수 | 정의, 특징, 블록체인 활용 예시](https://www.codestates.com/blog/content/%EB%B8%94%EB%A1%9D%EC%B2%B4%EC%9D%B8-%ED%95%B4%EC%8B%9C%ED%95%A8%EC%88%98)
- [Learn Hash Tables in 13 minutes #️⃣](https://www.youtube.com/watch?v=FsfRsGFHuv4)
- [Hash Tables, Hashing and Collision Handling](https://medium.com/codex/hash-tables-hashing-and-collision-handling-8e4629506572)

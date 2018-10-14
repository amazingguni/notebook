# 7.1 시스템 모델

한 프로세스 집합 내의 모든 프로세스가 그 집합 내의 다른 프로세스에 의해서만 발생될 수 있는 사건을 기다린다면, 그 프로세스 집합은 교착 상태에 있다.

# 7.2 교착상태의 특징

교착상태의 성격을 규정하는 특징을 더 자세히 살펴본다.

## 7.2.1 필요 조건들

교착상태는 한 시스템에 다음 네 가지 조건이 동시에 성립될 때 발생할 수 있다

아래 조건들이 독립적이지는 않다. 하지만 나눠서 생각하는 것이 유용하다.

### 1. 상호배제

최소한 하나의 자원이 비공유 모드로 점유되어야 한다.

- 비공유 모드에서는 한 번에 한 프로세스만이 그 자원을 사용할 수 있다.
- 다른 프로세스가 자원을 요청한다면, 방출될 때까지 기다려야 한다.

### 2. 점유하며 대기(hold-and-wait)

프로세스는 최소한 하나의 자원을 점유한 채, 현재 다른 프로세스에 의해 점유된 자원을 추가로 얻기 위해 반드시 대기해야 한다.

### 3. 비선점(no preemption)

자원들을 선점할 수 없어야 한다. 즉 자원이 강제로 방출될 수 없고, 점유하고 있는 프로세스가 태스크를 종료한 후 그 프로세스에 의해 자발적으로만 방출될 수 있다.

### 4. 순환 대기(circular wait)

대기하고 있는 프로세스의 집합에서 P0은 P1이 점유한 자원을 P1은 p2가 점유한 자원을...Pn은 P0이 점유한 자원을 대기한다.

## 7.2.2 자원 할당 그래프

- 프로세스 P를 원으로
- 리소스 타입 R은 사각형으로 표현하고 인스턴스를 하나의 점으로 표현한다.
- 요청 간선은 R을 향해, 할당 간선은 점으로부터 프로세스로 향한다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_01_ResourceAllocation.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_01_ResourceAllocation.jpg)

자원 할당 그래프가 사이클을 포함하지 않으면 교착상태가 아니라는 것을 보일 수 있다.

- 사이클이 있을 경우 교착상태가 존재할 수도 있다(반드시는 아니다)
- 각 자원이 하나의 인스턴스만을 가지면, 하나의 사이클은 교착상태가 발생했다는 점을 의미한다.

**사이클이 있고 교착상태**

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_02_Deadlock.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_02_Deadlock.jpg)

**싸이클이 있지만 교착상태는 아님**

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_03_CycleNoDeadlock.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_03_CycleNoDeadlock.jpg)

- 결국 자원 할당 그래프에 사이클이 없다면, 시스템은 교착상태가 아니다.
- 반면 있다면 교착상태일 수도 있고 아닐 수도 있다.

# 7.3 교착상태 처리 방법

교착상태를 처리하는 데에는 3가지 방법이 있다.

1. 교착상태 예방, 회피
2. 교착상태를 허용하고 회복시킴
3. 교착상태가 발생하는 것을 무시
	- 유닉스 윈도우가 이렇게 한다.
	- 교착상태가 일년에 한두번 발생하기에, 가장 효율적인 방법일수도 있음

# 7.4 교착상태 예방

네 가지 필요조건이 성립하지 않도록 보장하여 예방할 수 있다.

## 7.4.1 상호 배제

상호 배제 조건은 공유가 불가능한 자원에 대해서는 반드시 성립해야 한다.

- 프린터는 동시에 여러 프로세스들에 의해 공유될 수 없다.

하지만 공유 가능한 자원들은 배타적인 접근을 요구하지 않는다.(읽기 전용 파일)

공유가 불가능한 자원은 존재할 수밖에 없기에 상호 배제 조건을 인정하지 않아 교착상태를 예방하는 것은 **불가능**하다.

## 7.4.2 점유하며 대기

프로세스가 자원을 요청할 때는 다른 자원들을 가지고 있지 않다는 것을 보장해야 회피할 수 있다.

- 실행되기 전에 필요한 모든 자원을 할당받는 방법
- 프로세스가 자원을 가지고 있지 않을 때만 자원을 요청할 수 있도록 제약

이 두 방법 모두 다음과 같은 문제가 있다

- 쓰지 않는 자원들의 이용률이 낮아질 수 있다.
- 기아 상태가 일어날 수 있다.

## 7.4.3 비선점

이미 할당된 자원이 선점되지 않아야 한다는 조건이다.

이것을 성립되지 않게 하기 위해서는 다음의 프로토콜을 사용할 수 있다.

1. 어떤 자원을 점유하고 있는 프로세스가 즉시 할당할 수 없는 다른 자원을 요청하면, 점유하고 있는 모든 자원들이 묵시적으로 방출된다.
2. 선점된 자원들은 그 프로세스가 기다리고 있는 자원들의 리스트에 추가된다.
3. 프로세스는 기다리던 자원 뿐만 아니라 이미 점유하였던 옛 자원들을 다시 획득할 수 있을 때에만 다시 시작될 것이다.

혹은,

1. 프로세스가 어떤 자원들을 요청한다
2. 이들이 사용 가능한지 검사한다.
	- 가능하다면 할당한다
	- 자원들이 대기중인 프로세스에 할당되어 있다면 선점하여 할당한다.
3. 불가능하다면, 요청 프로세스는 대기하게 된다.
	- 프로세스가 대기하는 동안 자원들 중 일부는 다른 프로세스가 요청하는 경우 선점될 수 있다.
4. 프로세스가 요청 중인 새로운 자원을 할당받고, 대기 중에 빼앗겼던 모든 자원을 되돌려 받았을 때에만 다시 시작할 수 있다.

이 방법은 CPU 레지스터나 메모리처럼 저장/복원이 용이한 자원에 종종 적용된다.

- 디스크 프린터에는 무리다.

## 7.4.4 순환 대기

자원을 요청하는 순서를 정의하여 해결할 수 있다.

1. 프로세스는 최초에 자원을 획득한 이후에는 가지고 있는 자원 다음 순서의 자원만 할당 받는다
2. 순서를 어기는 자원이 필요한 경우 자원을 다 방출하고 다시 할당받아야 한다.

# 7.5 교착상태 회피

예방 방법은 장치의 이용률이 저하되고 시스템 처리율이 감소한다는 부수적인 문제가 있다.

교착상태를 회피하는 다른 대안은 자원이 어떻게 요청될찌에 대한 추가 정보를 제공하도록 요구하는 것이다.

교착상태 회피 알고리즘은 시스템에 순환 대기 상황이 발생하지 않도록 동적으로 자원 할당 상태를 검사한다.

- 이 상태에는 가용 자원의 수, 할당된 자원의 수, 프로세스들의 최대 요구 개수가 포함된다.

## 7.5.1 안전 상태

시스템이 안전하다는 것은 어떤 순서로든 프로세스들이 요청하는 모든 자원(최대 요구 개수까지)을 교착상태를 야기시키지 않고 차례로 모두 할당해 줄 수 있다는 것을 뜻한다.

즉 시스템이 안전 순서를 찾을 수 있다면 시스템은 안전하다고 할 수 있다.

- 안전상태의 시스템은 교착상태가 발생하지 않는다.

모든 프로세스를 무사히 마칠 수 있는 순서를 찾을 수 없으면 불안전하다고 한다.

- 불안전하다는 것은 교착상태일 수도 있다는 것을 의미한다. 

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_06_StateSpaces.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_06_StateSpaces.jpgㅍ)

회피 알고리즘은 시스템의 상태가 항상 안전 상태를 떠나지 않도록 고수하는 것이다. 

- 최초에는 안전상태로 시작한다.
- 프로세스들이 자원을 요청하면 이후에도 안전 상태가 보장될 때에만 제공한다
- 따라서 이용률이 낮아질 수 있다.

## 7.5.2 자원 할당 그래프 알고리즘

만약 각 자원 타입마다 단지 하나의 인스턴스를 갖는 자원 할당 시스템을 갖고 있다면, 우리는 자원 할당 그래프의 변형을 사용할 수 있다.

- 요청 간선과 할당 간선에 추가하여 예약 간선(claim edge)라는 새로운 타입의 간선을 도입한다. 이 간선은 미래에 자원을 요청할 것이라는 의미이다.

> 이 그래프를 그리려면 자원이 반드시 예약되어야 한다.

프로세스 Pi가 자원 Rj를 요청한다고 가정하자

- 요청간선 Pi -> Rj를 할당 간선 Rj -> Pj로 변환해도 자원 할당 그래프에 사이클을 형성하지 않을 때에만 요청을 허용할 수 있다.
- 이때 사이클 탐지 알고리즘을 이용하는데, n^2의 연산이 필요로 한다.(n은 프로세스의 수)
- 사이클이 없다면 할당해도 안전상태가 된다.
- 사이클이 발견된다면 할당은 시스템을 불안전상태로 만들것이기에, 대기해야 한다.

아래에서 R2를 P2에 할당하면 사이클이 생기기 때문에 할당할 수 없다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_07_DeadlockAvoidance.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_07_DeadlockAvoidance.jpg)

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_08_UnsafeState.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_08_UnsafeState.jpg)

## 7.5.3 은행원 알고리즘

자원 할당 그래프 알고리즘은 자원이 여러 개씩 있게 되면 사용할 수 없다.

은행원 알고리즘은 자원이 여러개여도 사용할 수 있다. 하지만 효용성은 조금 떨어진다.

- 이 알고리즘을 은행에 적용하면 고객들이 현금을 찾으러 와도 일정한 순서에 의해 모든 고객의 요청을 다 들어줄 수 있기 때문이다.

이 시스템에서는 프로세스가 시작할 때 프로세스가 가지고 있어야 할 자원의 최대 개수를 자원 종류마다 미리 신고해야 한다. 

- 물론 이 수가 자원의 총 보유수를 넘어서면 안된다.
- 프로세스가 자원을 요청하면 시스템은 그것을 들어주었을 때 계속 안전 상태에 머무르게 되는지 판단해야 한다.
- 안전하다면 요청을 받아주고 그렇지 않다면 기다리게 한다.

은행원 알고리즘을 위해 필요한 자료구조는 다음과 같다.

- n이 프로세스의 수이고 m이 자원의 종류 수라고 하자
- Available - 각 종류별로 가용한 자원의 개수를 나타내는 백터로 크기가 m이다.
- Max - 각 프로세스가 최대로 필요로 하는 자원의 개수를 나타내는 행렬로 크기가 n * m이다.
- Allocation - 각 프로세스에게 현재 나가 있는 자원의 개수를 나타내는 행렬로 크기가 n * m이다.
- Need - 각 프로세스가 향후 요청할 수 있는 자원의 개수를 나타내는 행렬로 크기가 n * m이다.

### 7.5.3.1 안정성 알고리즘

1. Work와 Finish는 크기가 m과 n인 vector이다.
	- Work = Available로 초기값을 준다.
	- Finish[i] = false로 초기값을 준다
2. 아래 두 조건을 만족시키는 i 값을 찾는다.
	- Finish[i] == false
	- Need i <= Work
	- 못찾으면 step 4로 간다.
3. 해당 프로세스를 끝내줬다고 판단한다
	- Work = Work + Allocationi
	- Finish[i] = true
4. 모든 i 값에 대해 Finish[i] == true이면 안전상태에 있다.

연산하는데 (m * n^2)가 필요하다

### 7.5.3.2 자원 요청 알고리즘

자원 요청을 안전하게 들어줄 수 있는지를 검사하는 알고리즘을 설명한다.

- Request i는 프로세스 Pi의 요청 벡터라고 하자.
- (Request i[j] == k)라면 Pi가 Rj를 k개까지 요청한다는 뜻이다.

Pi가 자원을 요청하게 되면 아래와 같은 조치가 취해진다.

1. 만일 Requesti <= Needi 이면, Step2로 간다.
	- 아니면 시스템에 있는 개수보다 더 많이 요청했으므로 오류로 처리한다.
2. 만일 Requesti <= Available이면 step3로 간다.
	- 아니면 요청한 자원이 당장은 없기 때문에 Pi는 기다려야 한다.
3. 마치 시스템에 Pi에게 할당해준 것처럼 시스템 상태 정보를 아래처럼 바꾼다
	- Available = Available - Requesti
	- Allocationi = Allocationi + Requesti
	- Needi = Needi - Requesti
4. 안전 상태를 확인한다.
	- 불안전하다면 Pi는 Requesti가 만족될때까지 기다려야 한다.

### 7.5.3.3 예시

- 다섯 개의 프로세스
- 세 가지 종류의 자원(A 10개, B 5개, C 7개)

최초에는 이 시스템은 안전하다 1 -> 3 -> 4 -> 0 -> 2 순서로 실행 가능

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_IllustrativeExample.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_IllustrativeExample.jpg)

여기서 P1이 A 자원 한 개와 C 자원 두 개를 추가로 요청한다면

1. Request1 = (1, 0, 2)
2. Request1 <= Need1(1,2,2) 이기에 유효한 요청이다
3. 따라서 Request1 <= Available인지 본다.
	- (1, 0, 2) <= (3, 3, 2)
4. 할당 가능하기에 요청이 되었다고 가정하고 상태 정보를 만들고 안전한지 여부를 살펴본다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_IllustrativeExample_2.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_IllustrativeExample_2.jpg)

위의 표는 안전하다.

- 1 -> 3 -> 4 -> 0 -> 2 
- 요청을 받아들여준다.

# 7.6 교착상태 탐지

교착상태 예방이나 방지 알고리즘을 사용하지 않는 시스템은 다음 알고리즘을 반드시 지원해야 한다.

1. 교착상태 발생 여부를 검사하는 알고리즘
2. 교착상태 회복 알고리즘

## 7.6.1 각 자원 타입이 한 개씩 있는 경우

모든 자원들이 한 개의 인스턴스만 있다면, 대기 그래프를 사용해 탐지 알고리즘을 정의할 수 있다.

- 자원 할당 그래프에서 자원 타입의 노드를 제거하고 간선을 결합하여 대기 그래프를 만든다.
- Pi -> Pj 간선은 Pj가 가지고 있는 자원들에 대해 Pi가 그 자원이 필요하여 방출하기를 기다린다는 것이다.
- 이 또한 대기 그래프가 사이클을 포함할 경우에만 교착상태가 존재한다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_09_TwoGraphs.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_09_TwoGraphs.jpg)

## 7.6.2 각 타입의 자원을 여러 개 가진 경우

대기 그래프도 자원이 여러개 존재하면 사용할 수 없다.

- n이 프로세스의 수이고 m이 자원의 종류 수라고 하자
- Available - 각 종류별로 가용한 자원의 개수를 나타내는 백터로 크기가 m이다.
- Allocation - 각 프로세스에게 현재 나가 있는 자원의 개수를 나타내는 행렬로 크기가 n * m이다.
- Request - 각 프로세스가 현재 요청 중인 자원의 개수를 나타내는 행렬로 크기가 n * m이다.

탐지 알고리즘의 원리는 가능한 모든 할당 순서를 조사해 보는 방식이다.

1. Work와 Finish는 크기가 m과 n인 vector이다.
	- Work = Available로 초기값을 준다
	- Allocation i != 0 이면 Finish[i] = false, 0이면 true를 준다.
2. 아래 두 조건을 만족하는 i 값을 찾는다.
	- Finish[i] == false
	- Request i <= Work
	- 그러한 irk djqtdmf Eosms ㄴㅅ데 4fh rksek.
3. Work = Work + Allocation i, 
	- Finish[i] = true
	- step 2로 간다
4. 어떤 i 값에 대해 finish[i] == false 이면, 교착상태에 빠져 있는 것이다.

이 알고리즘을 설명하기 위해

- 다섯 개의 프로세스
- 세 가지 자원이 있다고 가정한다.(A 7개, B 2개, C 6개)

아래 시스템은 교착상태에 처해있지 않음을 알 수 있다.

- 0 -> 2 -> 1 -> 3 -> 4

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_IllustrativeExample_3.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_IllustrativeExample_3.jpg)

다음처럼 바뀌면 교착상태에 바뀌게 된다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_IllustrativeExample_4.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter7/7_IllustrativeExample_4.jpg)

## 7.6.3 탐지 알고리즘 사용

교착 상태가 자주 발생하면 탐지도 자주 해야 한다.

- 자원의 요청이 즉시 만족되지 않을 때마다 탐지 알고리즘 실행
	- 당연히 오버헤드가 크다.
	- 교착상태를 유발한 프로세스를 찾을 수 있다.

오버헤드를 줄이는 간단한 대안은 지정된 시간 간격으로, 예를 들면 한 시간에 한번 또는 CPU 이용률이 40%이하로 떨어질 때 호출하는 것이다.

- 교착 상태가 CPU의 이용률을 떨어뜨리기 때문
- 교착상태를 야기한 프로세스를 알 수 없다.

# 7.7 교착 상태로부터 회복

처리 방법

- 운영자가 수작업으로 처리
- 시스템이 자동으로 처리

교착상태를 깨는 두가지 방법

- 순환 대기를 깨기 위해 한 개 이상의 프로세스를 중지(bort)
- 교착 상태에 있는 하나 이상의 프로세스들로부터 자원을 선점하는 것

## 7.7.1 프로세스 종료

두 방법이 있다.

1. 교착 상태 프로세스를 모두 중지
	- 프로세스들의 연산이 유실되는 비용이 든다.
2. 교착 상태가 제거될 때까지 한 프로세스 씩 중지
	- 중지하고 탐지하고 중지하고 탐지해야 해 오버해드가 든다

## 7.7.2 자원 선점

자원 선점을 이용해 교착상태를 제거하려면, 교착상태가 깨어질 때까지 프로세스로부터 자원을 계속적으로 선점해 다른 프로세스에 주어야 한다.

고려해야 할 점들

1. 희생자 선택
	- 어느 자원과 어느 프로세스들이 선점될 것인가?
	- 더 적은 비용이 소요되는 프로세스를 희생자로 잡는다.
2. 롤백
	- 자원을 선점당한 프로세스를 롤백시키고 다시 실행시켜야 한다.
	- 프로세스를 재시작하는 것이 좋다.
3. 기아 상태
	- 항상 희생자가 되는 프로세스가 기아가 될 수 있다.
	- 롤백의 횟수를 비용의 요소에 넣는 방법으로 해결할 수 있다.
# 8.1 배경

전형적인 명령어 실행은

1. 메모리로부터 한 명령어를 가져온다
2. 명령어를 해독하고 피연산자(operand)를 가져온다.
3. 피 연산자에 대해 명령어를 실행하고 
4. 계산 결과를 메모리에 다시 저장한다.

이 장에서는 메모리를 관리하기 위한 다양한 기법에 포함되는 여러 이슈를 다룬다.

## 8.1.1 기본 하드웨어

주 메모리와 프로세서 자체에 내장되어 있는 레지스터들은 CPU가 직접 접근할 수 있는 유일한 저장장치이다.

- 모든 실행되는 명령어와 데이터들은 CPU가 직접적으로 접근할 수 있는 주 메모리와 레지스터에 있어야 한다.
- 없을 경우 처리 전에 메모리로 이동시켜 놔야 한다.

메모리에 접근을 완료하기 위해서는 많은 CPU 클록 틱 사이클이 필요하다.

- CPU 내에 레지스터들은 일반적으로 CPU 클록의 1 사이클 내 접근이 가능하다

이 때 명령어를 실행하지 못하고 지연되는(stall) 현상이 발생하게 된다.

- 이런 상황을 방지하기 위해 CPU와 주 메모리 사이에 빠른 속도의 캐시를 둔다.

각각의 프로세스는 독립된 메모리 공간을 갖는다.

- 이를 위해서 특정 프로세스만 접근할 수 있는 합법적인 메모리 주소 영역을 설정하고, 프로세스가 합법적인 영역만을 접근하도록 하는 것이 필요하다
- 기준(base)와 상한(limit)이라 불리는 두 개의 레지스터들을 사용하여 보호 기법을 제공한다.
- 이를 벗어난 접근이 발생하면 트랩을 발생시킨다.
- base, limit 수정은 특권 명령으로만 적재된다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_01_LogicalAddressSpace.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_01_LogicalAddressSpace.jpg)

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_02_HardwareAddressProtection.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_02_HardwareAddressProtection.jpg)


## 8.1.2 주소의 할당(Address Binding)

디스크에서 주 메모리로 들어오기를 기다리고 있는 프로세스들의 집합은 입력 큐(input queue)를 형성한다.

프로세스의 주소는 메모리 내 어느 부분으로도 올라오는 것이 좋다.

- 그래서 원시 프로그램에서 주소는 숫자가 아닌 심벌 형태로 표현된다.
- 컴파일러는 심벌 주소를 재배치 가능 주소로 바인딩 시킨다
- 연결 편집기나 적재기가 재배치 가능 주소를 절대 주소로 바인딩 시킨다.

바인딩 시점에 따라 메모리 주소 공간에서 명령어와 데이터의 바인딩을 구분할 수 있다.

1. 컴파일 시간 바인딩
	- 프로세스가 메모리 내에 들어갈 위치를 알 수 컴파일 시간에 알 수 있다면 절대 코드를 생성할 수 있다.
	- MS-DOS의 .COM 양식
2. 적재 시간 바인딩
	- 재배치 가능 코드는 적재 시간에 바인딩 된다.
3. 실행시간 바인딩
	- 프로세스 실행 중간에 메모리 내의 세그먼트에서 다른 세그먼트로 옮겨질 수 있을 때
	- 바인딩이 실행 시간에 이루어진다고 할 수 있다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_03_MultistepProcessing.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_03_MultistepProcessing.jpg)



## 8.1.3 논리 대 물리 주소 공간

- 논리주소 - CPU가 생성하는 주소, 가상주소라고도 한다.
- 물리주소 - 메모리가 취급하게 되는 주소

- 논리주소 공간 - 프로그램에 의해 생성된 논리 주소 집합
- 물리주소 공간 - 논리주소에 상응하는 물리 주소 집합

- 메모리 관리기(MMU, Memory Management Unit) - 가상 주소를 물리주소로 변환해줌
- base register - 프로세스가 할당되는 메모리의 시작, relocation 레지스터라고도 부름
	- 이 값은 사용자 프로세스에 의해 생성된 모든 주소에 더해져 물리 메모리로 변환된다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_04_DynamicRelocation.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_04_DynamicRelocation.jpg)

## 8.1.4 동적 적재(Dynamic Loading)

메모리 공간 이용률을 높이기 위해 동적 적재를 해야 한다.

- 각 루틴은 실제 호출되기 전까지는 메모리에 올라오지 않고 재배치 가능한 상태로 디스크에서 대기하고 있다.
- 먼저 메인 프로그램이 메모리에 올라와 실행되고
- 다른 루틴을 호출하게 되면 메모리에 있는지 조사하고 없으면 재배치 가능 연결 적재기(relocatable linking loader)를 부른다.
	- 재배치 가능 연결 적재기가 해당 루틴을 메모리로 가져온다
	- 가져온 루틴의 주소를 프로그램의 주소테이블에 기록한다.

오류 처리 루틴과 같이 간혹 발생하지만 코드 양이 많은 경우 유용하다.

## 8.1.5 동적 연결 및 공유 라이브러리(Dynamic Linking & Shared Libraries)

동적 연결에서는 연결이 실행 시기까지 미루어진다.

- 보통 동적 연결은(dynamic linking) 언어 서브루틴 라이브러리나 시스템 라이브러리에서 사용된다.
- 이 기능이 없었다면 모든 프로그램들은 시스템 라이브러리의 복사본, 혹은 참조하는 루틴을 가지고 있어야 한다. -> 공간 낭비

동적 연결에서는 라이브러리를 부르는 곳마다 스텁(stub)이 생긴다.

- 이 스텁은 메모리에 존재하는 라이브러리를 찾는 방법, 또 없을 경우 적재하는 방법을 알려준다.
- 다음 번에 호출될 때는 직접 라이브러리 루틴을 실행하게 된다.
- 여러개의 프로세스에서 한 시스템 라이브러리를 써도 공유할 수 있어진다.(공유 라이브러리)

라이브러리 루틴을 변경할 때에 한번에 새로운 버전을 사용할 수 있게 할 수 잇어 좋다.

- 대신 코드상에 라이브러리 버전 정보를 적어줘야 하게 된다.

동적 연결은 운영체제의 도움이 필요하다.

- 운영체제만이 여러 프로세스가 같은 메모리 주소로 접근하도록 허용할 수 있다.

# 8.2 Swapping

필요한 경우 프로세스는 실행 도중에 임시로 보조 메모리로 교체되어 나갔다가 다시 돌아올 수 있다. 이를 스와핑이라 한다.

스왑되어 나갔다 돌아온 프로세스가 이전과 동일한 주소로 되돌아 오는 것이 가장 간단하다

- 주소 바인딩 문제가 간단
	- 만약 어셈블이나 적재 시간에 바인딩이 이루어지는 프로세스의 경우다른 위치로 이동될 수 없기 때문
	- 실행시간 바인딩 프로세스는 다른 공간으로 이동되어도 무방하다.

스와핑은 보통 디스크를 사용하여 이루어진다.

1. 디스패처가 준비완료 큐의 다음 프로세스를 고를 때 메모리에 올라와있지 않으면 디스크에서 불러오게 된다.
2. 이 때 프로세스를 위한 공간이 메모리에 없다면 현재 메모리에 있는 프로세스를 내보내고(swap out) 불러와야 한다.
3. 그러고 나서 레지스터 등을 실행해야 할 프로세스의 것으로 다시 적재하고 제어권을 넘긴다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_05_ProcessSwapping.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_05_ProcessSwapping.jpg)

스와핑 시스템에서는 문맥 교환 시간이 상당히 오래 걸린다.

- 따라서 사용해야 부분만을 스왑하는 것이 중요하다.

스와핑을 할 때는 아래 제약이 따른다.

한 프로세스를 스왑아웃하려면, 완전히 유휴 상태에 있음을 확인해야 한다. 

특히 입출력을 기다리는 경우는 더욱 주의해야 한다. 이 문제의 해결책은 2개이다.

1. 입출력이 종료되지 않은 프로세스를 스왑하지 않거나
2. 입출력은 항상 운영체제의 버퍼와만 하는 것
	- 전송은 프로세스가 스왑되어 들어온 다음 하면 된다.

스와핑은 평소에는 하지 않지만, 너무 많은 프로세스가 실행되고 메모리 공간의 부족이 느껴질 때 실행된다.


# 8.3 연속 메모리 할당

메모리는 일반적으로 **1)운영체제, 2)사용자 프로세스** 두 부분으로 나누어진다.

- 0번지부터 인터럽트 백터가 있다
- 운영체제도 그 근처 하위 메모리에 위치한다.
- 운영체제 코드 중 필요에 따라 메모리에 없어도 되는 코드를 일시적 운영체제 코드(transient OS code)라고 부른다. 이를 통해 메모리가 필요할 때에 운영체제 코드의 크기를 줄일 수 있다.

## 8.3.1 메모리 보호

논리 주소는 limit 레지스터보다 작아야 한다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_06_HardwareSupport.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_06_HardwareSupport.jpg)

## 8.3.2 메모리 할당

프로세스에 메모리를 할당하는 가장 간단한 방법은 고정된 크기로 분할하여 적재하는 것이다.

- 분할의 개수가 다중 프로그래밍 정도를 결정한다.
- 한 분할이 비면 입력 큐에서 한 프로세스가 선택되어 빈 분할에 들어온다.

물론 현대에는 거의 안쓰인다.

가변 분할 기법에서 운영체제는 메모리의 사용 현황을 파악할 수 있는 테이블을 유지한다.

- 초기에 메모리 공간은 한 개의 사용 가능한 블록으로 간주
- 프로세스가 시스템에 들어오면 입력 큐에 넣는다
- 운영체제는 각 프로세스가 메모리를 얼마나 요구하며, 사용 가능한 메모리 공간이 어디에 얼마나 있는지를 고려하여 공간을 할당
- 프로세스가 공간을 할당받게 되면, 이후로는 CPU를 할당받기 위해 경쟁
- 프로세스가 끝나면 메모리를 반납
- 운영체제는 입력 큐에 있는 다른 프로세스로 이 공간을 채운다.

운영체제는 항상 가용 공간의 크기들과 입력 큐를 유지해야 함.

동적 메모리 할당 문제를 해결하기 위한 기법은 세가지가 있다.

1. 최초 적합
2. 최적 적합
3. 최악 적합

보통은 최초와 최적 적합이 효율적이라고 입증되었다.(일반적으로 최초 적합이 속도가 빠르다.)

## 8.3.3 단편화(Fragmentation)

외부 단편화

- 프로세스가 적재되고 제거되는 일이 반복되다 보면 어떤 자유 공간은 너무 작은 조각이 되어 버린다.
- 모두 합치면 충분한 공간이 되지만 너무 작은 조각들로 여러 곳에 분산되어 있을 때 외부 단편화가 발생한다.
- 통계적으로 봤을 때 최초 적합의 경우 1/3의 메모리를 쓸 수 없게 된다.(50% 규칙)
- 압축을 통해 이 문제를 해결할 수 있다.
	- 한메모리의 모든 내용을 한군데로 몰고 자유 곤간들을 다른 한 군데로 몰아서 큰 블록을 만드는 것이다.
	- 재배치가 어샘블이나 적재 시에 정적으로 되는 프로세스가 있는 경우 압축은 불가능하다.
	- 비용이 많이 든다
- 또 다른 방법은 프로세스의 논리 주소 공간을 여러 개의 비 연속적인 공간으로 나누어 할당하는 방법이다.(페이징, 세그먼테이션)

내부 단편화

- 일반적으로 메모리를 작은 공간으로 분할하고 프로세스가 요청하면 이를 할당하게 된다.
- 할당된 공간은 요구된 공간보다 약간 더 클 수 있다. 이 차이가 내부 단편화이다.


# 8.4 Paging

페이징은 외부 단편화를 방지하고 압축 작업이 필요 없게 만든다.

- 페이징을 사용하면 주 메모리에 올라와 있던 부분을 스왑아웃할 때 연속된 공간을 찾을 필요가 없어진다.

## 8.4.1 기본 방법

- 물리 메모리는 프레임이라 불리는 고정 크기의 블록으로 나누어져 있고, 
- 논리 메모리는 페이지라 불리는 프레임과 같은 크기의 블록으로 나누어진다.
- 프로세스가 실행될 때 그 프로세스의 페이지는 파일 시스템 또는 보조 메모리로부터 가용한 주 메모리 프레임으로 적재된다.
	- 오 원래는 페이지가 파일 시스템에 매핑 되어 있었던듯

CPU에서 나오는 모든 주소는 페이지 번호(p)와 페이지 변위(d:offset)으로 나누어진다.

- 페이지 번호는 페이지 테이블을 액세스할 때 사용된다
- 페이지 테이블은 주 메모리에 존재하는 페이지의 기준 주소를 가지고 있다.
- 페이지에 매핑된 프레임의 주소에 페이지 변위를 더하면 물리주소가 된다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_10_PagingHardware.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_10_PagingHardware.jpg)

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_11_PagingModel.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_11_PagingModel.jpg)

만약 논리 주소 공간의 크기가 2^m, 페이지가 2^n 크기라면 논리 주소의 상위 m-n 비트는 페이지 번호를 나타내고, 하위 n 비트는 페이지 변위를 나타낸다.

![https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_11A_PageNumberOffset.jpg](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter8/8_11A_PageNumberOffset.jpg)






## 8.4.2 하드웨어 지원

## 8.4.3 보호

## 8.4.4 공유 페이지

# 8.5 페이지 테이블의 구조

## 8.5.1 계층적 페이징(Hierachical Paging)

## 8.5.2 해시된 페이지 테이블(Hashed Page Tables)

## 8.5.3 역 페이지 테이블(Inverted Page Table)

# 8.6 Segmentation

## 8.6.1 기본 방법

## 8.6.2 하드웨어

# 8.7 사례 Intel Pentium

## 8.7.1 Pentium 세그먼테이션

## 8.7.2 Pentium 페이징

## 8.7.3 Pentium 시스템 기반의 Linux
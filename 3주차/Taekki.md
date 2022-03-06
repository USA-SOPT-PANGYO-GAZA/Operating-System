## 0. 들어가며



세 번째 시간에는 프로세스에 대해 학습한다.

## 1. 프로세스의 개념


<aside>
⛔ Process is a program in execution

프로세스는 실행 중인 상태의 프로그램이다.

</aside>

### 프로세스의 문맥 (Context)

프로세스의 **현재 상태**를 나타내는 데 필요한 모든 요소

ㄴ 현재 읽고 있는 기계어, 수행되고 있는 메서드, 변수에 들어있는 값, PC(Program Counter)가 가리키고 있는 부분 등

1. CPU와 관련된 하드웨어 문맥 (PC, register)
2. 프로세스의 주소 공간 (code, data, stack)
3. 프로세스 관련 커널 자료 구조 (PCB, Kernel stack)
    - Kernel stack은 프로세스마다 별도로 두고 있음

<br />

### 프로세스의 상태 (Process State)

CPU를 잡고 있고 instruction을 수행하고 있는 프로세스의 상태

`Running` - 수행 중인 상태

`Ready` - 다른 모든 준비는 되어있고, CPU만 얻으면 당장 instruction을 수행할 수 있는 상태 (최소한의 메모리는 가진 상태, I/O 작업)

`Blocked` - 당장 instruction을 수행할 수 없는 상태 (공유 데이터, 디스크, 키보드 등)

`Suspended(stopped)` - 외부적인 이유로 프로세스의 수행이 정지된 상태

`New` - 프로세스가 생성중인 상태

`Terminated` - 수행이 끝난 상태 (정리 중인 상태)

Blocked - 자신이 요청한 event가 만족되면 Ready

Suspended - 외부에서 resume해 주어야 Active

<br />

### Process Control Block (PCB)

PCB의 구성 요소는 크게 4가지로 구성되어 있다.

✅ 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지(관리)하는 정보

<br />

### 문맥 교환 (Context Switch)

정말 중요한 개념이다.

**하나의 사용자 프로그램(A)에서 또 다른 사용자 프로그램(B)으로 CPU가 넘어가는 과정**

첫 번째, 두 번째 시간에서도 살펴봤듯이 하나의 프로세스는 계속해서 CPU를 독점할 수 없다. 나름 공평하게 돌아가면서 CPU를 사용하게 되는데, 그럴 때마다 이전 프로세스가 어디까지 일을 하고 있었는지(어떤 instruction을 수행하고 있었는지)를 기억하고 있어야 다시 CPU의 제어권을 넘겨받았을때 이어서 일을 수행할 수 있을 것이다.

예를 들면, 우리가 넷플릭스에서 영상을 보고 있었는데 어디까지 보고 있었는지 기억해야 다음에 보던 부분부터 이어볼 수 있어야 하는 것처럼 프로세스 역시 현재 수행 중이던 상태를 기억한 다음 CPU를 넘겨주거나 넘겨받게 된다.

**그렇다면 현재 상태의 정보는 어떻게 기억하게 될까?**

1) CPU 안에 PC, Register 등 기억해야 하는 정보들이 있을텐데

2) 이를 운영체제의 커널 스택 내부의 data 영역에서 **PCB**라는 자료구조 형태로 그 정보를 관리하게 된다.

**🚨Context Switch의 상태를 잘 구분해야 한다.**

Kernel mode로 넘어가는 순간, 프로세스의 문맥은 기억하고 있게 된다.

- [ ]  (1) User mode(A) → Kernel mode → User mode(A) `mode switching`
- [x]  (2) User mode(A) → Kernel mode → User mode(B) `문맥 교환 일어남` `process switching`

<br />

### 프로세스를 스케줄링하기 위한 큐

1) Job queue - 현재 시스템 내에 있는 모든 프로세스의 집합

Ready queue, Device queue를 모두 포함

2) Ready queue - 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기 기다리는 프로세스의 집합

3) Device queue - I/O 디바이스의 처리를 기다리기 위한 큐

<br />

### 스케줄러 (Scheduler)

Long-term scheduler (장기 스케줄러, job scheduler)
메모리를 어떤 프로세스에게 줄 지를 결정
degree of Multiprogramming 제어 (애초에 올라갈 수 있는 프로그램 수 제어)

Short-term scheduler (단기 스케줄러, CPU scheduler)
다음에 어떤 프로세스에게 CPU를 넘겨주어야 할 지 결정

Medium-term scheduler (중기 스케줄러, Swapper)
여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
프로세스에게서 메모리를 뺏는 문제
degree of Multiprogramming 제어 (일단 올려놓고 개수 제어)
더 효과적
Suspended (stopped) → 새롭게 추가된 상태

<br />

## 2. Thread

같은 일은 하는 프로세스를 여러 개 띄어놓고 싶다.

<br />

### Thread(스레드) : CPU를 수행하는 단위

**프로세스 하나에 프로세스 수행 단위만 여러 개 두고 있는 것**

프로세스는 하나만 띄어놓고 Code 영역의 어느 부분을 실행하고 있는가를 체크하기 위한 PC를 여러 개 띄어놓는 것.

1) Thread의 구성 (CPU 관련 정보만 독자적으로 가지고 있다.)

- program counter
- register set
- stack space

2) Thread가 동료 thread와 공유하는 부분**(=task)**

- code
- data
- OS resources

<br />

### 장점

- 응답성 → 빠른 응답성
    - 하나의 스레드가 웹 페이지의 이미지를 불러오는 동안에 해당 스레드를 blocked 시키지 않고 또 다른 스레드가 이미 읽어온 텍스트라도 display
    - 게임을 예로 들어봐도 좋을 듯하다.
        - 타이쿤, 슈팅 게임 등 (독립적인 스레드가 수행된다.)
- 자원 공유
    - 프로세스 내 스레드 간 통신은 메모리와 파일을 공유하기 때문에 커널의 개입이 필요없다.
- 경제성
    - 문맥 교환은 오버헤드가 크지만(PCB 바꿔주고, 캐시 메모리에 있는 값을 모두 클리어해주고) 쓰레드 간의 CPU switching은 동일한 공간을 사용하기 때문에 오버 헤드가 그렇게 크지 않다.
- MP 아키텍처의 유틸성

1) 다중 스레드로 구성된 테스크 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 테스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있다.

2) 같은 일을 수행하는 다중 스레드가 협력하여 높은 처리율과 성능 향상을 얻을 수 있다.
파일 여러 개를 띄우는 작업

3) 스레드를 사용하면 병렬성을 높일 수 있다.
순차적인 실행을 막는다.

<br />

### 스레드 상태와 동기화

- 스레드 역시 실행, 준비, 대기와 같은 상태를 가지기 때문
- 보류는 없다. (프로세스 레벨의 개념)
    - 보류란 메모리와 관련된 개념인데, 스레드는 애초에 메모리 공간을 하나만 가지고 있기 때문에 보류는 없다.

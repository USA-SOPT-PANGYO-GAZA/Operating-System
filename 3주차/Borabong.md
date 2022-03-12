# 3️⃣차시

# Process

---

## 💡 프로세스의 개념

```
💬 “Process is a program in execution”
```
: 프로세스는 실행중인 프로그램이다.

### 프로세스의 문맥(context)

```
💬 프로세스가 현재 어떤 상태에서 수행되고 있는지 정확히 규명하기 위해 필요한 정보이다.

프로그램이 처음 태어나서 쭉 실행되다가 언젠간 종료가 될 텐데, 문맥이라는 것은 그 중간 어느 시점을 딱 잘라놓고 봤을 때 그 프로그램이 무엇을 어떻게 실행했는지, 현재 시점이 어떤 상태에 있는지를 정확하게 나타내기 위해서 사용되는 개념이다.
```

<img width="310" alt="os1" src="https://user-images.githubusercontent.com/63224278/157997307-c15aabe5-b301-41f6-b779-13ee99d4191c.png">

1. CPU 수행 상태를 나타내는 **`하드웨어 문맥`** (현재 CPU가 instruction을 어디까지 수행했는지 알기 위해서)
    - Program Counter (= PC)
    - 각종 register
2. 프로세스의 독자적인 `주소 공간`
    - **code**(프로그램이 CPU를 잡게 되면 PC가 해당 프로세스의 code영역을 가리키게 됨), **data**, **stack**
3. 프로세스 관련 `커널 자료 구조` (OS와 연관 → 더 알아보기)
    - PCB(Process Control Block)
    - Kernel stack (system call이 발생하면 PC가 프로세스의 주소공간을 가리키지 않고, 커널 주소공간을 가리키게 됨)
        - OS에게 서비스를 요청할 때 Kernel의 코드를 실행하게 됨 → 여러 프로그램들이 공유하는 stack
        - 따라서 어떤 프로그램이 요청했는지에 따라 커널 스택은 프로세스마다 별도로 두고 있음. (겹치지 않게 하기 위해서)
    
- 현대에는 여러 프로세스가 번갈아 실행되기 때문에 (time-sharing을 통한) 프로세스의 현재 문맥을 알고 있지 않으면 다음번 CPU를 잡았을 때 앞부분부터 재실행해야하는 문제가 생길 수도 있기 때문에 해당 프로세스가 어느 시점까지 수행했는지를(문맥을) 잘 파악하고 있어야 다음 일이 순차적으로 실행될 수 있음!

 <br>

## 💡 프로세스 상태도

<img width="674" alt="os2" src="https://user-images.githubusercontent.com/63224278/157997308-35c9fd29-37b4-4e42-bd8f-47eb82d094e7.png">

- 프로세스 생성 후 메모리에 올라가는 과정을 admitted.

 
<br>

### [컴퓨터로 나타낸 프로세스 상태도]

<img width="345" alt="os3" src="https://user-images.githubusercontent.com/63224278/157997314-82859c23-ab00-4dd0-bf07-77082a25647b.png">

1. CPU는 굉장히 빠르고, 여럿이 공유하는 자원이기 때문에 하나의 프로세스만 CPU에서 Running을 하고 있다가 timer interrupt가 들어오면 CPU를 뺏기고 다시 Ready queue의 맨 끝으로 가서 줄을 서게 됨 
2. CPU에서 running이 되다가 disk에서 뭔가를 읽어와야 할 때 상태는 running에서 blocked로 바뀌고 Disk I/O queue에 가서 줄을 서게 됨 (Disk는 이 때 Disk controller의 지휘 하에 순서대로 disk 작업을 수행할 것임) 
3. 디스크 작업 끝나면 disk controller가 cpu에게 interrupt를 걸어 작업이 끝났음을 알리고 CPU는 하던 작업을 잠시 멈추고 CPU제어권을 운영체제 커널에게 넘겨준다 
4. 운영체제 커널은 I/O를 요청했던 프로세스의 작업이 끝났으니 해당 프로세스의 상태를 blocked → ready로 바꿔서 CPU를 얻을 수 있는 상태로 바꿔준다.

⇒ 모든 자원들이 놀지 않고 모두 일을 할 수 있는 매커니즘이 필요.

<img width="690" alt="os4" src="https://user-images.githubusercontent.com/63224278/157997320-65c5d2ac-5efd-4c59-bae4-48bb3d342343.png">

- queue라는 것은 운영체제 커널이 본인의 Data영역에 자료구조로 queue를 만들어놓고 프로세스의 상태를 바꿔가면서 운영하는 것. (더 찾아보기)

<br>

## 💡 프로세스의 상태(Process State)

: 프로세스는 상태(state)가 변경되며 수행된다.

### `Running`

- CPU를 잡고 instruction을 수행중인 상태 (단 하나의 process가 해당)

### `Ready`

- CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족하고)
- 물리적인 메모리에 올라가 있는 상태
    - 다른 모든 준비는 끝나 있고 CPU만 얻으면 실행이 가능한 상태를 의미.

### `Blocked(wait, sleep)`

- CPU를 주어도 당장 instruction을 수행할 수 없는 상태
- Process 자신이 요청한 event(예: I/O 작업)가 즉시 만족되지 않아 이를 기다리는 상태
- ex) 디스크에서 file을 읽어와야 하는 경우

### Suspended(stopped)

→ `중기 스케줄러`로 인해 생겨난 상태

- 외부적인 이유로 프로세스의 수행이 정지된 상태
- 프로세스는 통째로 디스크에 swap out 된다
- (예) 사용자가 프로그램을 일시 정지시킨 경우 (break key)
    - 시스템이 여러 이유로 프로세스를 잠시 중단시킴 (메모리에 너무 많은 프로세스가 올라와 있을 때)

```
💬 Blocked: 자신이 요청한 Event가 만족되면 Ready
Suspended: 외부에서 resume해 주어야 Active
```

### New

- 프로세스가 생성중인 상태

### Terminated

- 수행이 끝난 상태


<br>

## 💡 Process Control Block (PCB)

### PCB

- 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보
- 다음의 구성 요소를 가진다. (구조체로 유지)
    
    <img width="235" alt="os5" src="https://user-images.githubusercontent.com/63224278/157997322-b8852bfc-0309-4ce3-96f3-93760bd045a2.png">
    
    (1) OS가 관리상 사용하는 정보
    
    - Process state, Process ID
    - scheduling information, priority(먼저온 순서대로 처리하지 않고, 우선순위가 있음)
    
    (2) CPU 수행 관련 하드웨어 값
    
    - Program counter, registers
    
    (3) 메모리 관련
    
    - Code, data, stack의 위치 정보
    
    (4) 파일 관련
    
    - Open file descriptors...

<br>

## 💡 문맥 교환(Context Switch)

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
    - 프로세스는 짧은 시간 간격으로 CPU를 얻었다 뺏겼다 하기 때문에 CPU를 뺏겼다가 다시 얻었을 때 뺏기던 시점의 문맥을 기억해두었다가 그 시점부터 일을 수행할 수 있게끔 하는 매커니즘이 필요!
- CPU가 다른 프로세스에게 넘어갈 때 `운영체제는` 다음을 수행.
    
    <img width="505" alt="os6" src="https://user-images.githubusercontent.com/63224278/157997324-fc83d13c-dd71-4c60-9888-307998cfe34a.png">
    
    - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
    - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 얻어옴
    
    ```
    💬 문맥교환 시 save하는 정보는 `커널`에서 해당 프로세스의 PCB 중에 위치한다.
    ```
    
<br>

### System call이나 interrupt 발생시 반드시 `문맥교환`이 일어나는 것은 아니다!

- 사용자 프로세스로부터 CPU가 운영체제로 넘어가는 것이기 때문에 문맥교환은 아니다.
    - 하지만 이 이후에 운영체제가
    
    <img width="606" alt="os7" src="https://user-images.githubusercontent.com/63224278/157997328-a038c865-f323-4c0b-a280-d362535418b8.png">
    
    (1) 사용자 프로세스 A → kernel mode → 사용자 프로세스 A (문맥 교환 X)
    
    - (CPU 수행 정보 등) 약간의 문맥 save가 되어야하긴 함. 하지만 교환은 아님.
    
    (2) 사용자 프로세스 A → kernel mode → 사용자 프로세스 B (문맥 교환 O)
    
    - timer interrupt라는 것은 CPU를 다른 프로세스에게 넘기기 위해 발생되는 interrupt이므로 또 다른 사용자 프로세스에게 CPU를 넘기게 되기 때문에 문맥교환 발생.
    
    ```
    💬 (1)의 경우에도 CPU 수행 정보 등 context의 일부를 PCB에 save해야 하지만, 
    문맥교환을 하는 (2)의 경우 그 부담이 훨씬 큼 (eg. cache memory flush)
    ```
    
<br>

## 💡 프로세스를 스케줄링하기 위한 큐

### Job queue

- 현재 시스템 내에 있는 모든 프로세스의 집합

### Ready queue

- 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합

### Device queues

- I/O device의 처리를 기다리는 프로세스의 집합

⚙️ 프로세스들은 각 큐를 오가며 수행된다.

<img width="555" alt="os8" src="https://user-images.githubusercontent.com/63224278/157997334-824cc996-eebf-4008-99a9-ef7373ae93b2.png">

⚙️ 프로세스 스케줄링 큐의 모습

<img width="567" alt="os9" src="https://user-images.githubusercontent.com/63224278/157997336-75649f1f-c81e-4352-97b8-09dfdfce247e.png">


<br>

## 💡 스케줄러(Scheduler)

### Long-term scheduler (장기 스케줄러 or job scheduler)

- 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
- 프로세스에 `memory`(및 각종 자원)를 주는 문제
- degree of Multiprogramming을 제어 → 메모리를 줌으로써 멀티프로그래밍을 제어함.
- time sharing system에는 보통 장기 스케줄러가 없음 (무조건 ready)
- [장기 스케줄러]
    
    작업 스케줄러라고도 부르며 어떤 프로세스를 준비 큐에 삽입할지를 결정하는 역할을 함. 디스크에서 하나의 프로그램을 가져와 커널에 등록하면 프로세스가 되는데 이때 디스크에서 어떤 프로그램을 가져와 커널에 등록할지(준비큐에 등록할지) 결정.
    
    장기 스케줄러는 수십 초 내지 수 분 단위로 가끔 호출되기 때문에 상대적으로 속도가 느린 것이 허용됨.
    또한 장기 스케줄러는 메모리에 동시에 올라가 있는 프로세스의 수를 조절하는 역할.
    
    하지만 현대의 시분할 시스템에서 사용되는 운영 체제에는 일반적으로 장기 스케줄러를 두지 않는 경우가 대부분.
    과거에는 적은 양의 메모리를 많은 프로세스들에게 할당하면 프로세스당 메모리 보유량이 적어져 장기 스케줄러가 이를 조절하는 역할을 했지만,
    현대의 운영체제에서는 프로세스가 시작되면 장기 스케줄러 없이 바로 그 프로세스에 메모리를 할당해 준비 큐에 넣어주게 됨
    
<br>

### Short-term scheduler (단기 스케줄러 or CPU scheduler)

- 어떤 프로세스를 다음번에 running시킬지 결정
- 프로세스에 CPU를 주는 문제
- 충분히 빨라야 함 (millisecond 단위)
- [단기 스케줄러]
    
    CPU스케줄러라고도 하며 준비 상태의 프로세스 중에서 어던 프로세스를 다음 번에 실행 상태로 만들 것인지를 결정.
    시분할 시스템에서 타이머 인터럽트가 발생하면 단기 스케줄러가 호출됨.
    
    일반적으로 `스케줄러`라 함은 `단기 스케줄러`를 의미하며 단기 스케줄러는 미리 정한 스케줄링 알고리즘에 따라 CPU를 할당 할 프로세스를 선택함.
    
    단기 스케줄러는 밀리 세컨드(ms) 이하의 시간 단위로 매우 빈번하게 호출되기 때문에 수행 속도가 충분히 빨라야 한다.
    
<br>

### Medium-term scheduler (중기 스케줄러 or Swapper)

- 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
- 프로세스에게서 memory를 뺏는 문제
- degree of Multiprogramming을 제어
- [중기 스케줄러]
    
    너무 많은 프로세스에게 메모리를 할당해 시스템의 성능이 저하되는 경우 이를 해결하기 위해 **메모리에 적재된 프로세스의 수를 동적으로 조절**하기 위해 추가된 스케줄러
    
    만약 메모리에 많은 수의 프로세스가 적재되어 프로세스 당 보유하고 있는 메모리량이 극도로 적어지게 되면 CPU 수행에 당장 필요한 프로세스의 주소 공간조차도 메모리에 올려놓기 어려운 상황이 발생하게 됨. 그렇게 되면 디스크 I/O가 수시로 발생하게 되어 시스템의 성능이 심각하게 저하될 수 있음. 이런 경우 메모리에 올라와 있는 프로세스 중 일부로 부터 메모리를 통째로 빼앗아 그 내용을 디스크의 스왑 영역에 저장해 둠. 이와 같은 행위를 **`스왑 아웃`**(swap out)이라고 함.
    
    디스크로 스왑 아웃시켜야 하는 경우 Blocked 상태에 있는 프로세스들을 첫번째로 스왑 아웃 시킴. 이유는 Blocked 상태의 프로세스들은 당장 CPU를 획득할 가능성이 없기 때문.
    
    Blocked 상태의 프로세스들을 스왑 아웃시켜도 문제가 해결되지 않는 경우 중기 스케줄러는 타이머 인터럽트가 발생해 준비 큐로 이동하는 프로세스를 추가적으로 스왑아웃 시킴. 중기 스케줄러는 이러한 방식으로 장기 스케줄러와 마찬가지로 메모리에 올라와 있는 프로세스의 수를 조절하는 역할을 함.
    
<br>

### ⚙️ Swapping

- Swap-out : 메모리에서 프로세스를 제거하여 디스크에 저장
- Swap-in : 실행을 계속하기 위해서 디스크에서 메모리로 다시 적재

→ 중기 스케줄러로 인해 프로세스의 상태에서 **`Suspended`** 상태 추가.

<img width="539" alt="os10" src="https://user-images.githubusercontent.com/63224278/157997339-5d27327f-508c-4857-9e50-003843baa7f8.png">

- 프로세스가 자신의 코드를 실행하다가 System call 발생 등으로 user mode에서 kernel mode로 진입하여 운영체제의 코드를 실행한다 하더라도, 그 사용자 프로세스가 kernel mode(운영체제의 모드)로 Running 중인 것이지 운영체제가 Running되는 것이 아니다.
    
    

<img width="348" alt="os11" src="https://user-images.githubusercontent.com/63224278/157997342-4c204a07-d279-446e-b2d2-7932e2b854ff.png">

`inactive`: Process가 얼어붙어서 정지되어있는 상태 

- **`Suspended`** 상태가 되었다는 것은 메모리를 완전히 잃어버리는 (swap-out)상태이기 때문에 CPU는 아무런 일을 할 수 없다. (하지만 별개로 I/O는 실행될 수 있으므로 이 때 I/O 작업이 완료되면 Suspended Ready상태로 넘어갈 수 있는 상황이긴 하다.)

<br>

## 💡 Thread

```
💬 Process가 하나 주어지면 그 프로세스마다 독자적인 Code, data, stack 주소공간이 만들어지고 PCB에서 현재 메모리에서 어떤 부분을 수행중인지 PC가 가리키고 있음.

→ 동일한 일을 하는 프로세스가 여러개 있다고 할 때, 이 프로세스를 별도로 만들면 주소공간도 별도로 만들어지기 때문에 메모리 공간이 낭비됨 
→ 프로세스를 여러개 띄워두고 싶을 때 메모리 낭비를 막기 위해 프로세스를 하나만 띄워두고 (메모리 공간(주소공간)은 하나만 만들어두고) 각 프로세스마다 다른 코드를 실행할 수 있게 해주면 어떨까? ⇒ '스레드'의 개념!
```

<img width="544" alt="os12" src="https://user-images.githubusercontent.com/63224278/157997347-b5e31fde-5838-45eb-8ab0-3805fac934c6.png">

<br>

### Thread란?

→ CPU를 수행하는 단위.

프로세스 하나에 CPU 수행 단위만 여러개를 두고 있는 것을 Thread라고 함.

<img width="200" alt="os13" src="https://user-images.githubusercontent.com/63224278/157997349-b9aebcb9-502f-4e42-822b-9f1085309a89.png">

<img width="688" alt="os14" src="https://user-images.githubusercontent.com/63224278/157997352-f2f3fd9c-753d-402a-bbca-fb9a5a4f88ab.png">

- **Thread의 구성**
    - Program counter (PC)
    - register set
    - stack

- **Thread가 동료 thread와 공유하는 부분(=task)**
    - code
    - data
    - OS 자원

- 전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.

```
💬 Thread에서는, 프로세스간 공유할 수 있는 자원은 최대한 공유함 (code, data, Heap 등)

- 다만, CPU 수행과 관련된 부분(PC, register, stack)은 독자적으로 가짐
```

<br>

### Thread의 장점

- **`빠른 응답성(Responsiveness)`** 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 스레드가 blocked(waiting)상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있다.

```
💬 네트워크를 통해서 웹페이지를 읽어오는 작업도 사실은 I/O 작업임. 네트워크를 읽어오는 동안에는 오래걸리기 때문에 내 웹 브라우저는 blocked상태가 됨 → 웹페이지를 다 읽어올 때 까지 화면에 아무것도 보이지 않기 때문에 사용자는 답답함 → 웹 브라우저를 여러개의 스레드를 사용해 프로그램을 만들어놓게 되면 하나의 스레드가 멀리 네이버 웹 서버에서 웹 페이지의 그림같은 것을 불러오는 동안에 스레드를 blocked시키지 않고 또다른 스레드가 이미 읽어온 text라도 띄워 display를 해준다면 사용자가 결과를 더 빨리 볼 수 있게 됨. (빠른 응답성 제공)
```

- **`자원 절약(공유)`** 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.
- **`경제성`** 프로세스를 하나 만드는 것은 오버헤드가 크지만, 프로세스 하나 안에다 스레드를 만드는 것은 오버헤드가 크지 않다. 또한 프로세스 간 문맥교환도 오버헤드가 크지만, 스레드 간 문맥교환은 오버헤드가 크지 않기 때문에 스레드를 이용하면 경제성이 높다.
- **`병렬성`** 스레드를 사용하면 병렬성을 높일 수 있다.

<br>

### Thread 구현 방법

### 1. Kernel Threads

- Thread가 여러개 있다는 사실을 운영체제(kernel)가 알고 있음. 따라서 커널이 CPU 스케줄링을 하듯이 넘겨줌.

### 2. User Threads

- Library를 통해 지원됨.
    - kernel이 모르고 있음.

### 3. real-time Threads



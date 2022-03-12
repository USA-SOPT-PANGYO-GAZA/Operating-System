## 프로세스 생성

**Q. 누가 프로세스를 생성하는가?
A. 부모 프로세스가 자식 프로세스를 생성**

- 사용자 프로세스가 다른 프로세스를 직접 생성하는 건 아니고 운영체제를 통해서만 생성 가능 
→ **fork(), exec() (System Call)**

**Q. 생성 방식?**

  **A. 복제 생성**

**✅  *하나의 부모 프로세스를 복제 생성을 통해서 여러 자식 프로세스를 생성한다.***

▶️  프로세스의 계층 구조가 **트리 구조로 형성**된다.

<br/>

- **프로세스는 자원을 필요로 한다.**
    - 운영체제로부터 자원을 받는다.
    - 부모 프로세스와 자원을 공유한다.(경우에 따라서) → 원칙적으로는 공유하지 않음
        - 별도의 프로세스이기 때문에 자원을 가지고 경쟁하는 사이이다.
        - 자원을 공유하지 않는게 일반적이다.
- **부모, 자식 프로세스의 모델**
    
    
    | 자원의 공유 | 수행(Execution) |
    | --- | --- |
    | 부모, 자식이 모든 자원을 공유하는 모델 | 부모와 자식이 공존하며 수행되는 모델 |
    | 일부를 공유하는 모델 | 자식이 종료될 때까지 부모가 기다리는 모델<br/>- 부모가 Blocked 상태 |
    | 전혀 공유하지 않는 모델 |  |
    - 가능하면 부모의 자원을 공유하는게 효율적이지만 별개의 프로세스이기 때문에 **독립적으로 갖는 것이 원칙**

<br/>

**Q. 부모가 자식을 어떤 방식으로 생성하느냐?**

1️⃣  부모 프로세스의 주소 공간과 OS 데이터(PCB, 자원들)를 자식 프로세스가 복사를 한다. → **fork()**

2️⃣  복제된 자식 프로세스에 새로운 프로그램을 덮어씌운다. → **exec()**

- 복제가 안된 상태에서 exec()를 실행하면 해당 프로세스가 다른 프로그램으로 덮어씌워진다.

**copy-on-write(COW)**

<aside>

🗂️ **Write가 발생했을 때, Copy를 하겠다.**

</aside>

- 내용이 바뀔 때 Copy해서 새로운 걸 만들고 이전까지는 부모의 자원을 공유
    - 부모의 Code, Data, Stack를 통째로 Copy 하는 것이 아니라 물리적 메모리에 올라가는 잘게 쪼개진 부분만 Copy하게 된다.

<br/>

## 프로세스 종료

**종료 과정(자발적)**

<aside>

🗂️ 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려준다. ▶️  **exit()**

</aside>

1️⃣  자식이 부모 프로세스에게 Output Data를 보낸다. → **wait()**

- 프로세스의 세계에서는 **자식이 먼저 종료**된다.

2️⃣  프로세스의 각종 자원들이 운영체제로 반납된다.

**종료 과정(비자발적)**

<aside>

🗂️ 부모 프로세스가 자식의 수행을 종료시킨다. ▶️  **abort()**

</aside>

- 자식이 **할당 자원의 한계치를 넘어서는** 경우
- 자식에게 **할당된 Task가 더 이상 필요하지 않을** 경우
- **부모 프로세스가 종료(exit)**되는 경우
    - 자식 프로세스가 먼저 죽는 것이 원칙이기 때문에, 자신이 생성한 모든 자식 프로세스를 종료하고 나서 종료된다.
    - 단계적인 종료 ▶️  트리 밑 부분부터 **단계적으로 종료**된다.
    
<br/>

## System Call

### fork() 시스템 콜 → create a child(copy)

```c
int main() {
	int pid;
	pid = fork(); // System Call

	if (pid == 0)
		print("\n Hello, I am child!\n");
	else if (pid > 0)
		print("\n Hello, I am parent!\n");
}
```

- 자식 프로세스는 부모 프로세스의 자원을 그대로 Copy하기 때문에, 문맥(PC)도 동일하게 Copy하게 된다. **결론적으로 자식은 fork()이후부터 실행하게 된다.**

💾  **문제점**

1. 자식 프로세스가 자신이 원본이라고 주장한다.
2. 부모와 동일한 프로세스가 만들어지니깐 모든 프로그램이 동일한 제어 흐름을 따라야 할 것이다.

**✅ 복제본을 만들면 fork()함수의 return value로 부모 프로세스인지, 자식 프로세스인지 알 수 있다.**

- 부모 프로세스 - 양수(자식 프로세스의 pid)
- 자식 프로세스 - 0

**만약 코드가 이렇게 생겼다면?**

```c
int main() {
	int pid;
	print("\n Hello, System Call!");
	pid = fork(); // System Call

	if (pid == 0)
		print("\n Hello, I am child!\n");
	else if (pid > 0)
		print("\n Hello, I am parent!\n");
}
```

| 부모 프로세스 | 자식 프로세스 |
| --- | --- |
| 1️⃣  print("\n Hello, System Call!"); | 1️⃣  print("\n Hello, I am child!\n"); |
| 2️⃣  fork() 실행 |  |
| 3️⃣  print("\n Hello, I am parent!\n"); |  |

### exec() 시스템 콜 → overlay new image

<aside>

▶️ **다른 시스템을 실행하기 위해서 존재한다. 
어떤 프로그램을 완전히 새로운 프로세스로 태어나게 하는 역할을 한다.**

</aside>

```c
int main() {
	int pid;
	print("\n Hello, System Call!");
	pid = fork(); // System Call

	if (pid == 0) {
		print("\n Hello, I am child!\n");
		execlp("/bin/date", "/bin/date", (char *) 0);
	}
	else if (pid > 0)
		print("\n Hello, I am parent!\n");
}
```

- exec() 시스템 콜이 실행되면 전에 실행하던 프로그램은 잊고 완전히 **새로운 프로그램으로 태어난다**.
    - 프로그램의 시작부분부터 다시 시작하게 된다.
    - 전에 실행되던 프로그램으로 돌아올 수 없다.
    

**꼭 exec()이라는 것이 자식을 만들어야만 가능한 것이 아니다.**

```c
int main() {
	print("1");
	execlp("echo", "echo", "Hello New Process!", (char*) 0);
	print("2");
}
```

- exec() 이후에 있는 `print("\n Hello, I am parent!\n");` 는 영원히 실행이 안된다.

### wait() 시스템 콜 → sleep until child is done

<aside>

▶️ **프로세스를 block 상태로 보낸다. 잠들게 한다.**

</aside>

<img width="600" alt="스크린샷 2022-03-07 오후 2 54 26" src="https://user-images.githubusercontent.com/55099365/158005238-7b296576-bf74-4a6e-bf65-3f02e29d9510.png">

1️⃣  자식 프로세스를 생성하고 나서 자식 프로세스가 종료되길 기다리면서 **blocked 상태**가 된다.

2️⃣  자식 프로세스 종료

3️⃣  부모 프로세스가 **ready 상태**로 바뀌고 CPU를 얻을 수 있게 된다.

ex) 프롬프트에서 프로그램을 실행한 뒤에 해당 프로그램이 종료되길 기다렸다가 종료가 되면 프로그램 입력 커서가 다시 생성됨.

→ 프로그램이 끝날 때까지 부모 프로세스인 프롬프트가 wait()하고 있었다는 걸 알 수 있음.

### exit() 시스템 콜 → frees all the resources, notify parent

<aside>

▶️ **프로그램을 종료시킬 때 실행하는 시스템 콜이다.**

</aside>

```c
int main() {
	int pid;
	print("\n Hello, System Call!");
	exit()
	pid = fork(); // System Call

	if (pid == 0)
		print("\n Hello, I am child!\n");
	else if (pid > 0)
		print("\n Hello, I am parent!\n");
}
```

`print("\n Hello, System Call!");` 만 실행되고 끝나버린다.

**자발적 종료**

- 마지막 Statement 수행 후 exit() 시스템 콜을 통해
- 프로그램에 명시적으로 적어주지 않아도 main()가 리턴되는 위치에 컴파일러가 넣어준다.

**비자발적 종료(외부에 의해 종료)**

- 부모 프로세스가 자식 프로세스를 강제 종료시킨다.
    - 자식 프로세스가 한계치를 넘어서는 자원을 요청했을 경우
    - 자식에게 할당된 Task가 더 이상 필요하지 않을 경우
- 키보드로 kill, break 등을 친 경우
- 부모가 종료하는 경우
    - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료된다.
    

## 프로세스 간 협력

**[ 프로세스는 굉장히 독립적이다 ]**

**독립적 프로세스(Independent process)**

: 태어난 자식 프로세스는 스스로 알아서 수행해야 한다. 부모 프로세스와 경쟁을 하며 실행되어야 한다.

: 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못한다.

**[ 협력을 해야만 효율적인 실행이 된다 ]**

**협력 프로세스(Cooperating process)**

: 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있다.

**Q. 협력 프로세스에서 말하는 협력이란?**

A. 정보를 주고 받으면서 실행을 하는 것 → 이를 위한 메커니즘 존재(IPC)

### Interprocess Communication(IPC)

<aside>

💾 **프로세스 간 협력 메커니즘**

</aside>

![스크린샷 2022-03-12 오후 2 31 40](https://user-images.githubusercontent.com/55099365/158005271-29c9b342-b506-46e2-960b-816b714bec26.png)

- **Message Passing** : 프로세스 A가 프로세스 B에게 **메시지를 전달하는 방법**
    - 프로세스는 독립적이기 때문에 자기 메모리 주소 공간만 볼 수 있다.
    - 프로세스가 다른 프로세스에게 메시지를 전달할 방법이 원칙적으로는 없다.
    - 프로세스 사이에 **공유 변수를 사용하지 않기** 때문에 **메시지를 이용해서 통신를 하는 시스템**이다.
    
    ✅  ***따라서 커널을 통해서 메시지를 전달한다.  → 커널 : 메신저 역할***
    
    **Direct Communication**
    
    : 상대 메시지 프로세스의 이름을 **명시적**으로 표시
    
     ![스크린샷 2022-03-12 오후 2 32 13](https://user-images.githubusercontent.com/55099365/158005289-b5e940ae-b5ac-44c9-abe6-ee26f3534092.png)
    
    **Indirect Communication**
    
    : mailbox(또는 port)를 통해 메시지를 **간접 전달**
    
    ![스크린샷 2022-03-12 오후 2 32 34](https://user-images.githubusercontent.com/55099365/158005301-358fa15e-f67d-40fe-b584-32b855efa1c9.png)

<br/>

![스크린샷 2022-03-12 오후 2 33 09](https://user-images.githubusercontent.com/55099365/158005318-d055a9cd-adec-4671-919e-6580ab0ebd3e.png)

- Shared Memory : 서로 다른 프로세스 간에도 **일부 주소 공간을 공유**
    - 물리적인 메모리에 매핑할 때 **일부 영역은 공유되도록 매핑**
        - 프로세스 A가 어떤 내용을 shared memory에 넣으면 프로세스 B도 그 내용을 바로 전달받아서 볼 수 있게 된다.
    - 프로세스끼리 메모리 공간을 공유하기 위해선 **커널에게 shared memory를 사용한다는 System Cal**l를 해서 share하게 해두고 그 때부터 사용자 프로세스끼리 영역을 공유할 수 있게 된다.
        - 처음에는 kernel의 도움을 받지만 한 번 도움을 받고 나면 사용자 프로세스들끼리 공유하면 된다.
        
        ✅  ***따라서 신중하게 shared memory 영역을 사용해야 한다. → 두 프로세스가 상당히 신뢰하는 관계***

- thread : thread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread들간에는 주소 공간을 공유하므로 협력이 가능
    
    ✅  ***프로세스 간의 협력은 아니다. 하지만 thread들끼리의 공유는 쉽다.***
    
    **Q. thread들끼리 공유가 쉬운 이유?**
    
    A. thread들끼리 주소 공간을 완전히 공유하고 있기 때문에
    
<br/>   


## CPU and I/O Bursts in Program Execution

![스크린샷 2022-03-12 오후 2 33 44](https://user-images.githubusercontent.com/55099365/158005334-32c1f626-6a76-47c4-ab27-2c66f9f37567.png)

| Process | State |
| --- | --- |
| CPU burst | load store<br/>add store<br/>read from file<br/>→ I/O 오래 걸리는 작업 |
| I/O brust | wait for I/O  |
| Ready Queue | Ready State |
| CPU burst | store<br/>increment<br/>index<br/>write to file |
| I/O brust | wait for I/O  |
| Ready Queue | Ready State |
| CPU burst | load store<br/>add store<br/>read from file |
| ... | ... |

**CPU burst**

: CPU만 연속적으로 쓰면서 instruction를 실행하는 단계

**I/O burst**

: I/O만 사용하는 단계

✅  ***프로그램이 실행된다는 것은 CPU burst와 I/O burst를 반복적으로 수행하는 것***

- 프로그램의 종류에 따라서 두 가지가 빈번할 수도, 하나만 진득하게 나올 수도 있다.
- **두 가지가 빈번한 프로그램** : 사용자가 많이 끼어드는 프로그램, Interactive Job
    - CPU burst가 짧아지고 I/O burst가 많아진다.
- **하나만 진득하게 프로그램** : 중간에 사람의 Interaction이 들어오지 않는 프로그램, CPU를 사용해서 오랫동안 계산을 해야하는 프로그램

### CPU-burst Time의 분포

![스크린샷 2022-03-12 오후 2 34 06](https://user-images.githubusercontent.com/55099365/158005345-b9d43bc8-0660-4d09-8dfc-a3b68fc8552c.png)

- **I/O bound job** : CPU를 아주 짧게 쓰고 중간에 I/O가 끼어드는 작업
    - **many short** CPU bursts
- **CPU bound job** : CPU만 아주 오랫동안 사용하는 작업, **Computing bound job**
    - **few very long** CPU bursts

▶️  **컴퓨터 내부에는 여러 종류의 process가 섞여있기 때문에 CPU 스케줄링이 필요하다.**

▶️  **CPU의 시간은 CPU bound job이 많이 사용하고 CPU를 사용하는 빈도는 I/O bound job이 많다.**

<aside>

🗂️ **CPU bound job은 한 번 잡은 CPU를 잘 놓지 않는다.** 
이로 인해 I/O bound job를 사용하는 사용자가 CPU가 job으로 부여되지 않아서 답답함을 느낀다. 
이런 문제점을 해소하기 위해서는 CPU 스케줄링을 통해서 I/O bound job에게 적절한 response를 제공해줘야 한다.

- 공평함보다는 효율성이 우선
- Interactive한 job이 너무 오래 기다리게 하면 안됨

</aside>

**CPU 스케줄링의 주요 이슈** 

누굴 우선해서 줄 것인가? 얼만큼의 시간을 주고 CPU를 뺏어야 하는가?

**CPU 스케줄링의 주요 목적**

CPU를 할당받을 프로세스를 잘 골라 실행시켜줌으로써 전체적으로 시스템의 성능을 높이자는 것이 스케줄링의 목적

- **Process, Job?**
    
    Job = Process ≥ Task(small size Process) ≠ Thread(inside Process) > Transaction(one-time)
    

## CPU Scheduler & Dispatcher

**CPU Scheduler**

: Ready 상태의 프로세스 중에서 이번에 CPU를 줄 **프로세스를 고른다**.

**Dispatcher**

: CPU를 어떤 프로세스에게 줄 지 결정했으면 그 프로세스에게 **CPU를 넘겨주는 역할**을 하는 운영체제 커널 코드

- CPU를 넘겨주기 위해서 넘겨주기 전에 실행하던 프로세스의 context를 저장해야 함. → ***문맥 교환***

**Q. CPU Scheduler는 어떻게 생겼나요?**

A. **운영체제 안에서 CPU Scheduling를 하는 코드를 CPU Scheduler라고 이르는 것**, Dispatcher도 동일. 운영체제에서 특정 기능을 하는 것들을 이런 식으로 부르는 것이다.

**Q. CPU 스케줄링은 언제 필요한가?**

1. **`Running → Blocked`** : I/O 요청이 들어와서 I/O를 하러간 경우
    - 자진해서 CPU를 내어놓는 경우
2. **`Running → Ready`** : CPU를 계속 사용하고 싶은데 Timer Interrupt 때문에 다시 Ready Queue로 돌아간 경우
3. **`Blocked → Ready`** : I/O 완료 후에 Device Controller가 Interrupt를 걸어서 상태를 Ready로 변경된 경우
    - I/O가 끝나고나서 바로 Running으로 가지 않는다. **하지만 넘겨야 하는 경우 존재**
        - **우선순위**에 기반한 스케줄링의 경우
4. **`Terminate`** : 프로세스가 종료되어서 할 일이 없다보니 다른 프로세스에게 CPU를 넘겨줘야 하는 경우

▶️  1, 4는 CPU를 가지고 있어도 더이상 instruction를 실행하지 못해서 **자진 반납하는 경우**이다. 

**= nonpreemptive**

▶️  2, 3은 CPU를 계속 사용하고 싶은데 번갈아 사용해야해서 **강제로 CPU를 빼앗긴다**.

**= preemptive**

## ****How does iOS 10 schedule its CPU****

At a high level, there are real time threads (which run until they block) and time share threads, which have a base priority. The time share thread with the highest priority runs on the CPU but, as it runs, its priority decays. Eventually that priority drops below that of some other time share thread, and the CPU switches threads.

[How does iOS 10 schedule its CPU](https://developer.apple.com/forums/thread/68280)

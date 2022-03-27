## 복습

✔️ cpu 스케줄링이 필요한 이유? 여러 종류의 job이 섞여 있으므로 !

✔️ 결국에는 Round Robin 

- 효율적 + 공정한 방법
- CPU를 주고 나서 할당된 시간을 미리 세팅 >> 그 시간만큼 쓰고 >> 뺏어와서 다음에 줄서있는 프로세스에게 CPU 할당
- CPU를 기다리는 시간이 CPU를 사용하는 시간에 비례 (→ 그런 면에서 공정한 스케줄링이라고 볼 수 있음)

✔️ 왜 좋은가? process context를 아낄 수 있고, 다시 재개할 수 있는 메커니즘이므로 

## Multilevel Queue

(그동안) Ready Queue에 CPU를 기다리는 방식 → 한줄 서기 >> 여러줄로 설 수 있음 !! >> CPU는 1개인데 줄이 여러개이므로 *고려할 것들이 있음

*무엇을 고려?

1. 프로세스를 어느 줄에?
2. 어느 줄에 우선권을 주는가?

→ 줄이 정해져 있고, 프로세스가 우선순위에 따라서 줄을 서는 것 

>> CPU가 어떻게 할당을 하는가?

>> 우선순위가 높은 줄에 먼저 할당 (출신에 따라서 .. 영원히 계급을 극복하지 못함 .. ㅠ)

- Ready Queue를 여러개로 분할
    - foreground → interactive
    - background → batch (사용자와의 인터랙션 없이 CPU를 사용)
- 각 줄(큐)마다 스케줄링 방식이 필요
    - foreground → RR (사용자와의 인터랙션이 있으므로)
    - background → FCFS (문맥교환을 줄이기 위해서)
- 줄(큐) 안에서 누구에게 CPU를 줄 것인가? 어떻게 우선순위를 매길 것인가?
    - Fixed Priority Scheduling
        
        우선 순위가 높은 줄부터 CPU 할당 
        
        → starvation 현상이 발생할 수 있음 
        
    - Time Slice
        
        각 큐에 CPU 시간을 적절한 비율로 할당 
        
        (ex. 전체 CPU 시간의 80%는 우선 순위가 높은 큐에 할당, 20%는 우선 순위가 낮은 큐에 할당)
        

~~.. 공정하지 않고 차별적임 ..~~ 

## Multilevel Feedback Queue

→ 우선순위가 바뀔 수 있음 (.. like 현대사회)

- 프로세스가 다른 줄(큐)로 이동 가능
- aging을 이와 같은 방식으로 구현 가능
- Multilevel Feedback Scheduling을 정의하는 파라미터
    - Queue의 수
    - 각 큐의 스케줄링 알고리즘
    - 프로세스를 상위 큐로 보내는 기준
    - 프로세스를 하위 큐로 내쫓는 기준
    - 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준
    
    .. 이러한 기준에 따라서 우선순위가 결정
    
    >> 보통은? 처음 들어오는 프로세스는 우선순위가 높은 큐(CPU 할당시간이 짧음)로 → 아래 큐로 강등 → .. 마지막 큐는 FCFS로 
    
    >> CPU 사용 시간이 짧으면 처음에 들어와서 사용하고 바로 나가, 오래 걸리면 다음 큐로 이동하면서 CPU 할당 시간은 긴데, 우선 순위가 낮도록 
    
    >> 사용 시간 예측 필요 없음 + CPU 사용시간이 짧은 프로세스가 우선시
    

## 특이한 케이스에서의 CPU 스케줄링

### Multiple-Processor Scheduling (= CPU가 여러 개일 때)

- CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐
- Homogeneous Processor :
- Load Sharing : 특정 CPU만 일하면 안되고 적절히 분배가 되어야 함
- Symmetric Mulitiprocessing : 각 프로세서가 알아서 스케줄링 결정 (모든 CPU가 동등)
- Asymmetric Mulitiprocessing : 하나의 CPU가 전체적인 컨트롤을 담당, 하나의 프로세서가 시스템 데이터의 접근/공유를 책임지고 나머지 프로세서는 거기에 따름

### Real-Time Scheduling (= Deadline이 정해진 스케줄링)

**Hard real-time systems**

정해진 시간 안에 **반드시 끝내도록** 스케줄링

**Soft real-time computing**

반드시 지키기보다, 일반 프로세스에 비해 높은 우선순위를 갖도록 해야 함 >> deadline을 보장x

### Thread Scheduling

- 쓰레드
    
    프로세스 하나 안에 CPU 수행 단위가 여러개 .. 
    
1. User Level 쓰레드 → Local Scheduling 
    
    : 사용자 수준의 쓰레드 라이브러리에 의해 어떤 쓰레드를 스케줄링할 지 결정 (사용자 프로세스가 직접)
    
2. Kernel Level 쓰레드 → Global Scheduling
    
    : 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 쓰레드를 스케줄링할 지 결정 
    

## Algorithm Evaluation
(= 어떤 알고리즘이 좋은지 평가하는 방법)

1. Queueing models (이론적인 방법)
    
    : 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산
    
2. Implementation & Measurement (실제 시스템에 구현, 돌려보고 성능 측정, 실측)
    
    : 실제 시스템에 알고리즘을 구현하여 실제 작업에 대해서 성능을 측정 
    
3. Simulation 
    
    : 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교
    
---

## 데이터의 접근

컴퓨터 시스템 안에서 데이터가 접근되는 패턴
데이터가 있고 >> 그 데이터를 갖고 와서 >> 연산 처리 >> 연산 결과 반영 

(누가 먼저 읽는지에 따라서 문제가 발생할 수 있음)

---

## Race Condition
→ S-box를 공유하는 E-box가 여러개 있는 경우, Race Condition의 가능성이 있음

→ 원하지 않은 결과가 나올 수 있음

.. 과연 컴퓨터 내에서 많이 생기는 문제인가? mulitiprocessor system / 공유 메모리를 사용하는 프로세스들 / **커널 내부 데이터를 접근**하는 루틴들 간 (ex. 커널 모드 수행 중 인터럽트로 커널 모드 다른 루틴 수행시) 

(유저 레벨에서는 큰 문제가 생기지 않지만, 커널 레벨에서는 생김) 

---

## OS에서의 race condition

### 예제 1 - Interrupt handler VS kernel
→ 커널 모드 실행 중 인터럽트가 발생 >> 인터럽트 처리 루틴이 수행 (→ 양쪽 다 커널 코드이므로 커널 주소 공간을 공유)

<aside>
🤔 어떻게 문제를 해결하는가?

- 중요한 값을 건들이는 동안에는
- 인터럽트가 들어와도
- 인터럽트 처리 루틴으로 가는 것이 아니라
- 해당 작업이 끝날 때까지는 인터럽트 처리를 하지 않음
</aside>

📌 **결국에는, 순서를 정해주면 됨 !!**

### 예제 2- Preempt a process running in kernel?
→ 어떠한 프로세스 실행? 본인의 코드만 실행되는 것이 아니라, 시스템 콜을 통해서 구현되는 것도 있음 (= 유저모드와 커널모드를 번갈아가면서 실행)

→ 실제로는 프로세스 A와 B 모두 커널에 접근해서 ++을 하므로, 2가 증가해야 하지만 커널 접근 도중에 인터럽트가 되어서 B의 커널 모드가 실행되므로 하나가 반영되지 않음

<aside>
🤔 **어떻게 문제를 해결하는가?**
프로세스가 커널 모드에 있을 때는 할당 시간이 끝나도, CPU를 빼앗지 않도록 (→ 커널 모드에서 사용자 모드로 돌아갈 때 preempt)

</aside>

### 예제 3 - mulitiprocessor
→ CPU가 여러 개 있는 환경 : 앞의 방법 그 어떤 것으로도 해결 되지 않음 

<aside>
🤔 **어떻게 문제를 해결하는가?**

1. CPU가 데이터를 들고 갈 때 lock을 걸어야 함 → 데이터 과정이 끝나면 lock을 풀기 (개별 데이터에 대해서 lock을 걸고 풀기)

2. 커널을 접근하는 CPU를 1개로 제한

</aside>

---

## Process Synchronization 문제

- 공유 데이터의 동시 접근은 데이터의 **불일치 문제**를 발생시킬 수 있음
- 일관성 유지를 위해서는 협력 프로세스 간의 **실행 순서를 정해주는 메커니즘** 필요

**Race Condition**

- 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
- 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐

→ **concurrent process는 `동기화` 되어야 함**

## 용어

### 임계구역 (= Critical Section)
n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우, 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 critical section이 존재

📌 문제 📌

하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 함

→ 자유로워졌을 때 공유 데이터에 접근할 수 있어야 함 

📌 해결 📌

... 다음 시간에 .. 

****

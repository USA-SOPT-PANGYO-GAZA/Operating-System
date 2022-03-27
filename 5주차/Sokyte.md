## CPU and I/O Bursts in Program Execution

프로그램에 따라서 조금씩 차이가 있지만, 공통적으로 

`CPU burst` 🔁 `I/O burst`

(CPU만 사용) .. (I/O만 사용) 서로 번갈아가면서 .. 

### CPU - burst Time의 분포
.. 여러 종류의 job (= process)가 섞여 있기 때문에 CPU 스케줄링이 필요

프로세스 특성에 따라 두가지로 분류

- I/O bound process : CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job (짧고 빈번)
- CPU-bound process : 계산 위주의 job

I/O job은 주로 사용자와 interactive하는 job 

→ 만약 CPU bound job이 오래 잡고 있으면? 사용자는 오래 기다려야 함 

→ 효율성을 생각해야 함 ..

⇒ CPU 스케줄링이 필요한 이유 

✔️ 누구한테, 우선으로 줄 것인가?

✔️ 언제 뺏을 것인가?

### CPU Scheduler & Dispatcher

**CPU Scheduler**

하드웨어? 소프트웨어? .. 운영체제 안에 있는 코드 

Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고름 

**Dispatcher**

CPU 제어권을 CPU shceduler에 의해 선택된 프로세스에게 넘김 → 이 과정을 문맥교환 (context switch) 라고 함 

🤔 CPU 스케줄링이 필요한 경우? 

~~.. 여러 경우가 있음 구구절절 할 필요는 없음 ..~~ 

: 프로세스에게 다음과 같은 **상태 변화**가 있는 경우

1. 오래 걸리는 작업(I/O 작업 등의)을 하러 간 경우 
2. 빼앗고 다른 프로세스에게 넘기는 경우 .. (time interrupt)
3. 1번의 작업이 완료된 경우 .. 당장 CPU를 얻으면 일이 끝나는 경우 (I/O 작업이 완료된 후)
4. 프로세스 종료 후 CPU 넘기는 경우 

→ 1, 4에서의 스케줄링은 자진반납-오래 걸리거나, 할 일이 끝났거나 (= **nonpreemptive**) .. 그 외는 강제로 (= **preeptive)**

## CPU and I/O Bursts in Program Execution

컴퓨터 프로그래밍이 실행될 때, 일련의 단계가 실행 (프로그램 종류마다 조금씩 다름 .. CPU burst 가 많은 프로그램 .. I/O burst 가 많은 프로그램 ..

→ 두가지 단계가 번갈아 가면서 실행 

(모든 프로그램 별로 다른 단계를 갖고 있기 때문에, CPU 스케줄링이 필요함)

: 현재 ready queue에 들어있는 프로세스 중에서 어떤 프로세스에 CPU를 줄 것인지에 대한 ..

크게 두가지 이슈

1. 누구한테 지금 당장 CPU를 줄 것인가
2. CPU를 주었다면, 언제까지 주어야 하는가

비선점형/선점형 

→ 어느 것이 좋은 것인가 평가할 수 있어야 함 .. : 성능 척도

## 성능척도

크게 두가지 입장에서 볼 수 있음

### 시스템 입장에서

CPU 하나로 최대한 일을 많이 시키는 것 

- 이용률 (CPU utilization)
    
    전체 시간 중 CPU가 일한 시간의 비율 
    
    CPU는 가능한 바쁘게 일하고 있어야 함 
    
- 처리량 (Throughput)
    
    주어진 시간에 일을 몇개를 처리했는가
    

### 프로그램 입장에서

CPU를 빨리 얻어서, 빨리 끝나는 것 (→ 시간과 연관) .. 시간의 개념을 3가지로 나눔

- 소요 시간, 반환 시간 (Turnaround Time)
    
    CPU를 쓰러 **들어와서, 다 쓰고 나갈 때까지** 걸린 시간을 의미 
    
- 대기 시간 (Waiting Time)
    
    CPU를 얻을 때까지 **기다린** 시간 (줄 서서 기다린 시간)
    
- 응답 시간 (Response Time)
    
    ready queue에 들어와서, **처음으로 CPU를 얻기까지** 걸린 시간 
    

<aside>
🤔 **Waiting Time? Response Time?**
쓰다가 다시 CPU를 빼앗길 수 있음 .. CPU를 얻었다가 .. 빼앗겼다가 .. 할 수 있음
→ 이 기다린 시간을 모두 합친 것이 waiting time

최초로 CPU를 얻기까지 기다린 시간 .. 사용자 입장에서 처음으로 응답을 받은 것 
→ response time

</aside>

## CPU 스케줄링 알고리즘

### FCFS

비선점형 → 내놓을 때까지는 빼앗지 않음

- 먼저 온 순서대로 처리
- Convey Effect : 처리시간이 긴 프로세스가 먼저 도착하게 되면, 뒤에서 계속 밀리게 되는 현상

### SJF (Shortest Job First)

- CPU burst time이 가장 짧은 프로세스를 가장 먼저 스케줄
- 평균 대기 시간이 가장 짧음
- 2가지 방식
    - Nonpreemptive
        
        일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점 당하지 않음 (해당 프로세스가 내놓기 전까지 갖고 있음)
        
    - Preemptive
        
        CPU를 주었다가 하더라도, 더 짧은 프로세스가 도착하면 그 프로세스에게 넘겨줌 
        
        새로운 프로세스가 도착하면, 언제든지 스케줄링이 변경될 수 있음 
        
        = Shortest Remaining Time First (SRTF) 라고 부르기도 함 
        
- SJF is Optimal → 주어진 프로세스에 대해 최소 대기 시간을 보장 (preemptive 버전의 경우에 해당)

<aside>
🤔 **제일 좋은 것 아닌가?**
2가지 이슈가 있음
****1. Starvation
CPU 사용 시간이 긴 프로세스는 평생 기다리기만 해야 함 
Long process는 starvation이 될 수 있음

2. CPU 사용시간을 미리 알 수 없음
실제로는 CPU 사용시간을 알 수 없기 때문에 
→ 그러나, **추정은 가능**

</aside>

<aside>
👀 **다음 CPU Burst Time의 예측**
(다음번 CPU burst time을 어떻게 알 수 있는가?)
****→ **추정**만 가능 
→ 과거의 CPU burst time을 이용해서 추정

</aside>

### Priority Scheduling

- 우선순위가 높은 프로세스에게 CPU를 주는 방식 (숫자가 작을 수록 우선순위가 높은 것으로 고려)
- 마찬가지로 nonpreemptive 방식과 preemptive 방식으로 나눠서 생각할 수 있음
- SJF처럼 starvation 문제가 발생함
    
    → Starvation 문제를 어떻게 해결하는가?
    
    → **Aging 기법으로 해결할 수 있음 : 언젠가는 CPU를 얻게 되므로** 
    

### Round Robin (RR)

현대적인 CPU는 라운드로빈 방식을 기반으로 작동

- 각 프로세스는 동일한 크기의 할당 시간 (time quantum) 을 갖고 있음
- 할당 시간이 지나면 프로세스는 선점을 당하고 ready queue의 제일 뒤로 가서 다시 줄을 섬
- 응답 시간이 빠름 (어떤 프로세스여도 CPU를 한번씩은 갖게 됨)
- CPU 사용시간을 미리 알 수 없다고 해도, CPU 사용시간이 적은 프로세스는 할당된 시간 동안 빠르게 작업을 하고 나갈 수 있음 → 굳이 예측할 필요 없음
- 할당시간이 너무 많으면? FCFS
- 할당시간이 너무 짧으면? context switch가 너무 많아지므로 오버 헤드 발생

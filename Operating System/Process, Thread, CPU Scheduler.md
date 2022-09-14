# TL;DR
운영체제란 HW를 효율적으로 관리하는 소프트웨어로, 시스템 입장에서는 자원 할당자다.<br>

<br> 컴퓨터 발전 과정에서 
1. 여러 개의 프로그램을 메모리에 올려둘 수 있게 됐고 (bigger and cheaper memory 발전),
2. User interactive 환경이 대두되면서(모니터/키보드 등), I/O bounded job 이 많아졌다.

<br> CPU를 더욱 효율적으로 사용하기 위한 방법이 모색됐고, 이를 CPU Scheduling 이라고 한다.
<br> 운영체제는 kernel data 영역에 각 프로세스를 관리하기 위한 정보인 PCB를 가지고, 
Scheduling Algorithm에 따라 CPU를 프로세스에 할당한다.
<br> 이 과정에서 process state, process context, context switch 등의 개념이 나온다.<br>

<br> Thread는 프로세스 내부에서 CPU를 수행하는 단위로, 동일한 프로세스를 여러개 실행해야할 때, 
주소 공간을 공유해 메모리 낭비를 막고 프로세스를 효율적으로 수행하게 하기 위한 기법이다.


# Process
Process 란 실행 중인 프로그램으로 job, task, sequential process 라고도 불린다.
> ☝️ 여기서 잠깐. 
> <br> **Program** 은 **파일 시스템 내 실행 파일**을 의미하고,
> <br> **Process**는 program이 진행되며 바뀌는 
**register, memory 영역 등을 모두 포함한 개념**이다.
<br>


## Process Context
프로그램이 어떤 것을 실행했고, 현재 어느 시점에 있는지 정확하게 규명하기 위한 모든 요소 집합.
<br>![prcoess_context](https://github.com/songhee-lee/songhee-lee.github.io/blob/main/assets/img/os/process_context.png)

<br> 위 그림과 같이 크게 세 가지 파트로 나눌 수 있다.
- **CPU 수행 상태를 나타내는 하드웨어 문맥**
  <br> : 현재 시점에 어디까지 실행 됐는가를 확인하는 지표들
  <br> Program Counter(PC), 각종 register
  <br>
  
- **프로세스의 주소 공간**
  <br> : 현재 시점에 메모리에 어떤 내용이 들어가 있는가를 확인하는 지표들
  <br> Code, Data, Stack
  <br>
  
- **커널의 주소 공간**
  <br> : 운영체제가 현 프로세스를 어떻게 평가하고 있느냐를 확인하는 지표들
  <br> 
  <details>
  <summary><b>Process Control Block(PCB)</b></summary>
  <div markdown="1">       

    ![process control block]([/assets/img/os](https://github.com/songhee-lee/songhee-lee.github.io/blob/main/assets/img/os)/pcb.png)
    <br> 운영체제가 각 프로세스를 관리하기 위해 프로세스 당 유지하는 정보로, kernel data영역에 있다. <br>
    
    1. OS가 관리상 사용하는 정보
    <br> : process state, process ID, scheduler information, priority 등 <br>
    
    2. CPU 수행 관련 하드웨어 값
    <br> : program counter, registers <br>
    
    3. 메모리 관련 위치 정보
    <br> : code, data, stack 위치 정보 <br>
   
    4. 파일 관련 정보
    <br>open file descriptors 등 <br>
  
  </div>
  </details>
<br>


## Process State
프로세스는 상태(State)가 변경되며 수행된다.
<br>![process state](https://github.com/songhee-lee/songhee-lee.github.io/blob/main/assets/img/os/process_state.png)

<details>
<summary>1. New : 프로세스 생성</summary>
<div markdown="1">       

프로세스 생성은 다음과 같은 네 가지 경우에서 일어난다.
1. System initialization
2. 실행 중인 프로세스가 프로세스 생성 시스템 호출 (fork)
3. 사용자가 새로운 프로세스 생성하도록 요청
4. 배치 작업의 시작

> Unix 에서는 fork 시스템 콜을 통해 프로세스를 생성한다.
> <br> fork를 통해 부모 프로세스의 주소 공간을 복사한 후, exec을 통해 새로운 프로그램을 실행시킨다.
> <br> 그러나 리눅스와 같은 운영체제에서는 바로 주소 공간을 복사하지 않고, 
  우선 실행하다가 부모 프로세스와 자식 프로세스의 내용이 달리지는 순간 카피해서 주소 공간을 할당한다.
> <br> 이 방식을 copy-on-wirte (COW) 라고 부른다.
  
</div>
</details>


<details>
<summary>2. Running : CPU 잡고 instruction 수행 중</summary>
<div markdown="1">       

- running -> waiting(blocked) : I/O 등 오래 걸리는 작업 때문에 자진해서 CPU를 내놓는 경우
- running -> ready : timer interrupt 걸려 CPU 빼앗김

</div>
</details>

<details>
<summary>3. Ready : CPU 기다리는 상태 (메모리 등 다른 조건을 모두 만족하고)</summary>
<div markdown="1">       

스케줄러에 의해 CPU 할당 받으면 당장 instruction을 수행할 수 있는 상태

</div>
</details>

<details>
<summary>4. Waiting(Blocked) : CPU 주어져도 당장 instruction 수행할 수 없는 상태</summary>
<div markdown="1">       

blocked -> ready : Process 자신이 요청한 event(예: I/O)가 만족될 때 상태 변화

</div>
</details>


<details>
<summary>5. Terminated : 프로세스 종료 </summary>
<div markdown="1">       

프로세스 종료는 다음과 같은 두 가지로 일어난다.
1. 자발적 종료
  - (main 끝나서 return 한다던가)
  - exit 시스템 콜 수행
2. 비 자발적 종료
   - Fatal Error 로 인한 강제 종료 (segment fault, nonexistent memory 등..)
   - 다른 프로세스에 의한 종료 (kiil)

  
> ☝️ 여기서 잠깐.
> <br> 프로세스의 세계는 자식 프로세스가 먼저 종료되어야 부모 프로세스가 종료되는, 단계적 종료가 일어난다.
> <br> 따라서 자식이 종료될 때는 부모 프로세스에 알린다.
  
</div>
</details>
<br>



# Thread
프로세스 내부에 CPU를 수행하는 단위로, lightweight process라고 부른다.
<br> 동일한 일을 하는 프로세스를 여러 개 실행할 때, 프로세스를 여러 개 만들면 주소공간이 낭비되기 때문에 
주소 공간을 하나만 두고(공유할 수 있는 자원은 공유) 실행하는 방식이다.

<br>![thread pcb](https://github.com/songhee-lee/songhee-lee.github.io/blob/main/assets/img/os/thread_pcb.png)

<br> **<장점>**
- CPU 관련 정보들 (PC, registers)만 Thread 별로 가지고 있기 때문에, 메모리가 절약된다.
- Creating & Switching 모든 면에서 프로세스보다 Thread가 빠르고 오버헤드도 적다.
- CPU가 여러개인 컴퓨터에서는 Thread를 활용해 병렬성을 높일 수 있다.
<br>

## Thread 구현
<br>![thread implementation](https://github.com/songhee-lee/songhee-lee.github.io/blob/main/assets/img/os/thread_implementation.png)

### a. User Threads
Thread 관리를 라이브러리로 제공한다.
- Thread를 지원하지 않는 OS 에서도 구현될 수 있고
- Thread 생성, 전환 등에 kernel 도움이 필요 없어 10-100배 정도 빠르다.
- Customized Scheduling Algorithm 사용이 가능하다.

- 그러나 OS가 Thread 존재를 모르기 때문에 clock interrupt 를 할 수 없어 무한루프 발생 등 오류 발생 시 thread 스케줄링이 어렵다.


### b. Kernel Threads
Thread 관리를 OS가 한다.
- OS가 Thread를 알기 때문에 스케줄링이 가능하다.
- Thread 생성, 전환 등에 


# Scheduler
CPU를 효율적으로 사용하기 위한 방법으로, 어떤 프로세스에게 CPU를 줄 지 고른다.
<br> CPU Scheduler가 프로세스를 고르면, **Dispatcher**가 CPU제어권을 선택된 프로세스에게 넘긴다.
<br> 이 과정을 **Context Switch**라고 한다.

> ☝️ 여기서 잠깐. 
> <br> CPU Scheduler는 별도의 하드웨어인가? 아니면 프로그램인가>
> <br> 정답은, 운영체제 안에 스케줄러 코드가 있고 이걸 CPU Scheduler라고 부르는 것 뿐이다.

<br> **< Scheduler가 필요한 이유 >**
<br> 프로그램의 실행은 CPU를 연석적으로 사용하는 단계(CPU burst)와 I/O를 사용하는 단계(I/O burst)로 이루어져 있다.
<br> Interactive job들은 I/O burst가 잦기 때문에, 사용자가 오래 기다리지 않게 하기 위해 CPU 스케줄링이 꼭 필요하다.

<br> **< 언제 Scheduler가 필요한가 >**
- nonpreemptive (자진 반납)
  <br> 예) I/O 시스템콜, Terminate
  
- preemptive(강제로 빼앗음) 
  <br> 예) CPU 할당 시간 만료(timer interrupt)
<br>

### Context Switch
CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정으로, 운영체제는 다음을 수행한다.
- CPU를 사용하던 프로세스 A가 system call 또는 interrupt 발생으로 CPU를 넘겨야 할 때
- CPU를 내어주는 프로세스 A의 상태를 A의 PCB에 기록하고,
- CPU를 새롭게 얻는 프로세스 B의 상태를 B의 PCB에서 읽어온다.

> ☝️ 여기서 잠깐.
> <br> system call이나 interrupt 발생 시 반드시 context switch가 일어나지는 않는다.
> <br> system call이나 interrupt 발생 시 프로세스 A에서 운영체제로 CPU가 넘어갔다가, 
> 바로 프로세스 A에게 CPU를 넘겨줄 수도 있다. 이런 경우는 context switch라고 부르지 않는다.
> <br> context switch는 운영체제가 다른 프로세스로 CPU를 넘길 때만 일어난다.
<br>


### Process state
process state에 suspend(stopped)가 추가된다.
<br>![process state(2)](https://github.com/songhee-lee/songhee-lee.github.io/blob/main/assets/img/os/prcess_state(2).png)


> ☝️ 여기서 잠깐. 
> <br> Blocked : 프로세스 자신이 요청한 event 가 완료되면 Ready 상태가 됨
> <br> Suspended : 외부(스케줄러)에서 프로세스를 정지해둔 상태로, 외부(스케줄러)에서 Resume 해주어야 Ready 상태가 됨.


> ✌️ 여기서 잠깐. 
> <br> User mode Running : 프로세스가 CPU 가지고 있으면서 본인의 코드를 실행하는 상태
> <br> Monitor mode Running : 운영체제에 요청해 운영체제가 실행 중인 상태로, 
> Kernel model 라고 말하지 운영체제가 running 한다고 말하지는 않는다.
<br>

## Scheduling Algorithm
스케줄링 알고리즘은 어떤 시스템이냐에 따라 다른 목적을 갖게 된다.
<br> 대부분은 CPU의 효율성에 중점이 있으나, 
<br> interactive system의 경우 응답 속도에, 
<br> real-time system의 경우 deadline 지키는 것에 중점을 둔다.

- FCFS (First-Come First-Served)
- SJF (Shortest Job Firt)
- SRTF (Shortest Remaining Time First)
- **RR (Round Robin)**
  <br> : 동일한 크기의 할당시간(quantum)을 가지고, 할당 시간이 끝나면 스케줄러가 조정한다.
  <br> Quantum을 어떻게 정할 것인지가 중요하다. (작게하면 switch 많이 일어나고, 크게하면 효율이 떨어진다.) <br>
- **Priority Scheduling**
  <br> : 우선 순위를 매겨 스케줄링한다.<br>
  
  <br> 아래 두 가지를 질문이 따라온다.
  1. 어떻게 우선순위를 정할 것인지?
  2. 우선순위가 낮은 프로세스들의 starvation 어떻게 해결할 것인지?
  
  **a. Multilevel Queue**
  <br> Ready Queue를 프로세스 유형별로 여러 개로 분할하고, 각 큐는 독립적인 스케줄링 알고리즘을 갖게 한다.
  <br> 예를 들어 interactive job의 경우 RR 방식으로, CPU만 사용해도 되는 job의 경우 FCFS의 방식으로 한다.
  <br> 우선순위가 낮은 프로세스들의 Starvation을 해결하기 위해 각 큐에 CPU time을 적절한 비율로 할당할 수 있다.
  <br>
  
  **b. Multilevel Feedback Queue**
  <br> 프로세스가 다른 큐로 이동이 가능한 방식이다.
  <br> 위 방법에서 필요한 Queue의 수, 각 큐의 scheduling algorithm과 더불어
  <br> process를 상위/하위 큐로 보내는 기준과 프로세스가 처음 CPU 할당받을 때 어떤 큐에 넣을지 기준이 필요하다. <br>
  
  <br> 구현 예시) 보통 우선 순위가 높은 queue는 quantum을 짧게 두고, 우선 순위가 낮은 queue는 길게 잡는다.
  <br> 처음 들어오는 프로세스를 우선 순위가 가장 높은 queue에 넣고, 해당 시간 내 끝나지 않으면 하위 큐로 강등되는 방식을 사용한다.
  <br> CPU 사용 시간이 짧은 프로세스에게 우선 순위를 높게 둠과 동시에, 
  모든 프로세스가 처음 들어올 때 상위 queue에 있어 starvation을 해결한다.
<br>  


# 추가 개념 및 용어 정리
### 프로세스와 관련한 시스템 콜
- fork() : create a child (copy)
  <br> 부모 프로세스에서 fork()가 발생하면, 자식 프로세스를 만들게 된다.
  <br> fork() 시스템 콜은 부모 프로세스에게 양수를, 자식 프로세스에게는 0을 각각 return 한다.
  <br> 자식 프로세스는 fork() 이후의 코드부터 실행한다.<br>

- exec() : overlay new image
  <br> exec() 실행 이후에는 코드가 남아있더라도 돌아올 수 없다. <br>
  
- wait() : sleep until child is done
- exit() : frees all the resoures, notify parent
<br>

## Interprocess Communication(IPC)

원칙적으로 프로세스는 독립적이라, 하나의 프로세스가 다른 프로세스에 영향을 주지 못한다.
<br> 그러나 협력이 필요한 경우가 있는데 이를 IPC라고 부른다.

<br>![ipc](https://github.com/songhee-lee/songhee-lee.github.io/blob/main/assets/img/os/ipc.png)

- Message passing : 커널을 통해 메세지를 보내 통신한다.
- Shared memory : 서로 다른 프로세스가 공유 메모리를 갖는다.
  <br> 커널에게 공유 메모리 사용을 요청하고 할당되면 이후 사용한다.
  <br> 한 번 공유 메모리가 설정되면 이후는 커널 도움없이 통신이 가능해진다.

> ☝️ 여기서 잠깐.
> <br> Thread는 사실상 하나의 프로세스이므로 IPC 라고 보기엔 어렵다.
> <br> 그렇지만 동일 프로세스를 구성하는 thread들 간에는 주소 공간을 공유하기 때문에 협력이 가능하다.
<br>

### Multiple-processor Scheduling
- Symmetric Multiprocessing (SMP) : 모든 CPU가 대등, 알아서 스케줄링한다,
- Asymmetric Multiprocessing (AMP) : 하나의 CPU가 대장이 되어 스케줄링을 책임진다.
<br>

### Scheduling Algorithm Evaluation
- Queueing models : 이론적으로 프로세스의 arrive rate과 service rate을 계산
- Implementation & Measurement : 실제 시스템에 알고리즘을 구현하여 성능 측정
- Simulation : 모의 프로그램으로 작성해 trace(예제)를 입력해 결과 비교
<br><br>

# References
### Image
- [thread_implementation](https://examradar.com/os-threads-different-types-thread-questions-answers/)
- [others](http://www.kocw.net/home/cview.do?cid=3646706b4347ef09)

### Content
- [kocw 반효경 교수님 OS 강의 내용](http://www.kocw.net/home/cview.do?cid=3646706b4347ef09)
- pino의 김선일 교수님 OS 강의 필기
- [gatevidyalay OS ariticles](https://www.gatevidyalay.com/process-states-in-operating-system/)

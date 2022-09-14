# Deadlocks (교착상태)

| <img width="432" alt="Screen Shot 2022-09-11 at 4 46 27 PM" src="https://user-images.githubusercontent.com/59877415/189517415-04fef6ae-810c-4c41-bd70-9a132243ef9c.png"> | 데드락이란?<br /><br />- 시스템 자원에 대한 요구가 뒤엉킨 상태<br />- 둘 이상의 프로세스가 서로가 가진 자원을 필요로 하며<br />무한 대기에 빠지는 상황 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

---

### 데드락은 언제 발생할까?

- 조건1) Mutual exclusion (상호 배타)

  - 자원 공유가 불가능하다.
  - 예를 들어 특정 자원의 인스턴스가 2개이면 최대 2개의 프로세스만 해당 자원을 사용할 수 있다.

- 조건2) Hold and wait (보유 및 대기)

- 조건3) No Preemption (비선점)

  - 다른 프로세스가 보유중인 자원을 뺐어올 수 없다.

- 조건4) Circular wait (환형 대기)

  - 자원 할당도 상에 원이 만들어져야 한다.
  - 자원 할당도
    - 자원은 네모로, 자원의 인스턴스는 점으로, 프로세스는 원으로, 자원의 할당은 화살표로 표시된다.

  - | <img width="250" alt="Screen Shot 2022-09-11 at 5 07 05 PM" src="https://user-images.githubusercontent.com/59877415/189518018-844cd147-bad4-4248-b19a-d3b0d781910a.png"> | <img width="275" alt="Screen Shot 2022-09-11 at 5 07 17 PM" src="https://user-images.githubusercontent.com/59877415/189518021-a612908e-73d2-4e91-b884-1316e0a6e5c8.png"> |
    | ------------------------------------------------------------ | ------------------------------------------------------------ |
    | 환형대기 (O) Deadlock (O)                                    | 환형 대기 (O) Deadlock (X)                                   |

→ 위의 조건들은 데드락의 필요조건으로, **4가지 조건이 모두 만족**될 경우 데드락이 일어날 **수도** 있다.

---

### 데드락은 어떻게 처리할까?

1. 교착 상태 방지 : 데드락의 발생 조건을 막는다.
2. 교착 상태 회피 : 프로세스의 자원 요청을 신중하게 승인한다.
3. 교착 상태 검출 및 복구
4. 교착 상태 무시

---

### 교착 상태 방지

-  교착 상태의 4가지 필요 조건 중 한 가지 이상을 불만족시키면 된다.

1. 상호 배타 방지

   - 자원을 공유 가능하게 한다.

   - 원천적으로 불가능한 경우가 대부분이다.

     → CPU, 메모리, 디스크, 프린트 모두 불가능하고 read only 파일 정도만 가능하다.

2. 보유 및 대기 방지

   - 프로세스가 동시에 모든 자원을 잡거나 순차적으로 자원을 잡는 경우 다 잡지 못하면 소유한 자원을 놓는다.
   - 자원 활용률이 저하되거나 기아(starvation) 문제가 발생할 수 있다.

3. 비선점 방지

   - 자원을 선점 가능하게 한다.

   - 원천적으로 불가능한 경우가 대부분이다.

     → 프린터 선점을 가능하게 하면 난리가 날 것이다. CPU context switching을 강제로 할 수는 있겠으나 부작용이 있을 것이다.

4. 환형 대기 방지

   | <img width="332" alt="Screen Shot 2022-09-11 at 5 20 46 PM" src="https://user-images.githubusercontent.com/59877415/189518314-5c52a6b1-7bb5-4795-8db6-5c559d84f4e4.png"> | - 자원에 번호를 부여하여 오름차순으로 자원을 요청하게 한다. <br />-  위의 그림은 식사하는 철학자 예시로 교착상태에 빠진 상황인데,<br />환형 대기 방지를 하면 R0를 기점으로 원이 끊어지게 된다.<br />- 자원 활용율이 저하되는 문제가 있다. |
   | ------------------------------------------------------------ | ------------------------------------------------------------ |

- 1번과 3번 방법은 현실성이 떨어지고 2번과 4번 방법은 그나마 사용가능하다.

---

### 교착상태 회피

| <img width="266" alt="Screen Shot 2022-09-11 at 5 30 42 PM" src="https://user-images.githubusercontent.com/59877415/189518654-8c661af2-423a-4b24-97f9-9f0bd06bdf57.png"> | - 교착 상태를 자원 요청에 대한 잘못된 승인으로<br />보는 관점에서의 해결책이다.<br /><br />- Process(Application)가 Resource(Hardware)를<br />필요로 할 때 OS가 자원 요청을 잘못 승인한 경우 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- 이를 해결하려면 OS가 **안전한 할당**을 해주면 된다.
- 아래는 자원이 12개인 상황에서의 예시이다. Current needs는 시작할 때의 자원 필요량, Max needs는 해당 작업을 끝낼 때의 자원 필요량이다.

| <img width="700" alt="Screen Shot 2022-09-11 at 5 37 25 PM" src="https://user-images.githubusercontent.com/59877415/189518871-94538df4-4228-41a9-800f-5b1bfc5fe7bc.png"> | 안전한 할당의 예시<br /><br />모두 실행 -> P1 종료 -> P0 종료 -> P2 종료 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <img width="703" alt="Screen Shot 2022-09-11 at 5 37 36 PM" src="https://user-images.githubusercontent.com/59877415/189518879-13925bc2-b53d-4e5c-8fce-116b0fd3ef93.png"> | 불안전한 할당의 예시<br /><br />모두 실행 -> P1 종료 -> 교착 상태 |

- 대출 전문 은행의 문제 해결 방식과 비슷하다. (Banker's Algorithm)

---

### 교착 상태 검출 및 복구

- 교착 상태가 발생하는 것을 허용하고 주기적으로 검사를 해서 교착 상태 발생시 복구를 한다.
- 검사에 따른 추가 비용이 발생한다. (overhead)
- 복구시에는 이전 상태로 되돌린다.
  - 이전 상태를 기억해야 하므로 비용이 발생한다.
  - 특정 프로세스를 강제 종료하거나 자원을 선점하여 타 프로세스에게 할당한다.

---

### 교착 상태 무시

- 교착 상태는 잘 일어나지 않으므로 발생하면 기기를 재시동한다. ~~껐켜~~

# Process Synchronization (동기화 문제)

- Concurrency Control (병행 제어) 와 같은 표현이다.

### 컴퓨터 시스템 내부의 데이터 접근 패턴

| <img width="435" alt="Screen Shot 2022-09-12 at 1 35 13 PM" src="https://user-images.githubusercontent.com/59877415/189574732-b079940f-c31a-484a-bbda-452b8fada342.png"> | - 데이터를 읽어와서 연산하고 저장한다.<br />- 읽기만 하면 문제가 없을텐데, 읽고 수정을 하면<br />동기화 문제가 발생한다.<br />- Execution Box와 Storage Box의 예시<br />1. CPU & Memory<br />2.  Computer 내부 & 디스크 (하드웨어 관점)<br />3. 프로세스 & 프로세스 주소 공간 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

---

### Race Condition (경쟁상태)

- Race Condition이란?

  : 2개 이상의 프로세스(스레드)들이 하나의 자원(공유 자원)에 동시에 접근하려 경쟁하는 상태

- 예시

| <img width="561" alt="Screen Shot 2022-09-12 at 2 47 20 PM" src="https://user-images.githubusercontent.com/59877415/189582001-6a3267fd-450c-4f4d-a417-961be2f5cdb4.png"> | - Storage Box를 공유하는 Execution box가<br />여러개인 경우 Race Condition의 가능성이 생긴다.<br /><br />- 왼쪽 예시의 결과는 0이어야 하는데<br />결과 저장의 순서에 따라 +1 또는 -1 이 될 수 있다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- Race Condition이 발생할 수 있는 경우
  - CPU가 여러개인 Multiprocessor System의 경우 메모리를 공유하므로 문제가 발생할 수 있다.
  - 여러 프로세스들이 공유 메모리를 사용할 때 커널 내부 데이터를 접근하는 루틴들이 겹치면 문제가 발생할 수 있다.

---

### Race Condition이 발생하는 경우

1. (process의 system call로) 커널모드로 작업을 수행하던 중 interrupt가 발생하여 interrupt 처리 루틴이 수행되는 경우

| <img width="549" alt="Screen Shot 2022-09-12 at 2 58 01 PM" src="https://user-images.githubusercontent.com/59877415/189583348-022123d6-c7fb-4f1f-a929-365e7f8f9784.png"> | 양쪽 다 커널 코드이므로<br />kernel address space가<br />공유된다.<br /><br />해결책 : 중요한 데이터를 처리하고 있으면<br />interrupt가 들어와도 처리를 미룬다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

2. (process의 system call로) 커널모드로 작업을 수행하던 중 context switching이 일어나는 경우

| <img width="516" alt="Screen Shot 2022-09-12 at 3 04 34 PM" src="https://user-images.githubusercontent.com/59877415/189584255-b82af1af-dd91-4151-88d3-b10621428e7c.png"> | - 커널 코드가 두 프로세스에서 수행되므로<br />kernel address space가 공유된다.<br /><br />해결책 : 프로세스 수행에 할당된 시간이<br />끝나도 kernel 코드를 수행중일 때는<br />다른 process가 CPU를 선점할 수 없게 한다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

3. Multiprocessor (여러개의 CPU)가 공유자원 내의 kernel data를 접근하는 경우

- 해결책1
  - kernel에는 1개의 CPU만이 접근할 수 있게 한다.
  - 대단히 비효율적이다.
- 해결책2
  - 커널 내부에 있는 공유 데이터에 접근할 때마다 각 데이터에 lock / unlock을 한다.
  - 해결책1보다 효율적이다.

[ 주의 사항 ]

- 여러 프로세스가 공유 자원을 사용한다고 해서 무조건 Race Condition에 놓이는 것은 아니다. kernel 내부 데이터에 동시에 접근하려 해야 Race Condition에 놓였다고 할 수 있다.

[ Critical Section (임계 구역) ]

- 공유 데이터에 접근하려는 코드
- 예시
  - X = X + 1
  - X = X - 1

---

### 동기화 문제

| <img width="217" alt="Screen Shot 2022-09-12 at 4 06 01 PM" src="https://user-images.githubusercontent.com/59877415/189592961-c8c8a9ec-f25f-4515-8d8b-c2f4597580c3.png"> | 동기화 문제를 해결하는 방법<br /><br />entry section : 공유 데이터에 접근하기 전에 lock을 건다.<br />exit section : 공유 데이터의 사용을 마치고 lock을 푼다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

**[ 동기화 문제를 해결하기 위해 충족해야 할 조건 ]**

1. **Mutual Exclusion** : 특정 프로세스가 critical section을 수행중이면 다른 프로세스들은 critical section에 들어가면 안된다.
2. **Progress** : critical section에 어떤 프로세스도 없으면 특정 프로세스가 critical section에 들어가고자 할 때 들어가게 해준다. (두 프로세스가 동시에 critical section에 들어가는걸 막으려다가 어떤 프로세스도 들어가지 못할 수 있다.)
3. **Bounded Waiting** : 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다. (starvation 방지)

---

### 동기화 문제의 소프트웨어적 해결 방안

**[ Algorithm 1 ]**

| <img width="333" alt="Screen Shot 2022-09-12 at 4 14 24 PM" src="https://user-images.githubusercontent.com/59877415/189594378-ced2feb0-5031-4e06-9eea-232481945e62.png"> | 왼쪽의 코드 : P0 기준 코드 (P0 , P1 두개의 프로세스 가정)<br />turn : critical section에 들어간 프로세스<br /> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- Mutual Exclusion 만족
- Progress 불만족
  - 프로세스 P0와 P1이 critical section에 교대로 들어가게 되어 있는데 P0이 critical section에 들어갔다 나왔는데 P1이 critical section에 들어가지 않으면 P0도 계속 critical section에 들어갈 수 없다.

**[ Algorithm 2 ]**

| <img width="394" alt="Screen Shot 2022-09-12 at 4 19 25 PM" src="https://user-images.githubusercontent.com/59877415/189595258-b7d9a278-fd2d-46f4-a8bc-2c4c4eb21766.png"> | (P0, P1 두개의 프로세스 가정)<br />flag : 두 프로세스가 각각 가지는 변수로 critical section에<br />들어가고자하는 의중을 나타냄 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- Mutual Exclusion 만족
- Progress 불만족
  - 둘이 동시에 flag가 true인 경우 critical section에는 아무 프로세스도 들어가지 않은채로 무한 대기 상태가 됨

**[ Algorithm 3 : Peterson's Algorithm ]**

| <img width="434" alt="Screen Shot 2022-09-12 at 4 22 35 PM" src="https://user-images.githubusercontent.com/59877415/189595791-6299927c-0917-4881-94f7-ee7139d23539.png"> | turn과 flag를 모두 사용함<br /><br />다른 프로세스가 critical section에 들어갈 의사가 있고<br />다른 프로세스의 turn일 때만 기다림 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- Mutual Exclusion, Progress, Bounded waiting 모두 만족
- **Busy Waiting**의 문제가 있다. (spin lock)

​		: critical section에 들어가도 되는지 확인하기 위해 while문을 계속 돈다.

​		(CPU 할당시간을 while문에 다 쓸지도 모른다.)

---

### 동기화 문제의 하드웨어적 해결 방안

| <img width="318" alt="Screen Shot 2022-09-12 at 4 27 36 PM" src="https://user-images.githubusercontent.com/59877415/189596708-3fb99bfc-b426-44e9-b4b9-f1a6035dee1c.png"> | Test_and_Set이라는<br />하나의 명령어로 읽고 쓰기가 함께 됨<br /><br />lock이 false로 읽히면서 True로 바뀌고<br />critical section을 사용하고 나오면서<br />lock이 false로 바뀜 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

---

### Semaphores

- Semaphore는 공유자원의 사용과 반납을 처리하기 위한 일종의 추상자료형이다.
- Semaphore의 값 S와 2개의 연산(P 연산, V 연산)으로 구성되어 있다.
  - 예를 들어 S가 5이면 자원의 개수가 5개이다.

| <img width="303" alt="Screen Shot 2022-09-12 at 5 21 13 PM" src="https://user-images.githubusercontent.com/59877415/189606382-66fdd576-6f3b-4c90-ac89-c3d6237bc780.png"> | <img width="304" alt="Screen Shot 2022-09-12 at 5 21 21 PM" src="https://user-images.githubusercontent.com/59877415/189606429-82fcacfa-8f12-459a-a55b-70835fe9564c.png"> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 공유 자원을 획득하여 사용하는 연산<br />(**busy-wait** 문제가 있다.) | 공유 자원을 사용하고 반납하는 연산                           |

- Semaphore의 종류

| Counting Semaphore                                           | Binary Semaphore                                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| - semaphore의 값이 0 이상의 정수 값이다.<br />(0, 3, 5, 10... 등등)<br />- 주로 resource counting에 사용된다. | - semaphore의 값이 0 또는 1이다.<br />(자원의 개수가 1개이다.)<br />- 주로 mutual exclusion에 사용된다.<br />(lock / unlock) |

- Semaphore를 Critical Section에 적용한 코드

<img width="488" alt="Screen Shot 2022-09-12 at 5 28 02 PM" src="https://user-images.githubusercontent.com/59877415/189607757-cf1e43d7-1af2-4ce5-8bbc-da897fb4c70e.png">

---

### Block / Wakeup (Sleep lock)

| <img width="410" alt="Screen Shot 2022-09-12 at 5 34 18 PM" src="https://user-images.githubusercontent.com/59877415/189609012-012c8434-ccf2-4cda-a810-ba6ab7fb31c5.png"> | <img width="405" alt="Screen Shot 2022-09-12 at 5 34 27 PM" src="https://user-images.githubusercontent.com/59877415/189609038-2d40f470-ff63-4ebe-b588-b8de79888d61.png"> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- Block / Wakeup 방식이란

  - busy-wait를 개선한 방법

  - 자원 획득을 희망하지만 자원을 획득할 수 없는 프로세스는 block 시킨다.

    → block된 프로세스의 PCB를 semaphore의 wait-queue에 넣는다.

  - 다른 프로세스가 자원을 반납하면 block된 프로세스들 중 하나가 wakeup되는 방식이다.

    → wakeup된 프로세스의 PCB를 ready-queue로 옮긴다.

- 구현 코드

| <img width="476" alt="Screen Shot 2022-09-12 at 5 35 52 PM" src="https://user-images.githubusercontent.com/59877415/189609318-5424eac4-acb4-40b4-9d5e-4ac843484085.png"> | <img width="474" alt="Screen Shot 2022-09-12 at 5 35 57 PM" src="https://user-images.githubusercontent.com/59877415/189609328-a55ca5b5-3320-47be-b402-f8d4f8ef3e72.png"> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| S의 값을 1 빼준다.<br />S의 값이 0보다 작으면 여분이 없다는 의미이고<br />해당 프로세스의 PCB를 S.L에 연결해준다. | S의 값을 1 더해준다.<br />S의 값이 0 이하이면 **어떤 프로세스가 S.L에서**<br />**기다리며 잠들어 있다는 의미이다.** |

- 두 방식의 비교

| Block / Wakeup                                               | Busy-wait                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| - CPU를 의미있게 사용하는 비율이 높아진다.<br />- 프로세스의 상태를 block에서 ready로 변경하는데<br />오버헤드가 있다<br />- critical section의 길이가 아주 짧지 않으면 일반적으로<br />권장된다. | - critical section의 길이가 아주 짧으면<br />사용이 권장된다. |

---

### Classical Problems of SynChronization

1. Bounded-Buffer Problem (Producer-Consumer Problem)

| <img width="587" alt="Screen Shot 2022-09-12 at 7 41 29 PM" src="https://user-images.githubusercontent.com/59877415/189634218-fe28d62c-f7a2-41ad-a13b-3e34e76274ad.png"> | <img width="526" alt="Screen Shot 2022-09-12 at 7 42 52 PM" src="https://user-images.githubusercontent.com/59877415/189634521-5218c332-ae12-4e14-a797-aa3cc8334ede.png"> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| - process가 2종류 : producer, consumer                       | - mutex : lock을 위한 변수<br />- full / empty : 들어있는 / 비어있는 버퍼의 수 |

- semaphore의 역할 
  - lock을 통해 두 프로세스의 접근을 막음
  - full, empty 등 가용 자원의 개수를 셈 (생산자와 소비자의 입장에서 가용 자원의 수는 반대임, 각각 empty와 full)

2. Readers and Writers Problem

| <img width="543" alt="Screen Shot 2022-09-12 at 7 49 10 PM" src="https://user-images.githubusercontent.com/59877415/189635559-d82fdb42-1f24-4167-998f-36a9baa707cb.png"> | - 한 프로세스가 DB에 write 중일 때 다른 프로세스<br />는 접근할 수 없다.<br />- read는 여러 프로세스가 동시에 할 수 있다.<br />- writer는 대기중인 reader가 없을 때 접근이<br />허용된다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- 공유 데이터 : DB, readcount
  - readcount 또한 공유 자원이므로 값을 변화시킬 때 lock을 걸어주어야 한다.
- writer의 starvation이 발생할 수 있고 우선순위 부여를 통해 이 문제를 해결할 수 있다.

3. Dining-Philosophers Problem

| <img width="265" alt="Screen Shot 2022-09-12 at 7 54 53 PM" src="https://user-images.githubusercontent.com/59877415/189636525-934dd9ce-41bb-48e1-ac1a-58d10a9bd30b.png"> | <img width="213" alt="Screen Shot 2022-09-12 at 7 54 58 PM" src="https://user-images.githubusercontent.com/59877415/189636548-c37dc6a2-ea4c-4552-a86e-99b2ec5d4714.png"> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- 철학자 5명은 생각하는 일 (배부를 때) 과 밥먹는 일 (배고플 때) 을 한다.
- 5명의 철학자가 모두 왼쪽 젓가락을 동시에 잡을 경우 데드락의 가능성이 있다.
  - 비대칭적 젓가락 잡기를 통해 개선이 가능하다. (짝수번은 왼쪽부터, 홀수번은 오른쪽부터)
  - 양쪽의 젓가락을 다 잡을 수 있지 않으면 젓가락을 반납하는 것으로도 해결할 수 있다.

---

### Monitor

- Semaphore의 문제점 (Monitor의 필요성)
  - 코딩하기 어렵고 한 번의 실수가 모든 시스템에 치명적인 영향을 끼친다.
  - 정확성을 입증하기 어렵다.

**[ Monitor ]**

| <img width="502" alt="Screen Shot 2022-09-12 at 11 30 59 PM" src="https://user-images.githubusercontent.com/59877415/189681225-ab6ea1f9-6220-468d-a348-2f50432b1eeb.png"> | <img width="255" alt="Screen Shot 2022-09-12 at 7 59 47 PM" src="https://user-images.githubusercontent.com/59877415/189637378-8d46b6b4-dc08-41ea-a967-3f70f53363c4.png"> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없다.

  **(프로그래머는 lock을 걸거나 unlock을 할 필요가 없다.)**

- 모니터 내에서는 한 번에 하나의 프로세스 만이 활동할 수 있다.

- 공유 데이터는 monitor 내부의 procedure를 통해서만 접근할 수 있다.

- 프로세스가 monitor 안에서 기다릴 수 있도록 condition variable을 사용한다.

- condition variable은 wait와 signal 연산에 의해서만 접근할 수 있다.

| <img width="500" alt="Screen Shot 2022-09-12 at 8 02 01 PM" src="https://user-images.githubusercontent.com/59877415/189638026-21f090de-afaa-4fae-bf9e-87e1c9317270.png"> | - Producer-Consumer Problem을<br />monitor를 사용하여 바꾼 코드<br /><br />- wait 연산<br />해당 프로세스는 다른 프로세스가 invoke할<br />때 까지 suspend 된다.<br /><br />- signal 연산<br />정확하게 하나의 suspend된 프로세스를<br />resume한다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

| Semaphore                                                    | Monitor                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 프로그래머가 자원의 획득과 반납에 관한 코드를 작성한다.      | Monior 차원에서 동시 접근을 제어한다.                        |
| <img width="582" alt="Screen Shot 2022-09-12 at 11 32 28 PM" src="https://user-images.githubusercontent.com/59877415/189681259-2e2d52b7-7fcd-4789-9bbc-50645db4f8f5.png"> | <img width="585" alt="Screen Shot 2022-09-12 at 11 31 19 PM" src="https://user-images.githubusercontent.com/59877415/189681270-1cdf620c-2ff9-4b15-88c7-c3648a636743.png"> |

- Semaphore와 Monitor의 차이는 명확하나 둘의 코드는 크게 다르지 않다.

---

**References**

[ 정리 내용 출처 ]

http://www.kocw.net/home/cview.do?cid=3646706b4347ef09

http://www.kocw.net/home/cview.do?cid=5c3c30382c7bbcf6

[ 사용 이미지 출처 ]

http://www.kocw.net/home/cview.do?cid=3646706b4347ef09

http://www.kocw.net/home/cview.do?cid=5c3c30382c7bbcf6

https://www.geeksforgeeks.org/introduction-of-deadlock-in-operating-system/

https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=three_letter&logNo=220380867227

https://truemind5.blogspot.com/2017/04/12-1.html

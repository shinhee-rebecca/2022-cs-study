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

### 

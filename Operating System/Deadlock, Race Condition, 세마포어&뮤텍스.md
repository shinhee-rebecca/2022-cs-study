## Index

[Deadlocks (교착상태)](#Deadlocks-(교착상태))

- [데드락의 발생 조건](#데드락의-발생-조건)
- [데드락의 처리 방법](#데드락의-처리-방법)
- [교착 상태 방지](#교착-상태-방지)
- [교착상태 회피](#교착상태-회피)
- [교착 상태 검출 및 복구](#교착-상태-검출-및-복구)
- [교착 상태 무시](#교착-상태-무시)

<br />

[동기화 문제](#동기화-문제)

- [Race Condition(경쟁상태)](#Race-Condition(경쟁상태))
- [Race Condition이 발생하는 경우](#Race-Condition이-발생하는-경우)
- [Critical Section (임계 구역)](#Critical-Section-(임계-구역))
- [동기화 문제를 해결하는 방법](#동기화-문제를-해결하는-방법)

<br />

[예상 질문](#예상-질문)

[References](#References)

<br />

## Deadlocks (교착상태)

<br />

<img width="432" alt="Screen Shot 2022-09-11 at 4 46 27 PM" src="https://user-images.githubusercontent.com/59877415/189517415-04fef6ae-810c-4c41-bd70-9a132243ef9c.png">

데드락이란 시스템 자원에 대한 요구가 뒤엉킨 상태이다. 즉, 둘 이상의 프로세스가 서로가 가진 자원을 필요로 하며 무한 대기에 빠지는 상황이다.

<br />

---

<br />

### 데드락의 발생 조건

<br />

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

| <img width="250" alt="Screen Shot 2022-09-11 at 5 07 05 PM" src="https://user-images.githubusercontent.com/59877415/189518018-844cd147-bad4-4248-b19a-d3b0d781910a.png"> | <img width="275" alt="Screen Shot 2022-09-11 at 5 07 17 PM" src="https://user-images.githubusercontent.com/59877415/189518021-a612908e-73d2-4e91-b884-1316e0a6e5c8.png"> |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 환형대기 (O) Deadlock (O)                                    | 환형 대기 (O) Deadlock (X)                                   |

위의 조건들은 데드락의 필요조건으로, **4가지 조건이 모두 만족**될 경우 데드락이 일어날 **수도** 있다.

<br />

---

<br />

### 데드락의 처리 방법

<br />

1. 교착 상태 방지 : 데드락의 발생 조건을 막는다.
2. 교착 상태 회피 : 프로세스의 자원 요청을 신중하게 승인한다.
3. 교착 상태 검출 및 복구
4. 교착 상태 무시

<br />

---

<br />

### 교착 상태 방지

<br />

교착 상태의 4가지 필요 조건 중 한 가지 이상을 불만족시키면 된다.

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

| <img width="332" alt="Screen Shot 2022-09-11 at 5 20 46 PM" src="https://user-images.githubusercontent.com/59877415/189518314-5c52a6b1-7bb5-4795-8db6-5c559d84f4e4.png"> | - 자원에 번호를 부여하여 오름차순으로 자원을 요청하게 한다. <br />-  위의 그림은 식사하는 철학자 예시로 교착상태에 빠진 상황인데, 환형 대기 방지를 하면 R0를 기점으로 원이 끊어지게 된다.<br />- 자원 활용율이 저하되는 문제가 있다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

1번과 3번 방법은 현실성이 떨어지고 2번과 4번 방법은 그나마 사용가능하다.

<br />

---

<br />

### 교착상태 회피

<br />

| <img width="266" alt="Screen Shot 2022-09-11 at 5 30 42 PM" src="https://user-images.githubusercontent.com/59877415/189518654-8c661af2-423a-4b24-97f9-9f0bd06bdf57.png"> | - 교착 상태를 자원 요청에 대한 잘못된 승인으로 보는 관점에서의 해결책이다.<br />- Process(Application)가 Resource(Hardware)를 필요로 할 때 OS가 자원 요청을 잘못 승인한 경우 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |

- 이를 해결하려면 OS가 **안전한 할당**을 해주면 된다.
- 아래는 자원이 12개인 상황에서의 예시이다. Current needs는 시작할 때의 자원 필요량, Max needs는 해당 작업을 끝낼 때의 자원 필요량이다.

| <img width="700" alt="Screen Shot 2022-09-11 at 5 37 25 PM" src="https://user-images.githubusercontent.com/59877415/189518871-94538df4-4228-41a9-800f-5b1bfc5fe7bc.png"> | 안전한 할당의 예시<br /><br />모두 실행 -> P1 종료 -> P0 종료 -> P2 종료 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <img width="703" alt="Screen Shot 2022-09-11 at 5 37 36 PM" src="https://user-images.githubusercontent.com/59877415/189518879-13925bc2-b53d-4e5c-8fce-116b0fd3ef93.png"> | 불안전한 할당의 예시<br /><br />모두 실행 -> P1 종료 -> 교착 상태 |

<br />

---

<br />

### 교착 상태 검출 및 복구

<br />

- 교착 상태가 발생하는 것을 허용하고 주기적으로 검사를 해서 교착 상태 발생시 복구를 한다.
- 검사에 따른 추가 비용이 발생한다. (overhead)
- 복구시에는 이전 상태로 되돌린다.
  - 이전 상태를 기억해야 하므로 비용이 발생한다.
  - 특정 프로세스를 강제 종료하거나 자원을 선점하여 타 프로세스에게 할당한다.

<br />

---

<br />

### 교착 상태 무시

<br />

교착 상태는 잘 일어나지 않으므로 발생하면 기기를 재시동한다. ~~껐켜~~

<br />

---

<br />

## 동기화 문제

<br />

둘 이상의 프로세스나 스레드가 공유 데이터에 접근하는 상황, 즉 경쟁 상태를 해결하는 방법이다. (Concurrency Control (병행 제어) 와 같은 표현이다.)

<br />

---

<br />

### Race Condition(경쟁상태)

<br />

2개 이상의 프로세스(스레드)들이 하나의 자원(공유 자원)에 동시에 접근하려 경쟁하는 상태이다. 여러 프로세스가 공유 자원을 사용한다고 해서 무조건 Race Condition에 놓이는 것은 아니다. kernel 내부 데이터에 동시에 접근하려 해야 Race Condition에 놓였다고 할 수 있다.

- CPU가 여러개인 Multiprocessor System의 경우 메모리를 공유하므로 문제가 발생할 수 있다.
- 여러 프로세스들이 공유 메모리를 사용할 때 커널 내부 데이터를 접근하는 루틴들이 겹치면 문제가 발생할 수 있다.

<br />

---

<br />

### Race Condition이 발생하는 경우

<br />

1. (process의 system call로) 커널모드로 작업을 수행하던 중 interrupt가 발생하여 interrupt 처리 루틴이 수행되는 경우

<img width="549" alt="Screen Shot 2022-09-12 at 2 58 01 PM" src="https://user-images.githubusercontent.com/59877415/189583348-022123d6-c7fb-4f1f-a929-365e7f8f9784.png">

양쪽 다 커널 코드이므로 kernel address space가 공유된다.

해결책 : 중요한 데이터를 처리하고 있으면 interrupt가 들어와도 처리를 미룬다.

<br />

2. (process의 system call로) 커널모드로 작업을 수행하던 중 context switching이 일어나는 경우

<img width="516" alt="Screen Shot 2022-09-12 at 3 04 34 PM" src="https://user-images.githubusercontent.com/59877415/189584255-b82af1af-dd91-4151-88d3-b10621428e7c.png">

커널 코드가 두 프로세스에서 수행되므로 kernel address space가 공유된다.

해결책 : 프로세스 수행에 할당된 시간이 끝나도 kernel 코드를 수행중일 때는 다른 process가 CPU를 선점할 수 없게 한다.

<br />

3. Multiprocessor (여러개의 CPU)가 공유자원 내의 kernel data를 접근하는 경우

- 해결책1
  - kernel에는 1개의 CPU만이 접근할 수 있게 한다.
  - 대단히 비효율적이다.
- 해결책2
  - 커널 내부에 있는 공유 데이터에 접근할 때마다 각 데이터에 lock / unlock을 한다.
  - 해결책1보다 효율적이다.

<br />

---

<br />

### Critical Section (임계 구역)

<br />

공유 데이터에 접근하려는 코드를 의미한다.

- 하나의 프로세스가 자신의 임계구역을 수행하는 동안에는 다른 프로세스는 그들의 임계구역에 들어갈 수 없어야 한다. 즉 임계구역 내의 코드는 원자적으로 실행되어야 한다.
- (임계구역에 진입하는 부분을 entry section이라고 부르고 임계구역에서 나오는 부분은 exit section이라고 부른다.)

<br />

---

<br />

### 동기화 문제를 해결하는 방법

<br />

1. Mutex

하나의 스레드만이 공유자원에 접근할 수 있도록하여 경쟁 상황을 방지한다.

- 하나의 스레드가 공유 자원에 lock을 걸면 다른 스레드는 unlock이 될 때까지 해당 자원에 접근할 수 없다. (lock의 획득과 반환을 통해 공유자원 접근을 제어한다.)
- acquire 함수와 release 함수를 사용한다.
- busy waiting을 통해 CPU가 낭비된다는 단점이 있다.

<br />

2. Semaphore

S개의 스레드만이 공유 자원에 접근할 수 있도록 제어하는 동기화 기법이다.

- 가용 자원의 수를 S값으로 초기화하고, 자원에 접근할 때에는 S-- 연산을 수행하여 세마포어 값을 감소시키고 자원의 사용이 끝나면 S++ 연산을 수행하여 세마포어 값을 증가시킨다. 세마포어 값이 0이 되면 모든 자원이 사용중인 것을 의미하고 이후 자원을 사용하려는 프로세스는 세마포어 값이 0보다 커질 때까지 block 된다.
- wait 함수와 signal 함수를 사용한다.
- Binary Semaphore는 Mutex와 비슷하다.
- busy waiting을 통해 CPU가 낭비된다는 단점이 있다.

<br />

3. Monitor

프레임워크나 라이브러리 그 자체에서 제공되는 동기화 기법이다.

- 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없다.
- 모니터 내에서는 한 번에 하나의 프로세스 만이 활동할 수 있다.
- 공유 데이터는 monitor 내부의 procedure를 통해서만 접근할 수 있다.

- 프로세스가 monitor 안에서 기다릴 수 있도록 condition variable을 사용한다.

- condition variable은 wait와 signal 연산에 의해서만 접근할 수 있다.

<br />

덧. Mutex와 Semapore의 Busy waiting을 해결하는 방법

Block and wake up 방식이다.

- 자원 획득을 희망하지만 자원을 획득할 수 없는 프로세스는 block 시킨다.

  → block된 프로세스의 PCB를 semaphore의 wait-queue에 넣는다.

- 다른 프로세스가 자원을 반납하면 block된 프로세스들 중 하나가 wakeup되는 방식이다.

  → wakeup된 프로세스의 PCB를 ready-queue로 옮긴다.

Critical Section이 아주 짧은 경우가 아니라면 Block and wake up 방식이 권장된다. CPU를 의미있게 사용하는 비율이 높아지기 때문이다. 단, 프로세스의 상태를 block에서 ready로 변경하는 데에 따른 오버헤드가 있다.

<br />

---

<br />

## 예상 질문

<br />

1. 교착 상태란?

   → 교착 상태는 어떨 때 발생하는지?

   → 교착 상태는 어떻게 해결하는지?

<br />

2. 동기화 문제란?

   → 동기화 문제를 해결하는 방법은?

<br />

---

<br />

## References

<br />

[KOCW 운영체제 반효경 교수님 강의](http://www.kocw.net/home/cview.do?cid=3646706b4347ef09)

[KOCW운영체제 양희재 교수님 강의](http://www.kocw.net/home/cview.do?cid=5c3c30382c7bbcf6)

[Introduction of Deadlock in Operating System](https://www.geeksforgeeks.org/introduction-of-deadlock-in-operating-system)

[[운영체제] 7장 교착상태](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=three_letter&logNo=220380867227)

[운영체제, OS, Operating System](https://truemind5.blogspot.com/2017/04/12-1.html)



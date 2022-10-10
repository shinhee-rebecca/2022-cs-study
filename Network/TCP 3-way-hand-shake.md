## TCP 3-way-hand-shake

## 목차

1. [TCP Header](#tcp-header)
    - [TCP FLAG](#tcp-flag)
1. [TCP 3-Way Handshake](#tcp-3-way-handshake)
    - [1st. SYN](#1st-syn)
    - [2nd. SYN + ACK](#2nd-syn--ack)
    - [3rd. ACK](#3rd-ack)
    - [요청자 & 수신자 State](#요청자--수신자-state)
    - [3-way handshake 이후 데이터 송수신 과정](#3-way-handshake-이후-데이터-송수신-과정)
1. [TCP 4-Way Handshake](#tcp-4-way-handshake)
    - [1st. FIN(요청자의 FIN)](#1st-fin요청자의-fin)
    - [2nd. ACK(수신자의 ACK)](#2nd-ack수신자의-ack)
    - [3rd. FIN(수신자의 FIN)](#3rd-fin수신자의-fin)
    - [4th. ACK(의 ACK)](#4th-ack요청자의-ack)
    - [요청자(Active Close) & 수신자(Passive Close) State](#요청자active-close--수신자passive-close-state)
    - [CLOSE_WAIT](#close_wait)
    - [TIME_WAIT](#time_wait)
        - [TCP TIMESTAMP](#tcp-timestamp) 
1. [SYN Flooding 공격](#syn-flooding-공격)
    - [SYN Flooding 공격이란?](#syn-flooding-공격이란)
    - [SYN Cookie](#syn-cookie)
        - [SYN Flooding 공격 방지](#syn-flooding-공격-방지) 
        - [정상적인 세션 연결과정](#정상적인-세션-연결과정)
        - [SYN Cookie 정보](#syn-cookie-정보)
    - [SYN Cookie 내용 정리](#syn-cookie-내용-정리) 

---

## TCP Header

![header](https://user-images.githubusercontent.com/75410527/192298825-eab87d86-cebf-41c0-87bb-3cee5e573814.png)

### TCP FLAG

- TCP FLAG란, 클라이언트와 서버간의 통신 연결, 해제 과정에서 나온 6가지 종류의 플래그
- TCP Header에는 Code Bit(Flag bit)라는 부분이 존재한다. 이 부분은 총 `6Bit`로 이뤄져 있으며 각각 한 bit들이 의미를 갖고 있다.
  - Urg, Ack, Psh, Rst, Syn, Fin 순서로 되어 있으며 해당 위치의 비트가 1이면 `해당 패킷이 어떤 내용을 담고 있는 패킷인지` 나타낸다.
  - 예를 들어, SYN 패킷일 경우엔 `000010`이고 ACK 패킷일 경우에는 `010000`이 된다.

#### SYN(Synchronize sequence number) - 연결요청 패킷

- TCP에서 세션을 성립할 때 가장 먼저 보내는 패킷
- 시퀀스 번호를 임의적으로 설정하여 세션을 연결하는 데 사용되며 초기에 시퀀스 번호를 보냄

#### ACK(Acknowledgement) - 응답 패킷

- 송신측으로부터 패킷을 잘 받았다는 걸 알려주는 패킷 플래그, 다른 플래그와 같이 출력되는 경우도 있음
- 보낸 사람의 시퀀스 번호에 TCP 계층에서 길이 또는 데이터 양을 더한 것과 같은 ACK Number(일반적으로 +1)를 보냄
  - 동기화 요청에 대한 답변 : Client의 Sequence Number+1을 하여 ACK로 돌려줌
- ACK 응답을 통해 보낸 패킷에 대한 성공, 실패를 판단하여 재전송하거나 다음 패킷을 전송함

> 연결을 성립하려면 서로 통신이 가능한지를 먼저 파악하기 위해 패킷을 먼저 주고 받습니다.  
> `Hand-Shake` 과정에서는 `SYN와 ACK 두 종류의 패킷`을 주고 받습니다.  
> 그 이유는 `요청`과 `응답`에 대한 패킷을 주고 받아야 하기 때문에 두 종류입니다.  

#### FIN(Finish) - 연결 종료 요청 패킷

- `더 이상 전송할 데이터가 없고` 세션 연결을 종료시키겠다는 플래그
- 세션 연결을 종료시킬 때 사용됨

#### RST(Reset) - 연결 재설정 플래그

- 비정상적인 세션 연결 끊기에 해당하는 패킷
- 이 패킷을 보내는 곳이 현재 접속하고 있는 곳과 즉시 연결을 끊고자 할 때 사용한다.
- **TCP가 RST 패킷을 송신하는 경우**
  - 연결을 맺고 있지 않은 호스트로부터 TCP 패킷이 온 경우 
    - 정당한 사용자가 아닌 악의적 사용자를 막아준다
  - 잘못됐거나 부정확한 시퀀스 넘버나 Ack 넘버 필드를 가지는 메시지를 수신한 경우
    - 정당한 사용자와의 통신에 공격자의 개입을 막아준다. 
  - Listen 상태가 아닌 포트로 SYN 메시지를 받은 경우 

#### PSH(Push) - 넣기 플래그

- 버퍼가 채워지기를 기다리지 않고 받는 즉시 전달
- 버퍼링 없이 7 Layer Application Layer의 응용 프로그램에게 바로 전달하는 플래그

#### URG(Urgent) - 긴급 데이터 플래그

- 긴급한 데이터의 우선순위를 다른 데이터의 우선순위보다 높여 긴급하게 전달하는 플래그

## TCP 3-Way Handshake

- 3-Way Handshake 는 TCP의 접속과정
- TCP 3-Way Handshake는 TCP 통신을 이용하여 데이터를 전송하기 위해 네트워크 연결을 설정(Connection Establish) 하는 과정


![스크린샷 2022-09-26 오후 10 18 03](https://user-images.githubusercontent.com/75410527/192286803-15bab168-51d9-4dfb-b3d6-c5e5ddd7771e.png)

|             | 요청자                                                                                                                                                                                                                        |               |              | 수신자                                                                                                                                                                         |               |
|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| Start State | Action                                                                                                                                                                                                                        | Move To State | Start State  | Action                                                                                                                                                                         | Move To State |
| CLOSED      | 요청자는 수신자가 수동 OPEN을 수행하고 연결을 수락할 준비가 될 때까지 아무 것도 할 수 없습니다. (시도할 수 있지만 수신자가 준비될 때까지 아무 것도 수행되지 않습니다.)                                                        | —             | CLOSED       | 수신자는 수동 OPEN을 수행하여 연결을 위한 전송 제어 블록(TCB)을 만들고 요청자로부터 연결 요청(SYN)을 받을 준비를 합니다.                                                     | LISTEN        |
| CLOSED      | **`1단계 전송`**: 요청자는 활성 OPEN을 수행하여 연결을 위한 전송 제어 블록(TCB)을 생성하고 수신자에 SYN 메시지를 보냅니다.                                                                                                    | SYN-SENT      | LISTEN       | 수신자는 요청자의 커넥션 요청을 기다립니다.                                                                                                                                  | —             |
| SYN-SENT    | 요청자는 자신이 보낸 SYN과 수신자의 SYN에 대한 ACK를 받기를 기다립니다.                                                                                                                                                     | —             | LISTEN       | **`1단계 수신, 2단계 전송`**: 수신자가 요청자로부터 SYN을 수신합니다. 수신자가 보낼 SYN과 요청자의 SYN에 대한 ACK가 포함된 단일 SYN+ACK 메시지를 요청자에 다시 보냅니다. | SYN-RECEIVED  |
| SYN-SENT    | **`2단계 수신, 3단계 전송`**: 요청자는 요청자의 SYN에 대한 ACK가 포함된 SYN+ACK를 수신자에서 수신하고 수신자로부터 SYN을 받습니다. 수신자의 SYN에 대한 ACK를 수신자에게 보냅니다. 요청자는 이제 연결 설정이 완료되었습니다. | ESTABLISHED   | SYN-RECEIVED | 수신자는 이전에 보낸 SYN에 대한 ACK를 기다립니다.                                                                                                                                | —             |
| ESTABLISHED | 요청자는 수신자가 정상적으로 작동할 수 있도록 연결 설정이 완료되기를 기다리고 있습니다.                                                                                                                                     |               | SYN-RECEIVED | **`3단계 수신`**: 수신자는 SYN에 대한 ACK를 수신하고 이제 연결 설정이 완료됩니다.                                                                                                  | ESTABLISHED   |
| ESTABLISHED | 요청자는 정상적인 데이터 전송 작업을 수행할 준비가 되었습니다.                                                                                                                                                            |               | ESTABLISHED  | 수신자가 정상적인 데이터 전송 작업을 수행할 준비가 되었습니다.                                                                                                                   |               |


![TCP-connection-1](https://user-images.githubusercontent.com/75410527/192292098-9c8b5a2e-ea39-4990-b660-548ce8009087.png)

> Q. 왜 3-way ? 2-way로는 불가능?  
> 
> TCP 커넥션은 `양방향성(bidirectional)` connection이기 때문이다.    
> 요청자가 수신자에게 존재를 알리고 패킷을 보낼 수 있다는 것을 알리듯, 수신자도 요청자에게 존재를 알리고 패킷을 보낼 수 있다는 신호를 보내야 한다.  
>
> 비유를 들자면 아래와 같은 과정이다.  
> 1. 요청자가 자신의 목소리가 들리는지 물어본다(SYN)
> 2. 수신자는 요청자의 목소리가 들린다고 말한다.(SYN+1) 그리고 자신의 목소리가 들리는지 물어본다.(ACK)
> 3. 요청자는 수신자의 목소리가 들린다고 말한다.(ACK+1)

#### full-duplex(전이중) 통신의 구성

- `1nd, 2st` 과정에서는 `요청자→수신자 방향`에 대한 연결 파라미터(시퀀스 번호)를 설정하고 이를 승인한다.
- `2nd, 3rd` 과정에서는 `수신자→요청자 방향`에 대한 연결 파라미터(시퀀스 번호)를 설정하고 이를 승인한다.

이를 통해 full-duplex 통신이 구축됩니다.

### 1st. SYN

- 요청자는 수신자와 커넥션을 연결하기 위해 SYN 패킷을 보낸다. (seq : x)
- 요청자가 최초로 데이터를 전송할 때 Sequence Number를 `임의의 랜덤 숫자로 지정`하고, `SYN 플래그 비트`를 1로 설정한 세그먼트를 전송한다.

#### PORT 상태

- 요청자: `CLOSE` => `SYN-SENT` 상태로 변함
- 수신자: `LISTEN`

> Q. 왜 초기 Sequence Number(ISN)는 임의의 랜덤 숫자?  
>
> Connection을 맺을 때 사용하는 포트(Port)는 유한 범위 내에서 사용하고 시간이 지남에 따라 재사용된다.  
> 따라서 두 통신 호스트가 과거에 사용된 포트 번호 쌍을 사용하는 가능성이 존재한다.  
> 수신자 측에서는 패킷의 SYN을 보고 패킷을 구분하게 된다.  
> 난수가 아닌 순차적인 Number가 전송된다면 `이전의 Connection으로부터 오는 패킷으로 인식`할 수 있다.  
> 이런 문제가 발생할 가능성을 줄이기 위해서 난수로 ISN을 설정한다.  

### 2nd. SYN + ACK

- 수신자가 SYN(x) 패킷을 받고, 요청자에게 받았다는 신호인 `ACK`와 `SYN` 패킷을 보냄 (seq: y, ack: x+1)
  - 접속요청을 받은 수신자가 요청을 수락했으며, 접속 요청을 한 프로세스인 요청자도 포트를 열어달라는 메세지 전송 
  - ACK Number필드를 `Sequence Number+1`로 지정하고 `SYN과 ACK 플래그 비트를 1로 설정`한 세그먼트 전송 (seq=y, ack=x+1, SYN, ACK)

#### PORT 상태

- 요청자: `SYN_SENT`
- 수신자: `SYN-RECEIVED(SYN_RCV)`

### 3rd. ACK

- 요청자는 수신자의 응답으로 온 `ACK(x+1)`와 `SYN(y)` 패킷을 받고, `ACK(y+1)` 패킷을 서버로 보냄
  - 마지막으로 접속 요청을 한 요청자가 수락 확인을 보내 연결을 맺음 (ACK)
  - 이때, 전송할 데이터가 있으면 이 단계에서 데이터를 전송할 수 있다.

#### PORT 상태

- 요청자: `ESTABLISED`
- 수신자: `SYN-RECEIVED` => ACK => `ESTABLISED`


### 요청자 & 수신자 State

|       상태       |                           설명                           |
|:----------------:|:--------------------------------------------------------:|
|    **CLOSED**    | 연결 수립을 시작하기 전의 기본 상태 (연결 없음)          |
|    **LISTEN**    | 포트가 열린 상태로 연결 요청 대기 중                     |
|   **SYN-SENT**   | SYN 요청을 한 상태                                       |
| **SYN-RECEIVED** | SYN 요청을 받고 상대방의 응답을 기다리는 중              |
|  **ESTABLISEHD** | 연결의 수립이 완료된 상태, 서로 데이터를 교환할 수 있다. |

### 3-way handshake 이후 데이터 송수신 과정

- 3-way handshake를 통해 TCP 연결이 수립된 이후, 데이터를 송수신할 때는 TCP 헤더의 `Sequence number`와 `Acknowledgement number`를 사용한다.
- 각 데이터는 몇 번째 데이터인지를 나타내는 정보를 포함함으로써, 중간에 결번발생 및 타임아웃을 통해 소멸데이터의 존재를 전송자가 인지할 수 있고 이에 한 번 더 전송할 수 있다.
- ACK와 SEQ의 `첫 수는 난수`로 주어지고, `한 번 전송될 때마다 각자의 번호가 1씩 증가해서 연결`되도록 설계되어있다.
  - 자신이 보낼 데이터의 `1 bytes당 시퀀스 번호를 1씩 증가`시키며 데이터의 순서를 표현하다 `4,294,967,296`를 넘어갈 경우 다시 0부터 시작한다.

#### Sequence Number(이하 SeqNumber) 

> 32 bits

- 송신 측 -> 수신 측 `“이 데이터가 몇 번째 데이터인지" 알려주는 역할`을 수행한다. (이 덕분에 TCP는 데이터를 송수신할 때 순서를 보장해 줄 수 있다.)
- “난 n번째 데이터를 보낼거야”
- 전송된 데이터에 일련번호를 부여하면, 수신자는 원래 데이터의 “몇 번째 데이터”를 받았는지 알 수 있다.

#### Acknowledgement Number(이하 AckNumber)

> 32 bits

- 수신 측이 `몇 번째 데이터를 수신했는지` 송신 측에 `알려주는 역할`을 수행한다.
- “나 2000번째 데이터 방금 잘 받았어! 다음에는 2200번째 데이터를 줘!”
- 다음 번호의 데이터를 요청하는데도 사용한다.
- ex) 10번 데이터를 수신하면, 11번 데이터를 송신 측에 요청한다.

![images_pu1etproof_post_eb19f6d3-6e98-47b4-8cd4-ee1b2bb5d749_pasted image 0 (3)](https://user-images.githubusercontent.com/75410527/192318921-866697fa-b063-4df3-b56f-f6d62c164bb3.png)

초기 `SeqNumber` 3001과 `AckNumber` 4001은 연결을 수립하는 3-way handshaking 과정에서 결정된다.

1. COM1은 COM2로 200 byte의 데이터를 전송한다.
2. COM2은 데이터를 수신하고, 다음에 수신하고자 하는 데이터 번호 3201을 `AckNumber`에 넣는다.
3. COM1은 COM2로부터 3201번부터 200byte의 데이터를 전송한다.
4. COM2는 데이터를 수신하고, 다음에 수신하고자 하는 데이터 번호 3401을 `AckNumber`에 넣는다.
5. 1~4 과정을 데이터 전송이 완료될 때까지 반복한다.

그러나 데이터가 항상 올바르게 전달되지는 않는다. -> 이 때, 일련번호와 확인응답번호를 사용해, 데이터가 손상되거나 유실된 경우 데이터를 재전송한다. (재전송 제어)

## TCP 4-Way Handshake

#### 요청자(Active Close)
#### 수신자(Passive Close)

> 클라이언트와 서버, `둘 중에 어느 쪽이든` 연결 종료 요청을 시작할 수 있기 때문에 이런 용어를 사용
> 먼저 연결 생성 요청을 했던 쪽이 먼저 연결 종료 요청을 보낼 수도 있고, 반대로 처음에는 연결 생성 요청을 당했던 쪽이 이번에는 먼저 연결 종료 요청을 보낼 수도 있다.
> 예: 서버가 먼저 종료하겠다고 FIN을 보낼 수 있고, 이런 경우 서버가 FIN_WAIT1 상태가 된다. 

- 4-Way Handshake은 연결을 해제 (Connecntion Termination)하는 과정이다. 여기서는 `FIN 플래그 패킷을 이용`한다.

![4-way](https://user-images.githubusercontent.com/75410527/192326359-722ba9c3-30e0-4e4c-917c-a14e545c99c2.png)

> Q. TCP의 연결 설정 과정(3단계)과 연결 종료 과정(4단계)이 단계가 차이나는 이유?
> 
> 요청자가 데이터 전송을 마쳤다고 하더라도 수신자는 아직 보낼 데이터가 남아있을 수 있기 때문에 일단 FIN에 대한 ACK만 보내고, 데이터를 모두 전송한 후에 자신도 FIN 메시지를 보내기 때문이다.

### 1st. FIN(요청자의 FIN)

<img width="400" alt="스크린샷 2022-09-27 오후 4 15 28" src="https://user-images.githubusercontent.com/75410527/192458960-e63922f1-4389-4789-afe5-7cd225672671.png">

- 먼저 연결을 종료하고자 하는 요청자가 FIN 패킷을 상대방에게 보내면서 `FIN_WAIT1` 상태로 들어서게 된다.
- 이 때 FIN 패킷에도 시퀀스 번호가 포함되어 있는데 이번에는 랜덤한 값이 아니라, 자신이 보내야할 순서에 맞는 시퀀스 번호를 사용하면 된다.
- 이때, 클라이언트는 서버에게 연결을 종료한다는 `FIN` 패킷을 보낸다.
  - 이때 FIN 패킷에는 실질적으로 `ACK`도 포함되어있다.
  - 그래서 실질적으로 `FIN+ACK` 패킷을 보내고 있다.

> Q. 왜 승인 번호를 함께 묶어서 FIN+ACK로 보내고 있는 것일까?
>
> Half-Close 기법. 
> 
> Half-Close 기법은 말 그대로 연결을 종료하려고 할 때 완전히 종료하는 것이 아니라 반만 종료하는 것이다.  
> Half-Close를 사용하면 요청자가 처음 보내는 FIN 패킷에 승인 번호를 함께 담아서 보내게 되는데,   
> 이때 이 승인 번호의 의미는 “일단 연결은 종료할 건데 귀는 열어둔다. 이 승인 번호까지 처리했으니까 마저 보낼 거 있으면 보내”라는 의미가 된다.  
> 즉, 반만 닫겠다는 말의 의미는 연결을 종료할 때 전송 스트림과 수신 스트림 중 하나만 우선 닫겠다는 것을 의미하는 것이다.  
> 이후 수신자는 미처 못 보낸 데이터가 있다면 열심히 보낼 것이고, 이에 요청자는 아직 살아있는 수신 스트림을 사용하여 데이터를 처리한 후 ACK 패킷을 응답으로 보낼 수 있다.   
> 이후 수신자가 모든 데이터를 처리하고나면 다시 요청자에게 FIN 패킷을 보냄으로써 모든 데이터가 처리되었다는 신호를 보내준다.  
> 그럼 요청자는 그때 나머지 반을 닫으면서 조금 더 안전하게 연결을 종료할 수 있는 것이다.  

#### PORT 상태

- 요청자(Active Close): `ESTABLISED` => `FIN_WAIT_1`
- 수신자(Passive Close): `ESTABLISED`

### 2nd. ACK(수신자의 ACK)

<img width="400" alt="스크린샷 2022-09-27 오후 4 13 27" src="https://user-images.githubusercontent.com/75410527/192458601-5ae7ab3e-147a-46b9-ad54-ff63abb6162b.png">

- 요청자로부터 FIN 패킷을 받은 수신자는 `요청자가 보낸 시퀀스 번호 + 1`로 승인 번호를 만들어 다시 요청자에게 응답해주면서 `CLOSE_WAIT` 상태에 들어간다.
  - 수신자는 ACK Number 필드를 (Sequence Number + 1)로 지정하고, ACK 플래그 비트를 1로 설정한 세그먼트를 전송한다.
- 수신자는 요청자에게 응답을 보내고 `아직 남은 데이터가 있다면 마저 전송을 마친 후`에 close()를 호출
  - 모든 전송이 끝났다면 명시적으로 `close()`나 `shutdown()`과 같은 함수를 호출하여 다음 단계로 넘어갈 것이다. 
- 요청자는 수신자에게 ACK를 받은 후에 수신자가 남은 데이터 처리를 끝내고 FIN 패킷을 보낼 때까지 기다리게 됩니다. (`FIN_WAIT_2`)
  - 만약 이 단계에서 수신자의 데이터 처리가 끝나도 연결 종료 함수가 명시적으로 호출되지 않으면 다음 상태로 넘어갈 수 없기 때문에 데드락이 발생할 가능성이 있습니다. 

#### PORT 상태

- 요청자(Active Close): `FIN_WAIT_1` => `FIN_WAIT_2`
- 수신자(Passive Close): `ESTABLISED` => `CLOSE_WAIT`

> FIN_WAIT_2
> 
> 요청자는 수신자로부터 승인 번호를 받고 자신이 보냈던 시퀀스 번호와 승인 번호의 차가 1이 맞는지 확인한다. 
> 하지만 아직 수신자의 데이터 전송이 전부 끝나지 않았을 수도 있기에 FIN_WAIT2 상태로 들어가서 수신자가 연결 종료를 허락하는 FIN 패킷을 보내줄 때까지 기다린다.
>
> 방금 CLOSE_WAIT 섹션에서 설명했듯이 여기서부터는 수신자가 다시 FIN 패킷을 보내줄 때까지 요청자는 계속 대기하는 시간이다.
> 하지만 CLOSE_WAIT와 다르게 무한정 대기만 하는 것은 아니고 커널 파라미터로 타임아웃이 정해져있는 경우, 일정 시간이 경과하면 자동으로 다음 단계로 넘어갈 수 있다.

### 3rd. FIN(수신자의 FIN)

<img width="400" alt="스크린샷 2022-09-27 오후 4 12 42" src="https://user-images.githubusercontent.com/75410527/192458431-bbcdd7d4-4708-4b55-ba59-34215f914ead.png">


- 수신자는 자신이 처리할 데이터가 더 이상 없다면 연결을 종료하는 함수를 명시적으로 호출하고, 아까 요청자가 보냈던 연결 종료 요청에 합의한다는 의미로 요청자에게 다시 `FIN 패킷`을 보낸다.
- 이때 수신자가 보내는 FIN 패킷에 담기는 `시퀀스 넘버`는 자신이 이번에 전송해야 하는 데이터의 시퀀스 번호를 그대로 사용하며, 승인 번호는 마지막으로 자신이 응답했던 승인 번호를 그대로 사용한다.
- 이후 수신자는 `LAST_ACK` 상태로 들어가며 요청자가 다시 승인 번호를 보내줄 때까지 대기한다.


#### PORT 상태

- 요청자(Active Close): `FIN_WAIT_2`
- 수신자(Passive Close): `CLOSE_WAIT` => `LAST_ACK`

### 4th. ACK(요청자의 ACK)

<img width="400" alt="스크린샷 2022-09-27 오후 4 11 18" src="https://user-images.githubusercontent.com/75410527/192458205-0775b4b3-3b2d-4ac1-8c4b-2e63e4d64d0f.png">

- 수신자가 보낸 FIN 패킷을 받은 요청자는 다시 수신자가 보낸 `시퀀스 번호+1`로 승인 번호를 생성하여 수신자에게 `ACK` 패킷으로 응답한다.
- 이후 요청자는 아직 수신자로부터 받지 못한 데이터가 있을 수 있으므로 `TIME_WAIT` 상태로 들어가며, `실질적인 연결 종료 과정`에 들어가게 된다.

> 이때, TIME_WAIT의 역할은 의도하지 않은 에러로 인해 연결이 데드락에 빠지는 것을 방지한다.
> TIME_WAIT에서 대기하는 시간은 2 MSL(Maximum Segement Lifetime)으로 정의되어 있으며, 정확한 MSL의 시간 값은 커널 파라미터로 정의되어있다.

```
chanyoung@MacBook-Pro-2 ~ % sysctl net.inet.tcp | grep msl
net.inet.tcp.msl: 15000
```

> 15초로 설정되어있다. 
> 즉, TIMEWAIT 상태에서 30초 정도 대기한다는 것이다. 참고로 이 값은 변경할 수 없기 때문에 TIMEWAIT에서 소비되는 시간은 변경할 수 없다.
> 하지만 CLOSE_WAIT와 마찬가지로 여기서도 데드락이 발생할 수 있다.

- 요청자가 보낸 ACK 패킷을 받은 수신자는 `CLOSED` 상태로 들어가며 연결을 완전히 종료한다.
- `TIME_WAIT` 상태에서 2 MSL만큼 시간이 지나면 요청자도 CLOSED 상태로 변경된다. 
  - 위에서 설명했듯이 이 시간은 커널 파라미터에 고정되어 있고, 필자가 사용하고 있는 OSX의 경우 30초 정도이다.

#### PORT 상태

- 요청자(Active Close): `FIN_WAIT_2` => `TIME_WAIT` => `CLOSED`
- 수신자(Passive Close): `LAST_ACK` => `CLOSED`

### 요청자(Active Close) & 수신자(Passive Close) State

|       상태      |                                                           설명                                                          |
|:---------------:|:-----------------------------------------------------------------------------------------------------------------------:|
|    **CLOSED**   | 연결 수립을 시작하기 전의 기본 상태 (연결 없음)                                                                         |
| **ESTABLISEHD** | 연결의 수립이 완료된 상태, 서로 데이터를 교환할 수 있다.                                                                |
|  **CLOSE-WAIT** | 상대방의 FIN(종료 요청)을 받은 상태. 상대방 FIN에 대한 ACK를 보내고 애플리케이션에 종료를 알린다.                       |
| **LAST-ACK**    | CLOSE-WAIT 상태를 처리 후 자신의 FIN 요청을 보낸 후 FIN에 대한 ACK를 기다리는 상태.                                     |
|  **FIN-WAIT-1** | 자신이 보낸 FIN에 대한 ACK를 기다리거나 상대방의 FIN을 기다린다.                                                        |
| **FIN-WAIT-2**  | 자신이 보낸 FIN에 대한 ACK를 받았고 상대방의 FIN을 기다린다. 수신자가 다시 FIN 패킷을 보내줄 때까지 요청자는 계속 대기하는 시간.                                                            |
| **CLOSING**     | 상대방의 FIN에 ACK를 보냈지만 자신의 FIN에 대한 ACK를 못받은 상태                                                       |
| **TIME-WAIT**   | 모든 FIN에 대한 ACK를 받고 연결 종료가 완료된 상태. 새 연결과 겹치지 않도록 일정 시간 동안 기다린 후 CLOSED로 전이한다. |

### CLOSE_WAIT

- `Passive Close` 측이 `CLOSE_WAIT` 상태에 빠지면 Active Close 측은 FIN을 못 받는 상태이기 때문에 FIN_WAIT2에서 마찬가지로 대기하게 된다. 
  - 그러나 `CLOSE_WAIT`와 달리 FIN_WAIT2는 일정 시간이 경과하면 TIME_WAIT 상태가 된다.
- 커널 옵션으로 타임아웃 조절이 가능한 FIN_WAIT이나 재사용이 가능한 TIME_WAIT과 달리, **`CLOSE_WAIT는 포트를 잡고 있는 프로세스의 종료 또는 네트워크 재시작 외에는 제거할 방법이 없다.`**
  - 즉, 로컬 어플리케이션이 정상적으로 close()를 요청하는 것이 가장 좋은 방법

> No, there is no timeout for CLOSE_WAIT. 
> 저마다 강조하는 바를 살펴봐도 CLOSE_WAIT는 커널 옵션이나 설정으로 타임아웃을 줄 수 없으며 로컬 어플리케이션의 문제이기 때문에 정상적인 문제 해결이 필요하다는 지적  

#### CLOSE_WAIT 원인

예시

1. 파일서버가 필요해 별도 웹 서버를 구성하지 않고 로컬 톰캣의 웹 서버를 사용
2. Static HTML 파일을 올려두고 톰캣에서 구동중인 웹앱의 HttpClient에서 로컬 자기 자신(동일한 톰캣)을 호출해 HTML을 받아가도록 구성
3. 그러나, 부하를 높이니 점점 느려지다 HTML 조차 내려주지 못하는 행업 상태 발생
4. 모든 소켓이 CLOSE_WAIT 상태에 빠짐

> 대부분의 쓰레드가 검색 결과를 받아오지 못하고 대기중(WAITING)인 상태   
> 이 검색 결과는 로컬에서 받아오는 것입니다. 즉, 로컬은 행업 상태여서 검색 결과를 보내주지 못하는 상황이고, 쓰레드는 검색 결과를 받기 위해 대기하는 상황입니다.  
> 원래 일정 시간이 경과하면 타임아웃으로 끊어야 하는데, 행업 상태에 빠지다보니 이 조차도 처리되지 않고 서로가 서로를 기다리는 상황. 
> 즉, 보낼 수도 없고 받을 수도 없는 일종의 교착 상태(deadlock)가 원인. 

### TIME_WAIT

- TIME_WAIT이란, `Active Close` 즉, 먼저 close()를 요청한 곳에서 최종적으로 남게 되며, 2*MSL동안 유지된다.
  - 먼저 연결을 끊는 (active closer) 쪽에 생성되는 소켓으로, 혹시 모를 패킷 전송 실패에 대비하기 위하여 존재하는 소켓이다.

#### TIME_WAIT는 왜 필요할까?

- 만일 TIME_WAIT이 짧다면 아래와 같은 두 가지 문제가 발생합니다.

![스크린샷 2022-09-27 오후 5 30 12](https://user-images.githubusercontent.com/75410527/192475448-8250a16c-29c0-46ff-8524-708acfd06dd5.png)

- `첫 번째는 지연 패킷이 발생할 경우`입니다.
  - 이미 다른 연결로 진행되었다면 지연 패킷이 뒤늦게 도달해 문제가 발생합니다. 매우 드문 경우이긴 하나 때마침 SEQ까지 동일하다면 잘못된 데이타를 처리하게 되고 데이타 무결성 문제가 발생합니다.

![스크린샷 2022-09-27 오후 5 31 42](https://user-images.githubusercontent.com/75410527/192475661-13ab6077-e939-403a-891d-863160776999.png)

- `두 번째는 원격 종단의 연결이 닫혔는지 확인해야 할 경우`입니다.
  - 마지막 ACK 유실시 상대방은 LAST_ACK 상태에 빠지게 되고 새로운 SYN 패킷 전달시 RST를 리턴합니다. 
  - 새로운 연결은 오류를 내며 실패합니다. 이미 연결을 시도한 상태이기 때문에 상대방에게 접속 오류 메시지가 출력될 것입니다.
  - 따라서 반드시 TIME_WAIT이 일정 시간 남아 있어서 패킷의 오동작을 막아야 합니다.

### TCP TIMESTAMP

- TCP timestamp가 사용되면 TIME_WAIT 상태의 소켓에 통신이 이루어진 마지막 시간(timestamp)를 기록할 수 있습니다.
- TCP timestamp는 패킷의 reordering을 판별하는 요소에 sequence number 뿐만 아니라 timestamp 값을 사용하겠다는 것입니다.
- PAWS가 구현된 TCP stack은 timestamp가 포함된 어느 패킷을 받았을 때 이 timestamp를 기록해두며 connection 별로 관리함

> wrapped sequence number 문제를 해결하기 위해, RFC 1323에서는 PAWS(Protection against Wrapped Sequence Numbers)라는 방법을 제안

- `net.ipv4.tcp_timestamps`
  - RFC 1323에서 고성능 익스텐션으로 제안된 옵션 중 하나이며 여기에는 `두 개의 4 바이트 타임스탬프 필드`가 있다. 
- `net.ipv4.tcp_tw_reuse`를 활성화하면, 새로운 타임스탬프가 기존 커넥션의 가장 최근 타임스탬프보다도 큰 경우 TIME_WAIT 상태인 커넥션을 재사용 

```bash
$ sysctl -w ipv4.tcp_timestamps="1"
$ sysctl -w net.ipv4.tcp_tw_reuse="1"
```

처음에 TIME_WAIT이 짧을때 두 가지 문제가 발생할 수 있다고 언급했는데 이 부분에 대해서는 다음과 같이 해결 가능

- 첫 번째 문제는 쉽다. 동일한 SEQ라도 이미 지난 타임스탬프 이므로 확인 후 그냥 버리면 된다.
- 두 번째 문제도 타임스탬프로 해결된다.
  - 서버가 ACK을 받지 못한 상태에서 새로운 커넥션이 SYN을 보내면 타임스탬프를 비교해 무시한다. 
  - 그러는 사이 서버의 FIN이 재전송된다. 그러면 SYN_SENT 상태에 있던 클라이언트는 RST를 보낸다. 
  - 이제 서버가 LAST_ACK 상태를 빠져나온다. 
  - 그러는 사이 ACK을 받지 못한 클라이언트는 1초 후 다시 SYN을 전송한다. 
  - 이제 서버도 SYN+ACK을 보낸다. 이제 둘은 정상적으로 ESTABLISHED 된다.

#### 연결 종료의 4-way handshake 과정 요약

```bash
FIN+ACK, seq = K, ack = L
ACK, seq = L, ack = K + 1
FIN+ACK, seq = L, ack = K + 1
ACK, seq = K, ack = L + 1
```

## SYN Flooding 공격

### SYN Flooding 공격이란?

- 3-way-hand-shake 과정에서 Server는 SYN 패킷과 ACK 패킷을 Client에게 전달합니다.
- 그리고 Server는 Client의 접속을 받아들이기 위해 `RAM(메모리)`에 `일정 공간을 확보해두고 대기`합니다.
  - SYN 패킷에 대한 세션을 생성하고 응답을 기다림 
- 근데 이 때 Client가 SYN 패킷만 지속적으로 보내고 ACK 패킷을 보내지 않으면 `Server`는 Client 연결을 받아들이기 위해 RAM(메모리) 공간을 점점 더 많이 확보해둔 상태에서 대기합니다.
  - Server의 세션 리소스가 고갈된 상황 
- 그리고 Server의 RAM이 꽉 차게 되면 더 이상 연결을 받아들일 수 없게 되고, Server는 서비스를 할 수 없는 상태가 됩니다.  

![synflood](https://user-images.githubusercontent.com/75410527/194807083-a12cfbec-4287-42ab-8641-ec553aaba6cd.png)

### SYN Cookie

- SYN Cookie는 클라이언트의 SYN 패킷에 대한 SYN/ACK 패킷을 전송할 때 `ISN(Initial Sequence Number)`에 Cookie 값을 넣어 전송하는 방식입니다.
- 상대방이 ACK 패킷을 보내면, ACK 값을 Cookie 값과 비교하여 연결 유효성을 확인 후 리소스 서버와 TCP 연결을 맺도록 해줍니다.

#### SYN Flooding 공격 방지

- 공격자가 SYN 패킷을 보내 서버의 세션 리소스를 소진시키려고 합니다. 그러나 중간에 L4 장비(방화벽 또는 Anti-DDOS 장비)가 `SYN Cookie` 방식이 설정되어 있습니다.
  - 그래서 공격자의 SYN 패킷과 리소스 서버 사이에 세션을 곧바로 생성하지 않습니다.
- L4 장비는 클라이언트의 SYN 패킷에 대해 SYN Cookie 값으로 클라이언트가 응답 패킷으로 사용해야 하는 sequence 정보를 모두 포함한 값을 내려줍니다.
  - L4 장비는 `Cookie에 모든 응답값을 포함했으므로 클라이언트의 SYN에 대한 세션을 기억하지 않습니다.` (즉, 세션 테이블에 별도로 저장하지 않음)
- 그래서 많은 양의 요청이 들어와도 세션 테이블이 고갈되지 않습니다.
  - 즉, L4에서 리소스 서버와의 연결을 허용하지 않습니다.

![스크린샷 2022-10-10 오후 3 15 42](https://user-images.githubusercontent.com/75410527/194807587-1c7414b1-202c-4d8c-a437-4b0c2409418b.png)

#### 정상적인 세션 연결과정

SYN Cookie를 사용했을 때 올바른 요청이라면 아래와 같은 순서로 정상적인 세션 연결과정을 거칩니다.

1. Client의 SYN 패킷 수신
2. L4(방화벽)은 응답으로 SYN/ACK 패킷에 추가로 Cookie 값을 넣어 Client에 송신
3. Client는 SYN/ACK에 대한 응답으로 ACK Number 필드에 Cookie+1한 값을 L4에 송신
4. L4는 수신한 ACK 패킷의 ACK Number - 1한 값이 Cookie 값과 일치하면 리소스 서버와 TCP Connection을 설정시켜줌
    - L4는 정상적인 Client로부터 timestamp 내에 응답이 오면 해당 Client의 원래 Cookie 값을 알아낼 수 있어 값 비교가 가능

이처럼 정상적인 연결의 경우 SYN Cookie 값에 대한 유효성이 확인되었기에 L4는 클라이언트가 리소스 서버와 세션을 맺도록 중간다리 역할을 해줌

![스크린샷 2022-10-10 오후 3 22 08](https://user-images.githubusercontent.com/75410527/194808235-59d903fc-bf77-4d17-af65-a47824215c3e.png)

#### SYN Cookie 정보

- SYN Cookie는 TCP Header 중 `Option(40 Byte)` 필드에 sequence 값을 인코딩한 정보를 포함하여 Client로 보냅니다.
- Client는 이 인코딩된 정보를 받아서 자신이 다시 응답해야 할 패킷을 생성

![스크린샷 2022-10-10 오후 3 31 18](https://user-images.githubusercontent.com/75410527/194809169-8bca5800-5301-4a20-8b1f-7a9d2289b2bd.png)

### SYN Cookie 내용 정리

- 클라이언트가 최초로 SYN 패킷으로 리소스 서버와 세션 연결을 하기 위한 시도를 하면, L4(방화벽)이 Cookie 정보에 사용자가 다시 ACK 패킷을 보낼 때 필요한 정보를 미리 포함하여 사용자에게 SYN/ACK 패킷을 내려주고 L4는 해당 내용에 대해 잊어버립니다.
- 정상적인 사용자라면 해당 정보를 분석해서 ACK 패킷을 만들어 보낼 것이고, 이 패킷의 cookie 값을 분석하여 정상적인 세션 연결 응답이라고 L4 판단하면 그 때 리소스 서버와 연결을 허용해줍니다.

---

- 출처
  - [[네트워크] TCP/UDP와 3 -Way Handshake & 4 -Way Handshake](https://velog.io/@averycode/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-TCPUDP%EC%99%80-3-Way-Handshake4-Way-Handshake#-tcp%EC%9D%98-3-way-handshake) 
  - [TCP Connection Establishment Process: The "Three-Way Handshake"](http://www.tcpipguide.com/free/t_TCPConnectionEstablishmentProcessTheThreeWayHandsh-3.htm)
  - [TCP 3-Way Handshake Process](https://www.geeksforgeeks.org/tcp-3-way-handshake-process/)
  - [TCP Flag란 ?](https://hongpossible.tistory.com/entry/TCP-Flag%EB%9E%80)
  - [[TCP] 3-way-handshake & 4-way-handshake](https://asfirstalways.tistory.com/356)
  - [네트워크 스터디 3주차 - TCP 3-way handshake 이후](https://velog.io/@pu1etproof/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%8A%A4%ED%84%B0%EB%94%94-3%EC%A3%BC%EC%B0%A8-TCP-3-way-handshake-%EC%9D%B4%ED%9B%84)
  - [[네트워크] TCP 3-way & 4-way handshake란?](https://seongonion.tistory.com/74#Time-wait%EC%-D%B-%--%EC%--%--%EB%-B%A-%EB%A-%B-%-F)
  - [TCP의 헤더에는 어떤 정보들이 담겨있는걸까?](https://evan-moon.github.io/2019/11/10/header-of-tcpdddd)
  - [TCP가 연결을 생성하고 종료하는 방법, 핸드쉐이크](https://evan-moon.github.io/2019/11/17/tcp-handshake/)
  - [CLOSE_WAIT & TIME_WAIT 최종 분석](https://tech.kakao.com/2016/04/21/closewait-timewait/#%EC%B1%84%EC%9A%A9-%EA%B3%B5%EA%B3%A0)
  - [리눅스 서버의 TCP 네트워크 성능을 결정짓는 커널 파라미터 이야기 - 3편](https://meetup.toast.com/posts/55)
  - [TCP flag(URG, ACK, PSH, RST, SYN, FIN)](https://mindgear.tistory.com/206)
  - [[TCP] TCP 가 RST 패킷 송신하는 경우](https://blog.daum.net/tlos6733/65)
  - [[DOS/DDOS] SYN Flooding 공격에 대해서 알아보자.](https://sata.kr/entry/DOSDDOS-SYN-Flooding-%EA%B3%B5%EA%B2%A9%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)
  - [[Anti-DOS]SYN Cookie](https://eunice513.tistory.com/161#SYN%--Flooding%--%EA%B-%B-%EA%B-%A-)

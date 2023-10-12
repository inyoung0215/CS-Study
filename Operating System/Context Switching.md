# Context Switching
CPU(Control Processing Unit)가 어떤 프로세스(Process)를 실행하고 있는 상태에서

스케줄러(Scheduler)가 인터럽트(Interrupt)를 진행하여 더 높은 우선순위를 가진 프로세스가 실행되어야 할 때,

스케줄러가 레지스터(Register)에 저장된 기존 프로세스 정보 값이나 기존 프로세스 상태 값(= Context)을 커널(Kernal) 내부에 존재하는 PCB(Process Control Block)에 저장하고,

새로운 Context를 교체하는 작업을 Context Switch(Context Switching)라고 한다.

한마디로 스케줄러가 기존 실행 프로세스를 우선순위 때문에 미루고 새 프로세스로 교체해야 할 때 프로세스 상태 값을 교체하는 작업을 말한다.

## Context는 무엇인가?
> 프로그래밍에서 Context는 (동작, 작업들의 집합)을 (정의, 관리, 실행)하도록 하는 (최소한의 상태, 재료, 속성)을 포함하는 (객체, 구조체, 정보)이다.
> 
> [What exactly is context in programming?](https://www.quora.com/What-exactly-is-context-in-programming?no_redirect=1)

OS에서 Context는 CPU가 해당 프로세스를 실행하기 위한 해당 프로세스의 정보이며, 프로세스의 PCB(Process Control Block)에 저장된다.

### PCB(Process Control Block) 
운영체제가 프로세스를 제어하기 위한 프로세스의 상태 정보를 저장하는 구조체이다. 

프로세스 생성시 PCB 가 만들어지며 주기억장치에 저장되다가 프로세스가 완료되면 PCB도 함께 제거된다.

* 운영체제에서 프로세스는 PCB 로 표현된다.
* PCB는 프로세스 상태 관리와 context switching 을 위해서 필요하다.
* PCB 는 프로세스의 중요한 정보들을 담고 있으므로 일반 사용자는 접근하지 못하는 보호된 메모리 영역에 존재한다.

#### ➕ PCB에 저장되는 정보
![image](https://github.com/inyoung0215/CS-Study/assets/65496092/1fab0c8d-f1bf-42f9-b9be-8c64d75cb2b7)
* 포인터: 프로세스의 현재 위치를 저장하는 포인터 정보
* 프로세스 상태(Process State): 프로세스가 현재 실행 중, 대기 중, 종료된 상태 등을 나타내는 정보.
* 프로그램 카운터(Program Counter): 프로세스가 실행 중인 명령어의 주소.
* 레지스터 상태: 프로세스가 사용하는 레지스터 값들.
* 스케줄링 정보: 우선순위, CPU 점유 시간, 대기 시간 등과 같은 스케줄링과 관련된 정보.
* 메모리 관리 정보: 프로세스가 사용하는 메모리 위치와 크기 등.
* 입출력 상태: 프로세스가 사용 중인 입출력 장치와 대기 중인 입출력 요청 목록 등.

#### ➕ PCB는 어떻게 관리되나요?
* Linked List 방식으로 관리
* 프로세스가 생성될 때마다 PCB가 생성되고, 이 PCB는 PCB 리스트의 머리(Head)에 연결됨.
* 주소값으로 연결이 이루어져 있는 연결리스트이기 때문에 삽입 삭제가 용이함.

## Context Switching은 언제 발생하는가?
Context Switch가 발생하는 경우는 멀티태스킹, 인터럽트 핸들링, 사용자 모드와 커널 모드 간의 전환까지, 크게 3가지가 존재한다.

* 멀티태스킹(Multitasking)
  * 실행 가능한 프로세스들이 운영체제의 스케줄러에 의해 조금씩 번갈아가며 수행되는 것을 말한다.
  * 번갈아 가며 프로세스가 CPU를 할당 받는데 이때 Context Switching 한다.
  * 사용자가 체감하기 힘든 속도로 Context Switching되며 프로세스가 처리되기 때문에 동시에 처리되는 것처럼 느껴진다.
* 인터럽트 핸들링(Interrupt handling)
  * 인터럽트란 컴퓨터 시스템에서 예외 상황이 발생했을때 CPU에게 알려 처리할 수 있도록 하는 것을 말한다.
  * 인터럽트가 발생하면 Context Switching한다.
  * 인터럽트 종류
    * I/O request : 입출력 요청
    * time slice expired : CPU 사용시간이 만료
    * fork a child : 자식 프로세스 생성
    * wait for an interrupt : 인터럽트 처리 대기
* 사용자와 커널 모드 전환(User and kernel mode switching)
  * 사용자와 커널 모드 전환은 Context Switch가 필수는 아니지만 운영체제에 따라 발생할수 있다

## Context Switching의 과정과 Overhead

![image](https://github.com/inyoung0215/CS-Study/assets/65496092/f619bad2-59f8-4ce9-a648-2c46dd82f22e)

1. 요청 발생: 인터럽트 또는 트랩에 의한 요청이 발생.(트랩은 소프트웨어 인터럽트)
2. PCB에 저장: 운영체제는 현재 실행중인 프로세스(P0)의 정보를 PCB에 저장.
3. CPU 할당: 운영체제는 다음 프로세스(P1)의 정보를 PCB에서 가져와 CPU를 할당.

위 그림을 보면 프로세스 P0가 실행 중인 상태(excuting)에서 유휴 상태(idle)가 될 때 프로세스 P1이 곧바로 excuting이 되지 않고 idle을 좀 더 하다가 excuting이 된다.

그 이유는 프로세스 P0의 상태를 PCB에 저장하고 프로세스 P1 상태를 PCB에서 가져와야 하기 때문이다.

이 과정에서 CPU는 아무일도 하지않는 시간이 발생하는데 이를 오버헤드(Overhead)라 하고 오버헤드가 잦아지면 성능이 떨어질수 있다.

# ✏️ 관련 질문
## Q1. Context Switching을 사용하는 이유는 무엇인가요? Overhead가 발생하는데도 사용하는 이유가 뭘까요?

Context Switching은 다음과 같은 이유로 사용됩니다

* 다중 작업 처리: 여러 프로세스 또는 스레드를 동시에 실행하여 멀티태스킹을 지원합니다.
* 우선순위 관리: 다양한 작업에 CPU 시간을 할당하여 중요한 작업을 처리하거나 신속한 응답을 제공합니다.
* 안정성: 프로세스 분리를 통해 하나의 프로세스나 스레드의 오류가 다른 것에 영향을 미치지 않도록 합니다.
* 대기 시간 최소화: I/O 작업과 같이 시간이 오래 걸리는 작업 중에도 다른 작업을 수행할 수 있도록 합니다.

Context Switching을 진행하는 동안 cpu는 다른 작업을 할 수 없는데(오버헤드), 

보통 이 시간보다 I/O 작업이 더 오래 걸리기 때문에 Context Switching을 통해

CPU가 I/O 작업이 완료될 때까지 대기하지 않고 다른 작업을 처리할 수 있도록 하면

시스템의 전체 처리량을 최적화하고 CPU 자원을 효율적으로 활용할 수 있습니다.

##  Q2. 스레드와 프로세스 중 Context Switching이 빠른 것은?
![image](https://github.com/inyoung0215/CS-Study/assets/65496092/c5095f87-6e76-463e-8ee5-e2f843e37f09)

동일한 프로세스 속에서 스레드는 스택(Stack)을 제외한 코드(Code), 데이터(Data), 힙(Heap) 영역을 공유합니다.

따라서, thread context switching이 발생할 경우 스택 및 간단한 정보만 변경하면 됩니다.

반면에, 프로세스 간에는 공유하는 데이터가 없으므로 process context switching이 발생할 경우 스레드의 context뿐만 아니라 프로스의 context까지 모두 변경해야 합니다.

>Reference <br></br>
>[[OS] Context Switching, PCB (Process Control Block)](https://velog.io/@nnnyeong/OS-Context-Switching-PCB-Process-Control-Block) <br></br>
>[콘텍스트 스위칭(Context Switching)](https://beststar-1.tistory.com/26) <br></br>
>[OS - Context Switch(컨텍스트 스위치)가 무엇인가?](https://jeong-pro.tistory.com/93) <br></br>
>[Context Switching 이란?](https://velog.io/@curiosity806/Context-Switching%EC%9C%BC%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-process%EC%99%80-thread) <br></br>
>[[운영체제] Context Switch(문맥 교환)이란?](https://spurdev.tistory.com/13) <br></br>
>[PCB & Context Switching](https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Operating%20System/PCB%20%26%20Context%20Switcing.md) <br></br>
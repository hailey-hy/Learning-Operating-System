### 1. 컴퓨터 시스템 구조

---

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/415ff2ac-85f9-4bd5-8044-4ec16e9a875c/Untitled.png)

1. input: I/O device를 통해 데이터가 computer로 들어가는 것
2. output: 받은 데이터를 처리한 후 computer에서 I/O device로 이동하여 출력하는 것
3. memory
    - CPU의 작업 공간, 원칙적으로 CPU만 접근할 수 있음
    - CPU는 매 순간 메모리에서 읽어서 작업을 처리
    - 운영체제가 들어있음
4. I/O device
    - 키보드, 마우스: input device
    - 모니터, 프린트: output device
    - 하드디스크: input & output device
    - device controller
        - I/O device에 붙어서 디바이스를 전담하는 작은 CPU역할을 함
        - 디스크의 헤드가 어떻게 움직이고, 어떻게 컨트롤 할지 등을 관할
    - local buffer: device controller의 작업공간 (CPU의 메모리처럼)
        1. device controller가 local buffer에 데이터를 임시로 저장한 후 메모리에 전달됨
        2. device controller가 인터럽트를 발생시켜 CPU에 보고함
        - CPU와 속도차이를 감당함
5. CPU
    - 메모리에서 인스트럭션을 하나씩 읽어서 실행함
        - 인스트럭션은 보통 4바이트
    - 굉장히 빠른 속도를 보전하기 위한 상호작용 장치들이 부가적으로 존재함
    - registers: CPU 내부에 존재하는 메모리보다 더 빠른 작은 저장공간
        - 메모리 주소를 가리키는 program counter registers:
            - 메모리 위치에서 인스트럭션을 읽어서 실행
            - 이후 register는 다음 위치를 가리키며 순차적으로 프로그램이 실행됨 (메모리위치를 점프하는 인스트럭션이 있으면 점프도 가능)
    - interrupt line: 메모리에 있는 인스트럭션만 수행하도록 되어 있는 CPU가 I/O device에서의 요청을 수행해야 할 경우, 요청을 전달
        - 먼저 CPU가 Disk의 divice controller에 특정 데이터를 읽어오라고 요청
        - Disk가 자신의 local buffer에 데이터를 넣음
        - CPU는 그동안 원래 하던 메모리 접근을 하고 있다가 데이터를 받고 요청을 실행
        
        → 데이터를 받기 전에는 요청을 실행할 수 없을 경우
        
    - CPU는 인스트럭션을 실행하고 다음 인스트럭션을 실행하기 전 interrupt line을 체크함
    - ex. I/O device 사용이 필요한 사용자 프로그램 구동 원리
        - 사용자 프로그램이 I/O를 해야 할 경우 스스로 OS에 CPU 제어권을 넘겨 줌
        - 마찬가지로 device controller에 명령을 내린 후, OS는 다른 프로그램에 제어권을 넘겨줌
        - I/O controller가 요청된 작업을 완료하고 데이터가 local buffer에 있게 되면 controller가 CPU에 interrupt를 검
        - 즉시 CPU의 제어권은 사용자 프로그램에서 OS로 넘어감
        - local buffer에 있는 I/O device로 입력된 값을 메모리 공간에 카피
        - 사용하고 있던 사용자 프로그램에 CPU를 다시 줌
        - 이후 차례가 오면 CPU를 얻게 됨
6. Timer
    - 정해진 시간이 흐른 뒤 운영체제에 제어권이 넘어가도록 Interrupt를 발생시킴
    - 특정 프로그램이 CPU를 독점해 발생하는 문제점을 막기 위함
    - ex) for, while문 등의 무한루프 처리 방식
        - OS가 사용자 프로그램에게 CPU를 넘겨줄 때 timer에 특정 시간을 세팅(load timer, 특권명령)한 후 넘겨줌
        - 세팅된 시간이 넘을 경우 timer가 interrupt line에 알려줌
        - 즉, CPU는 인스트럭션을 실행하고 interrupt line을 항상 체크하는 것이 루틴
        - 만약 timer로부터 interrupt line에 인스트럭션이 들어왔을 경우 CPU 제어권이 사용자 프로그램으로부터 OS로 넘어감
        - OS는 timer 특정값을 설정한 후 다음 프로그램에 CPU 제어권을 넘겨줌
    - CPU의 time sharing을 구현하는 역할
    - 
    - 클록
        - 계수기
7. mode bit
    - CPU를 운영체제가 가지고 있는지, 사용자 프로그램이 가지고 있는지 표시
    - 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치 필요에 의해 만들어짐
    - CPU는 보안과 관련된 명령을 수행하기 전에는 항상 mode bit를 조사해야 함
    1. 0 모니터 모드: OS 코드를 수행 중
        - 커널 모드, 시스템 모드라고도 불림
        - 모든 일을 처리할 수 있게 되어 있음
        - 보안을 해칠 수 있는 중요한 명령어는 모니터 모드에서 수행 가능한 특권명령으로 규정
            
            → 특권명령은 운영체제에 의해서만 수행할 수 있다
            
            - 특권명령의 종류: 입출력 명령
    2. 1 사용자 모드: 사용자 프로그램을 수행 중
        - 제한된 인스트럭션만 처리할 수 있음
        - ex) I/O 접근 불가
        - Interrupt나 Exception 발생 시 자동으로 모니터 모드로 바뀜
8. Device Controller
    - I/O device controller
        - 해당 I/O 장치유형을 관리하는 일종의 작은 CPU
        - 제어 정보를 위해 control register, status register를 가짐
        - local buffer를 가짐 (일종의 data register)
            - 제어를 위한 CPU register, 데이터 저장을 위한 register
    - I/O는 실제 device와 local buffer 사이에서 일어남
    - Device controller는 I/O가 끝났을 경우 interrupt로 CPU에 그 사실을 알림
    - device driver (장치구동기): OS코드 중 각 장치별 처리 루틴 → software
        - Disk firmware: device driver의 instruction 제공
    - device controller (장치제어기): 각 장치를 통제하는 작은 CPU → hardware
9. DMA controller (Direct Memory Access)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e24c962d-07a0-417a-a43b-f7772ad00c00/Untitled.png)
    
    - 직접 메모리에 접근할 수 있는 컨트롤러
    - 원래 CPU만 메모리에 접근할 수 있었으나, CPU가 interrupt를 너무많이 당해 속도가 느려지는 현상을 방지하기 위해 제작
    - 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
    - I/O 장치의 작업 Interrupt를 대신 받아 데이터를 메모리로 복사한 후 CPU에 Interrupt를 한번만 걸 수 있도록 함
    - 바이트 단위가 아닌 블록 단위로 데이터를 읽어 메모리에 전달한 후 CPU에 인터럽트 걸어 작업의 완료를 알림
10. Memory controller
    - CPU와 DMA controller의 메모리 접근을 중재하기 위함

### 2. 컴퓨터 시스템의 동작 원리

---

1. 커널
    - 운영체제의 가장 핵심적인 부분으로,  각종 자원을 관리하기 위해 항상 메모리에 올라가있는 부분
    - 운영체제의 모든 코드가 메모리에 상주해있으면 메모리 낭비가 발생하기 때문에 커널이 따로 존재

### 3. 입출력(I/O)의 수행

---

1. 입출력의 수행
- 모든 입출력 명령은 특권 명령
- 사용자 프로그램의 I/O 수행 방식
    - **시스템콜(system call)**
    - 요청시에는 software interrupt, 종료시에는 hardware interrupt가 발생
1. 동기식과 비동기식 입출력
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/21effacb-0d3e-46d3-9830-1caf4a4480b6/Untitled.png)
    
    1. 동기식 입출력 (synchronous I/O)
        - I/O 요청 후 입출력 작업이 완료된 후에야 그 프로그램이 후속 작업을 수행할 수 있는 방식
        - 구현 방법 1
            - I/O가 끝날 때 까지 프로그램의 다음 명령을 수행하지 않고 기다림
            - 입출력 완료 후 인터럽트로 사실이 전달된 후에 CPU의 제어권을 획득
            - 이후 다음 명령 수행 가능
            
            → CPU는 입출력 연산이 끝날 때까지 인터럽트를 기다리며 자원을 낭비
            
            → 매시점 하나의 I/O만 일어날 수 있음
            
        - 구현 방법 2
            - I/O가 완료될 때까지 해당 프로그램을 봉쇄상태로 설정
                
                → 입출력이 완료될 때까지 CPU를 할당받지 못해 명령을 수행하지 못하는 상태
                
            - I/O 처리를 기다리는 줄에 그 프로그램을 줄 세움
            - 다른 프로그램에게 CPU를 줌
            - 입출력이 완료되면 인터럽트를 통해 연산 완료를 통보
            - 운영체제 커널이 인터럽트 처리 루틴으로 가서 해당 프로그램을 봉쇄상태로부터 해제
            
            → 입출력 요청의 동기화를 막기 위해 장치별로 큐를 두어 요청한 순서대로 처리할 수 있도록 함
            
    2. 비동기식 입출력 (asynchronous I/O)
        - I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감
    - 두 가지 모두 I/O의 완료는 인터럽트로 알려줌
2. 서로 다른 입출력 명령어
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/40391aba-c8fc-4030-8ae1-da21e43d1be5/Untitled.png)
    
    - I/O를 수행하는 special instruction에 의해
    - Memory Mapped I/O에 의해
        - I/O 장치에 메모리의 연장 주소를 붙인 instruction

### 4. 인터럽트 (Interrupt)

---

1. 인터럽트
    - 현대의 운영체제는 인터럽트가 발생할 때에만 실행됨
        - 시스템 부팅 후 정상 상태에 머무르게 되면 CPU는 항상 사용자 프로그램에 의해 사용되고, 인터럽트가 발생했을 때만 잠깐 CPU의 제어권을 획득하기 때문
    - 인터럽트 당한 시점의 레지스터와 program counter를 save한 후 CPU의 제어를 인터럽트 처리 루틴에 넘긴다
2. Interrupt의 종류
    
    → 모든 인터럽트는 CPU 옆의 interrupt line에 신호를 보내 인터럽트를 발생
    
    1. Interrupt(하드웨어 인터럽트): 하드웨어가 발생시킨 인터럽트
        - ex) 타이머, I/O device controller 등
    2. trap(소프트웨어 인터럽트)
        - Exception: 프로그램이 오류를 범한 경우
            - ex) 0으로 나누기, 자신의 메모리 영역 바깥에 접근하려는 시도 등의 권한 없는 작업
        - System call: 프로그램이 커널 함수를 호출하는 경우
            - 사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것
            1. 사용자 프로그램은 운영체제에게 I/O 요청
            2. 직접 Interrupt 신청(software interrupt)
            3. CPU 제어권이 사용자 프로그램에서 운영체제로 넘어감
            4. trap을 사용하여 인터럽트 벡터의 특정 위치로 이동
            5. 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동
            6. 올바른 I/O 요청인지 확인 후 I/O 수행
            7. I/O 완료 시 제어권을 시스템콜 다음 명령으로 옮김
                - 이후에는 I/O controller가 interrupt를 걸어 작업이 끝났다고 알려주게 될 것
        
        → 두가지 모두 CPU의 제어권이 운영체제에 이양되며, 이 과정에서 프로그램 코드가 직접 인터럽트 라인을 세팅하는 명령을 실행해 인터럽트를 발생시킨 후 제어권이 넘어감
        
3. 인터럽트 관련 용어
    - 인터럽트 벡터
        - 각 인터럽트의 처리 루틴이 위치한 장소를 가리키는 자료구조
        - 운영체제가 인터럽트별로 할일을 쉽게 찾아가기 위해 갖고 있음
    - 인터럽트 처리 루틴
        - interrupt Service Routine, 인터럽트 핸들러
        - 해당 인터럽트를 처리하는 커널 함수
        - 다양한 인터럽트에 대해 각각 처리해야 할 업무들을 정의하고 있음
        - 운영체제는 인터럽트 처리 루틴을 보고 인터럽트를 어떻게 처리할지 결정
4. 인터럽트 핸들링
    - 의미: 인터럽트가 발생한 경우 처리해야 할 일의 절차
    - 동작 순서
        1. 프로그램 A 실행 중에 인터럽트가 발생
        2. A의 현재 상태(CPU에서 실행 중인 명령의 메모리 주소 등의 부가적 정보)를 PCB에 저장
            
            → CPU는 내부의 임시 기억장치 register에 데이터를 읽고 쓰며 작업을 하는데, 새로운 명령이 발생하면 기존의 register 값들이 모두 지워지게 되기 때문
            
        3. 

### 5. 저장장치 계층 구조

---

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c0b4152e-1285-497c-b950-ab99204b9d35/Untitled.png)

- Primary
    - register → Cache → DRam
    - 속도가 빠름
    - 단위면적대비 용량이 적음 (비쌈)
    - 휘발성
    - CPU에서 직접 접근할 수 있음 (바이트단위로 개체를 매길 수 있음)
    - Caching: 상대적으로 용량이 적고 빠른 저장장치를 이용해 느린 저장장치의 성능을 향상시키는 총체적 기법을 일컬음
        - 느린 저장장치에 있는 내용 중 당장 사용되거나 빈번히 사용되는 정보를 빠른 저장장치에 선별적으로 저장
        - 속도 차이를 완충함
        - 프로그램의 코드는 빈번히 사용되는 부분이 있기 때문
- Secondary
    - HDD, SDD, USB 메모리, ODD
    - 속도가 느림
    - 상대적으로 용량이 느림
    - 비휘발성
    - 섹터단위로 접근
    - 스왑 영역으로 사용
        - 메모리의 연장 공간으로서 메모리 공간이 부족한 경우 사용

### 5. 프로그램의 실행

---

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4b1b2958-d64e-4fa0-8671-08922afd5457/Untitled.png)

1. 프로그램의 실행
    - 프로그램은 파일 형식으로 하드디스크에 저장되어 있음
        - 전원이 꺼져도 file로 남아있음
    - 실행시키면 프로그램이 메모리로 올라가서 프로세스가 된다
        - Virtual memory 단계가 됨
            - 프로그램의 Address space가 생성 (code-data-stac의 형식)
            - 실행시키면 생겨나고 끝나면 사라짐
            - 통째로 메모리에 올리면 메모리가 낭비되기 때문에 해당하는 코드만 올림
            - 즉, 각 프로그램마다 독자적으로 갖고 있는 메모리 공간
        - 당장 필요한 부분은 Pysical memory에 올라감
            - 주소 변환 계층
                - Virtual memory와 Physical memory 주소가 다르게 되므로
        - 그렇지 않은 부분은 Swap area에 올라감
            - Swap area → 메인 메모리의 연장 공간으로 사용
        - 전원이 꺼지면 모든 정보가 사라짐
2. 커널 주소 공간의 내용
    - 운영체제 커널도 하나의 프로그램이므로 code, data, stack의 주소 공간으로 구성
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a093e10e-db8e-4970-819e-50987ec7fa71/Untitled.png)
    
- PCB: 운영체제가 현재 시스템 내에서 실행되는 프로그램들을 관리하기 위해 만드는 자료구조
    - 각각의 프로그램마다 하나씩 존재
    - 해당 프로그램의 어느 부분이 실행 중이었는지 저장
        - 실행중이던 코드의 메모리 주소, 레지스터값, 하드웨어 상태 등
- 함수 호출을 위한 stack
    - 사용자 프로그램들은 운영체제 커널의 코드를 불러다 쓰므로, 사용자 프로그램마다 커널스택을 따로 씀
1. 사용자 프로그램이 사용하는 함수
    - 모든 프로그램은 함수로 짜여져 있음
    - 함수 (Function)
        - 사용자 정의 함수: 자신의 프로그램에서 정의한 함수
        - 라이브러리 함수: 자신의 프로그램에서 정의하지 않고 갖다 쓴 함수
        유용한 기능으로 구성되어 있어 자신의 프로그램의 실행 파일에 포함되어 있음
        
        → 코드, 실행파일은 프로그램에 포함되어 있어 프로그램의 address space 사용
        
        - 커널 함수: 운영체제 프로그램의 함수, 시스템 콜과 동의어
        
        → 프로그램에 포함되어 있지 않아 CPU 제어권을 넘기며 Kernal Address space 사용해 호출
        
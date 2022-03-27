### 1. 프로그램의 구조

---

1. 프로그램의 구조
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c315952b-b899-47a2-992c-0dc3086e4d06/Untitled.png)
    
    1. 프로그램은 파일 시스템에 형태로 저장되어있음
    2. 실행시키면 프로그램별 Virtual memory 중 필요한 부분은 메모리에, 필요하지 않은 부분은 Swap area에 **주소변환**을 거쳐 올라감
        - Virtual memory의 논리적인 주소가 Physical memory의 물리적인 주소로 변환
2. Virtual memory
    - code
        - 프로그램에 내장된 기계어 코드들이 위치
    - data
        - 프로그램 실행 중 선언하는 전역변수, 프로그램 시작부터 종료까지 남아있는 데이터 등이 위치
    - stack
        - 프로그램의 함수 호출과 return에 대한 정보를 저장했다가 복귀하는 데 쓰임
3. Kernel Address space
    1. code
        - 하드웨어의 자원을 관리, 사용자 인터페이스의 편의를 관리, 인터럽트 처리, 시스템콜 등의 코드가 들어 있음
    2. data
        - 하드웨어, 실행중인 프로세스를 관리하기 위한 자료구조(PBC)
    3. stack
        - 프로세스 별로 각각 커널 복귀 주소를 저장함
            - 커널로 들어오기 전 어디에 있었느냐를 구분하기 위해 프로세스 별로 구성

### 2. 사용자 프로그램이 사용하는 함수

---

1. 함수
    1. 사용자 정의 함수: 자신의 프로그램에서 정의한 함수
    2. 라이브러리 함수: 자신이 프로그램의 실행 파일에서 갖다 쓴 함수
    3. 커널 함수: 운영체제 프로그램의 함수로, 커널 함수의 호출을 시스템 콜이라고 부름

### 3. 프로그램의 실행

---

1. 프로그램의 실행
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a2412b45-8176-42de-bf46-06d7372c3440/Untitled.png)
    
- 프로세스가 타 프로세스 B에 CPU를 뺏기면 프로그램이 종료됐다고 간주

### 4. 프로세스

---

1. 프로세스의 개념
    - 프로세스: 실행중인 프로그램
2. 프로세스의 문맥 (context)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/330b606c-b1ba-4c19-afd2-05c316142dcb/Untitled.png)
    
    1. CPU의 수행 상태를 나타내는 하드웨어 문맥
        - program counter 값
            - 주소공간의 어디에서 코드를 실행했는가
        - 각종 register 값
            - 어떤 기계어를 쓰고 있었는가
    2. 프로세스의 주소 공간
        - 자신의 메모리 공간에 어떤 것을 갖고 있는가
            - 프로세스의 주소공간 내 code, data, stack
    3. 프로세스 관련 커널의 자료 구조
        - PCB의 code, data, stack
        - kernel stack
3. 프로세스의 상태
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4f6f91dd-ed3b-494f-a9ec-eb8ec9212902/Untitled.png)
    
    1. running 상태: 프로세스가 CPU에서 기계어를 실행하는 상태
    2. ready 상태: 프로세스가 CPU를 즉시 사용하기 위해 대기하고 있는 상태
    3. blocked 상태: CPU 제어권을 가져도 intstruction을 수행할 수 없거나, 프로세스 자신이 요청한 event가 즉시 만족되지 않아 이를 기다리는 상태
        - ex) 디스크에서 file을 읽어와야 하는 경우
    4. New: 프로세스가 생성중인 상태
    5. Terminated: 수행(execution)이 끝난 상태
    6. Suspended (Stopped)상태: 외부적인 이유로 프로세스의 수행이 정지된 상태
        - 프로세스는 통째로 디스크에 swap out 된다
        - ex) 사용자가 프로그램을 일시 정지 시킨 경우 (break key)
            
            시스템이 여러 이유(메모리에 너무 많은 프로세스가 올라와 있을 때)로 프로세스를 잠시 중단시킴
            
        - I/O를 하던 도중 Suspended되면 I/O는 계속 할 수 있음
            
            → Suspended Blocked에서 I/O 작업이 종료되면 Suspended ready로 변환
            
    - bloced와 Suspended
        - blocked: 프로세스가 실행 중 → 자신이 요청한 event가 만족되면 ready
        - Suspended: 프로세스가 정지됨 → 외부에서 resume해 주어야 active
- 프로세스 상태 변환
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1dff18fb-48d0-4de7-854d-38c3ea622fa9/Untitled.png)
    
    - 운영체제는 ready 큐에 있는 프로세스에게 CPU 제어권을 넘겨줌
    - I/O device에서 입력을 끝났음을 알리는 인터럽트가 들어올 경우, 해당 프로세스를 blocked 상태에서 ready 상태로 바꿔 CPU를 얻을 수 있게 함
- 공유 데이터
    - 다른 프로세스가 공유데이터를 사용 중일 때, 공유데이터를 쓰고자 하는 프로세스는 blocked 상태가 됨

→ 여러 큐로 표현되는 프로세스의 상태를 운영체제가 PCB를 통해 자신의 데이터 영역에 프로세스별로 관리 중

- 프로세스 상태도
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4da2341c-9fbd-47b1-92f7-920918a052ab/Untitled.png)
    
    - Running - user mode / monitor mode
        1. 프로세스가 자기 코드에서 실행되고 있다가 운영체제의 서비스를 받기 위해 시스템 콜을 요청할 때
            - 시스템콜 이후에도 CPU는 사용자 프로그램을 위해 수행중이라고 보는 것이 타당함
            - 즉, 프로세스가 커널 모드에서 러닝한다고 표현함
        2. 사용자 프로그램 A가 CPU를 갖고 running 중에 프로그램 B의 I/O 요청 종료 알림 인터럽트를 받을 때
            - 외부 이유로 인터럽트되어 CPU가 넘어갔지만 여전히 A가 running한다고 간주
            - 인터럽트 후 CPU 제어권이 넘어간 운영체제는 running한다고 표현하지 않기 때문
- Disk

### 5. PCB

---

1. PCB
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2adf393c-72e3-4298-a83b-f1abf9ed0ef8/Untitled.png)
    
    - 운영체제가 각 프로세스를 관리하기 위해 프로세스당 유지하는 자료구조
    - 운영체제 커널의 주소공간에 저장되어 있음
    - 구성 요소
        1. 운영체제가 프로세스 관리상 사용하는 정보
            - 프로세스 상태 (ready 등)
            - 프로세스 생성 시 부여받는 프로세스 ID
            - CPU 스케줄링의 정보, 우선순위
        2. CPU 수행 관련 하드웨어 값 → 문맥 교환에 필요
            - program counter
            - registers
        3. 메모리 관련
            - code, data, stack의 위치 정보
        4. 파일 관련
            - open file descriptors
    - 문맥 교환
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a440d1ae-4fb5-42b1-bc06-f75f836ddd0d/Untitled.png)
        
        - CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
        - CPU가 다른 프로세스에게 넘어갈 때
            1. CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
            2. CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴
        - 시스템콜, 인터럽트 발생 시 반드시 문맥 교환이 일어나지는 않음
            - 문맥 교환이 아닌 경우
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a9b0dfd6-aae1-4a54-8e83-bf533e87282c/Untitled.png)
                
                → 문맥 교환은 사용자 프로세스 A로부터 사용자 프로세스 B로 넘어가는 것을 말함
                
                → 원래 프로세스로 돌아가므로 지극히 일부만 로드됨
                
            - 문맥 교환이 맞는 경우
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/421d4f8e-81bc-4f3a-884a-be2cb513ef7d/Untitled.png)
                
                → 결과적으로 사용자 프로그램 A로부터 다른 사용자 프로그램으로 넘어간다면 커널 함수를 거쳐도 문맥 교환
                
                → 다른 프로세스로 이동하므로 원래 프로그램이 썼던 캐시 메모리를 전부 지우고 이동한 프로세스의 메모리를 모두 세이브
                
2. 프로세스를 스케줄링하기 위한 큐
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a3514af0-16ae-4198-a7ac-a09eb25bd55d/Untitled.png)
    
    - Job queue
        - 현재 시스템 내에 있는 모든 프로세스의 집합
    - Ready queue
        - 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
    - Device queue
        - I/O device의 처리를 기다리는 프로세스의 일종
    
    → 프로세스들은 각 큐들을 오가며 수행된다
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ebdb46c5-b937-4312-a7c7-dee495f968a4/Untitled.png)
    
    - fork
        - 프로그램의 자식(복제)을 생성
    
    ### 6. 스케줄러
    
    ---
    
    - 운영체제의 함수, 커널 코드 중 스케줄링 기능을 하는 부분
    1. long-term scheduler 장기 스케줄러
        - new 상태에 있는 프로세스를 admit해서 ready 상태로 만들어 줌
        - 프로세스에 memory를 주는 문제
        - degree of multiprogramming을 제어
            - 메모리에 얼마나 많은 프로그램을 올릴 것인가?
        - 그러나 범용 운영체제(time sharing system)에는 보통 장기 스케줄러가 없어 무조건 ready 상태
    2. short-term scheduler 단기 스케줄러
        - CPU 스케줄링을 담당
            - CPU 스케줄링: CPU를 누구에게 얼마나 할당할 것인가
        - 대단히 자주 호출됨, m/s 단위 → CPU의 속도가 빠르기 때문
    3. medium-term scheduler, Swapper 중기 스케줄러
        - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄
        - 프로세스에게서 memory를 뺏는 문제
        - degree of multiprogramming을 제어

### 7. 스레드

---

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/09a90457-f6cc-42ac-a30e-a311166e106b/Untitled.png)

1. Thread 스레드의 정의
    - 프로세스 중에서 CPU의 수행 단위를 나타냄
    - 동일 프로그램 중 다른 code 위치를 보고 있을 때 좀 더 효율적으로 주소 공간을 사용하기 위함
    - CPU 수행과 관련된 부분만 별도로, 그 외는 단일 프로세스로 share하는 구조가 효율적
    - lightweight process
        - 전통적 process는 하나의 thread를 가진 구조라고 볼 수 있음
    - ex) 웹 브라우저가 여러 개 실행되면 code는 share하고, 프로세스는 하나가 뜨는데 현재 무슨 일을 하는 가에 따라서 register 값, stack에는 다른 위치, 다른 일을 하게 해놓음
    - ex) 크롬은 스레드를 쓰지 않음
2. Thread의 구성
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6c9f4a7a-74fb-44e6-a877-e3be5add5d23/Untitled.png)
    
    - program counter
    - register set
    - stack space
    - 동료 thread와 공유하는 부분(task)
        - code section
        - data section
        - OS resources
3. Thread의 효과
    - 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 스레드가 blocked 상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있다. (비동기식 입출력의 가능 요인)
    - 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.
    - 스레드를 사용하면 병렬성을 높일 수 있음
4. Thread의 장점
    - Responsiveness
        - 빠른 응답성
    - Resource sharing
        - 자원 공유에 의한 효율성
    - Economy
        - 프로세스 하나를 만드는 것에 비해 스레드 하나를 만드는 것이 더 효율적
            - ex) Solaris의 경우 30배 효율적
    - Utilization of MP Architectures
        - CPU가 여러 개 있는 환경에서 병렬성을 추구할 수 있음
5. Thread의 구현
    - Kernel Thread
        - 운영체제가 스레드를 이미 알고 있는 경우
    - User Thread
        - 운영체제가 스레드의 존재를 모름

### 8. 프로세스의 생성과 종료

---

1. 프로세스의 생성
    - 부모 프로세스가 자식 프로세스 생성 (복제)
        - **fork() 시스템 콜**: 자식 프로세스를 생성해 달라고 커널에 요청하는 시스템 콜
            - 부모를 복제한 자식이 생성 (부모가 fork()한 시점부터 실행)
            - 부모는 자식 프로세스의 pid(process id) 값을 받게 되고, 자식은 0을 받음
    - 프로세스의 트리(계층구조) 형성
    - 자원: 프로세스 실행은 자원을 필요로 함
        - 운영체제로부터 받은 후 부모와 공유하는 경우도 있음
            1. 부모와 자식이 모든 자원을 공유하는 모델
            2. 일부를 공유하는 모델
            3. 전혀 공유하지 않는 모델
    - 수행
        1. 부모와 자식이 공존하며 수행되는 모델
        2. 자식이 종료(terminate)될 때까지 기다리는(blocked) 모델
    - 주소 공간
        - 자식은 부모의 공간을 그대로 복사함 (binary and OS data)
        - **exec() 시스템 콜**: 자식은 그 공간에 새로운 프로그램을 올림
            - 원래 자식 프로그램은 부모와 같은 프로그램을 실행함
            - exec(프로그램의 이름, 전달할 argument, 제로포인트)
            - 다음 줄의 코드는 실행되지 않음
2. 프로세스의 종료
    1. exit: 자발적 종료
        - 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌
        - 자식이 부모에게 output data를 보냄 (via wait)
        - 프로세스의 각종 자원들이 운영체제에게 반납됨
    2. abort: 비자발적 종료
        - 부모 프로세스가 자식의 수행을 종료시킴
            - 자식이 할당 자원의 한계치를 넘어섬
            - 자식에게 할당된 태스크가 더 이상 필요하지 않음
        - 키보드로 kill, break등을 치는 경우 (운영체제가 종료시킬 수도 있음)
        - 부모가 종료하는 경우
            
            → 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않음
            
            → 단계적인 종료
            
3. 그 외
    1. wait
        - 프로세스가 wait() 시스템 콜을 호출하면
            
            → 커널은 child가 종료될 때까지 프로세스 A를 sleep 시킨다 (block 상태)
            
            → Child process가 종료되면 커널은 프로세스 A를 깨운다 (ready 상태)
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/20abeac9-24e0-4420-a45d-3561352ac5d4/Untitled.png)
            
        - wait를 하지 않으면 부모와 자식이 서로 CPU를 얻기 위한 경쟁관계에 있게 됨

### 9. 프로세스의 협력

1. 독립적 프로세스
    - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
2. 협력 프로세스
    - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
3. 프로세스간 협력 메커니즘 IPC(Interprocess Communication)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ec61a0dd-bcdb-48a2-bf74-f93438e3dc28/Untitled.png)
    
    - 메시지를 전달하는 방법
        - message passing
        - 커널을 통해 메시지 전달
        1. Direct Communication
            - 통신하려는 프로세스의 이름을 명시적으로 표시
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/113a0c39-1054-471f-8ae9-907842d8d927/Untitled.png)
                
        2. Indirect Communication
            - mailbox를 통해 메시지를 간접 전달
                
                ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9dea3295-e838-438b-ac80-739a7c3b76b6/Untitled.png)
                
    - 주소 공간을 공유하는 방법
        - shared memory
        - 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
    - cf. 스레드
        - 스레드는 사실상 하나의 프로세스이므로 메모리 공간을 전체 공유하고 있음
        - 협력이 가능
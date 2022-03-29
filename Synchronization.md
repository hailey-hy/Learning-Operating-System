### 1. Process Synchronization

- 공유 데이터의 동시 접근은 데이터의 불일치 문제를 발생시킬 수 있음
- 일관성 유지를 위해서는 협력 프로세스간의 실행 순서를 정해주는 메커니즘이 필요함

### 2. 데이터의 접근과 경쟁상태 발생

- 데이터 중복 접근의 문제

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3eaca6cf-928b-4cfc-b79d-cf5131dfe7f3/Untitled.png)

- Memory Address Space를 공유하는 CPU process가 여럿 있는 경우 Race Condition(경쟁상태) 발생의 가능성이 있음
- **경쟁 상태**: 공유 자원에 대해 여러 개의 프로세스가 동시에 접근을 시도해 접근의 타이밍이나 순서 등이 결과값에 영향을 줄 수 있는 상태.
    - OS에서 Race Condition은 언제 발생하는가?
        1. 커널 수행 중 인터럽트 발생 시
        2. 프로세스가 시스템 콜을 해 커널 모드로 수행 중인데 문맥 교환이 일어나는 경우
        3. 멀티프로세서에서 Shared memomry내의 kernel data
    - + CPU가 1개 있을 경우
        - 변수와 관련한 데이터는 프로세스 내부에 있고, 프로세스는 자기 자신 내부 데이터만 접근할 수 있으므로 경쟁상태가 발생하지 않음
        - 서로 다른 프로세스가 운영체제에 시스템 콜을 요청해 동일한 데이터에 접근했을 경우, 프로세스는 데이터를 공유하지 않지만 운영체제 안에 있는 데이터에서 경쟁상태가 발생함

### 3. OS에서의 Race condition

1. 커널 수행 중 인터럽트 발생
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dd5fad88-0078-4d12-921b-8e7d4fa6ec15/Untitled.png)
    
    - 커널 모드 수행 중 인터럽트가 발생하여 인터럽트 처리루틴을 수행하였으나, 인터럽트 처리 루틴과 수행중인 작업이 동일한 데이터를 접근했을 경우
    - 모두 커널 코드이므로 kernel address space를 공유하기 때문
    - 해결책: 커널 공유 변수에 접근하기 전에 disable/enable interrupt를 사용해 인터럽트를 차단함으로써 해결할 수 있음
2. 시스템 콜 중 문맥 교환
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/adbca122-4456-4dbd-9816-8dafe67b5a7d/Untitled.png)
    
    - 시스템 콜 수행 도중 접근하게 되는 커널의 데이터는 일종의 공유 데이터
    - 커널모드에 있다가 다른 프로세스로 문맥 교환이 일어나면 커널 데이터에서 race condition이 발생
    - 해결책: 커널 모드에서 수행중일 때는 CPU를 preempt하지 않음
3. 멀티프로세서
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8d952f17-f052-4556-b577-d8314f3be750/Untitled.png)
    
    - 각 CPU들이 시스템 콜로 운영체제의 코드를 실행할 경우 race condition 발생
    - 해결책
        1. 한번에 하나의 CPU만이 커널에 들어갈 수 있게 함
            
            → 그러나 오버헤드가 뒤따르게 됨
            
        2. 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock/unlock을 하는 방법

### 4. Critical Section

- 각 프로세스의 code segment에 있는 **공유 데이터를 접근하는 코드**
- 프로세스가 공유데이터에 접근하는 순서에 따라 실행 결과가 달라지는 영역
- n개의 프로세스가 공유 데이터를 동시에 사용하길 원하고, 하나의 프로세스가 critical section에 있으면 다른 프로세스들은 critical section에 들어갈 수 없어야 함 → lock
- 해결책의 충족 조건
    - Mutual Exclusion 상호 배제
        - 동시에 들어갈 수 없다
    - Progress 진행의 융통성
        - 아무도 critical section에 있지 않다면 원하는 프로세스가 들어갈 수 있어야 한다
    - Bounded Waiting 유한한 대기 시간
        - 기아 현상을 막아야 한다
- 만약 아래 코드로 된다면?

while(lock==2){};
임계구역
lock=2;

while(lock==1){};
임계구역
lock=1;

### 5. 해결책: 알고리즘

- ciritical section의 전후에 코드를 추가해 lock을 걸어둘 수 있도록 하는 알고리즘
1. turn 알고리즘
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8c3b6547-658d-460a-8d46-a0c45f0d4408/Untitled.png)
    
    - synchronization variable (turn) 추가
    - turn이 내 차례라면 critical section을 수행하고, 상대의 turn으로 바꿔줌
    - 문제점
        - 과잉 양보 → 차례를 무조건 번갈아 들어가도록 되어 있음
        - 상대와 빈도 수 차이가 날 경우 비효율성
2. flag 알고리즘
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/55ea8c6b-8467-420a-a3c1-d95bdd5697d7/Untitled.png)
    
    - critical section에 접근하고자 하는 프로세스는 flag를 들어 표시
    - 접근 전 상대방의 flag가 표시되어 있다면 ciritical section에 들어가지 않음
    - 접근 후 flag를 내려 상대방의 접근을 허용
    - 문제점
        - flag만 들고 아직 critical section에 들어가지 않은 상태라면 끊임 없이 양보하는 상황이 발생할 수 있음
3. Peterson 알고리즘
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/edc3aba3-f4f5-423a-b78b-a75e5d089ef3/Untitled.png)
    
    - flag를 든 경우에 한해 turn을 정해 차례를 정함
    - 문제점
        - Busy waiting (=spin lock)
            
            → while문을 빠져나가지 못해 CPU를 낭비
            
    - 해결책
        - Synchronization Hardware
        - Hardware적으로 test & modify를 atomic하게 수행할 수 있도록 지원하는 경우 앞의 문제는 간단히 해결
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/19e8cdb2-88be-4db4-b079-8812e6b00b1c/Untitled.png)
            

### 5. Semaphores

- Semaphores: 추상 자료형의 일종
    - Binary semaphore
        - 0또는 1의 값만 가지는 semaphore
        - 주로 mutual exclusion(Lock, unlock)에 사용
    - Counting semaphore
        - 도메인이 0 이상인 임의의 정수값
        - 주로 resource counting에 사용

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4d8d0645-ef2e-41a6-a058-66db52d9c9b5/Untitled.png)

1. Busy Waiting
    - 변수의 값은 정수, 동시 접근 문제에서는 변수 값을 1로 줌
    - 모든 연산은 atomic하게 이루어짐
    - P 연산
        - 자원을 획득하는 과정
        - 변수값이 0이라면 Lock을 건다
    - V 연산
        - 자원을 반납하는 과정
        - Lock을 풀어주는 과정
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d62e683f-ae12-432a-89ac-775c730de15c/Untitled.png)
    
    - mutex: mutual exclusion
    - busy waiting 문제 발생 가능
2. block and wakeup
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ede0e661-b0b6-4a1d-80af-f4d5d975586f/Untitled.png)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fbe7ec6a-8232-4e6e-874a-4df6c141b8f6/Untitled.png)
    
    - 대기 큐에 있는 프로세스를 잠재우고, 사용이 끝난 프로세스가 대기 큐 최상단의 프로세스를 깨움
    - spin lock 방지 가능
    - 음수라면 → 여분이 없어 누군가가 대기 큐에서 blocked되어 있음
    - 양수라면 → 아무도 대기큐에 있지 않아 여분이 있음
3. 두 가지 방식 비교
    - while문만 계속 돌면서 CPU를 낭비하는 busy waiting은 비효율적
    - 프로세스의 상태를 지속적으로 바꾸는 blocked and wakeup도 오버헤드 우려가 있음
    1. Critical section의 길이가 긴 경우 → blocked and wakeup
    2. Critical section의 길이가 매우 짧은 경우 → Busy Waiting의 오버헤드가 더 작을 수 있음
    3. 일반적으로는 blocked and wakeup
    

### 6. Deadlock

- 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
- 작업이 수행된 후에야 작업을 내놓기 때문에 영원히 상대방의 작업을 기다리게 됨
- starvation과 동시에 발생
    - 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상
- 해결책
    - 순서를 정하는 방법
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/db4512ec-579e-49c9-9727-e911fc791c2e/Untitled.png)
    

### 7. 동기화와 관련한 문제

1. producer-consumer (bounded buffer problem)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3f4fc12a-c651-4609-a4f5-c54bf263c201/Untitled.png)
    
    - 생산자 프로세스
        - 공유데이터를 넣어 놓는 역할
    - 소비자 프로세스
        - 공유데이터를 꺼내가는 역할
    - 자원은 생산자, 소비자의 입장에서 서로 반대
        - 생산자 프로세스는 비어있는 버퍼
        - 소비자 프로세스는 들어있는 버퍼
    - 공유데이터 동시 접근해 발생하는 문제를 어떻게 해결할 것인가?
    - 해결책
        - 세마포어
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f49eaed1-9e0b-467f-9d0c-363c6091d22e/Untitled.png)
            
            - 접근하기 전 다른 프로세스의 접근을 막는 lock을 걺
            - 필요한 자원(빈 버퍼, 찬 버퍼)이 생기기 전까지 기다림
2. Readers and Writers Problem
    - 한 process가 DB에 write 중일 때 다른 process가 접근하면 안됨
    - ↔ read는 동시 접근 가능
    1. Writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기 중인 Reader들의 DB접근 허용
    2. Writer는 대기 중인 Reader가 하나도 없을 때 DB 접근이 허용
    3. 일단 Writer가 DB에 접근 중이면 lock을 걸어 Reader들의 접근이 금지
    4. Writer가 DB에서 빠져나가야면 unlock되어 Reader의 접근 허용
- shared date
    - DB 자체
    - readcount: 현재 DB에 접근 중인 Reader의 수
        
        → reader들이 동시에 읽을 수 있게 하기 위해 설정
        
- Synchronization variables
    - mutex
    - db
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ef0d40a5-cc3b-47d8-99bc-2ad28a289727/Untitled.png)
    
- 문제점
    - Reader가 다 빠져나가야 Writer가 접근할 수 있는데, Reader가 계속 등장하면 Starvation이 발생할 수 있음
    - 해결책 → 신호등
        
        일정 시간까지 도착한 Reader만 허용, 그 후에는 Writer에게 기회를 줌
        
1. Dining Philosophers Problem
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0962fceb-3bb8-469f-abdf-e53b0f6b2726/Untitled.png)
    
    - 젓가락(공유데이터)를 두개 얻어야 수행 가능한 작업을 어떻게 굶어죽기 전(데드라인)까지 수행할 것인가
    - Deadlock의 가능성 → 모든 철학자가 왼쪽 젓가락을 들었다!
    - 해결책
        1. 자리 -1 의 철학자만 자리 배치 허용
        2. 젓가락을 모두 집을 수 있을 때만 젓가락 집기 허용
        3. 비대칭 → 짝수 철학자는 홀수 젓가락
            
            (동시에 다른 젓가락 집기를 방지)
            

### 7. 모니터

1. semaphore의 문제점
    - 코딩 어려움
    - 정확성 입증 어려움
    - 자발적 협력이 필요
    - 한번의 실수가 모든 시스템에 치명적 영향
2. 모니터
    - 고급 프로그래밍 언어 차원에서 제공하는 동기화 기능
    - 공유 데이터를 접근할 때는 프로그램에서 제공하는 프로시저를 따르도록 함
    - 세마포어와 달리 공유 데이터의 접근과 관련한 문제를 책임
        - 세마포어의 락은 프로그래머가 거는 것
            - 원자적 연산만 담당
        - 모니터는 모니터 안에서 처리함
            - 공유 데이터에 한번에 한 프로세스만 접근하도록 함
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fe243454-45f3-4d76-90e8-119c93f5cce1/Untitled.png)
    
    - condition variables
        - 여분이 없을 때 자신의 큐에 프로세스를 포함시켜 sleep, 또는 큐에서 프로세스를 깨우는 역할

### 8. 데드락

- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태
- Resource
    - 하드웨어, 소프트웨어 등을 포함하는 개념
    - I/O device, CPU cycle, memory space, semaphore 등
    - 프로세스가 자원을 사용하는 절차
        - Request, Allocate, Use, Release
- Deadlock 발생의 4가지 조건
    1. Mutual exclusion 상호 배제
        - 매 순간 하나의 프로세스만이 자원을 사용
    2. No preeption 비선점
        - 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음
    3. Hold and wait 보유하며 대기
        - 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음
    4. Circular wait 원형 대기
        - 자원을 기다리는 프로세스 간에 사이클이 형성되어야 함
- 자원 할당 그래프
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/997c0a68-2183-4625-a368-d00ab17177c2/Untitled.png)
    
- 데드락의 처리 방법
    1. Deadlock Prevention
        - 데드락을 방지
        - 자원 할당시 데드락의 4가지 필요 조건 중 하나가 만족되지 않도록 함
            1. Mutual Exclusion → 반드시 성립 필요
            2. Hold and Wait → 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 함
                - 방법 1. 프로세스 시작 시 모든 자원을 할당받게 하는 방법
                - 방법 2. 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청
            3. No Preemption 강제 빼앗음
                - 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
                - 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작
                - Stale을 쉽게 save하고 restore할 수 있는 자원에서 주로 사용(CPU, memory) (빼앗을 수 있으므로 빼앗음)
            4. Circular Wait
                - 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
    2. Deadlock Avoidance
        - 데드락의 방지
        - 자원 요청에 대한 부가정보를 이용해 자원 할당이 deadlock으로부터 안전한지 동적으로 조사, 안전한 경우에만 할당
        - 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법이 일반적
        - 데드락이 생길 수 있는 가능성이 있다면 자원을 할당하지 않음
- 자원할당 그래프를 이용한 교착 상태 검출
    - 무거운 교착상태 검출
        1. OS는 자원을 요청하거나 할당할 때마다 자원 할당 그래프를 갱신
        2. 이 자원할당 그래프가 싸이클을 이룰 때마다 교착상태가 발생
    - 교착상태가 없을 경우
        - 그래프에 사이클이 존재하지 않음
        - 프로세스의 작업이 계속 진행되어 교상태가 발생하지 않ㅇ므
    - 교착상태가 있을 경우
        - 운영체제는 그래프에 사이클이 발생하면 교착 상태가 검출된 것으로 판단
        - 단일 자원을 사용하는 경우 자원 할당 그래프에 사이클이 있으면 교착 상태
        - 다중 자원을 사용하는 경우 사이클이 있다고 해서 모두 교착상태라고 판단할 수 없음
    - 자원 할당 그래프를 유지, 갱신, 검사함으로서 오버헤드가 발생하는 단점
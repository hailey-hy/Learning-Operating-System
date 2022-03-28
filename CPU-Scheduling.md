### 1. 프로세스와 CPU

- 프로세스의 일생
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ce3caa3-463a-4d88-90e2-7d692094f96c/Untitled.png)
    
- CPU - burst Time의 분포
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d230f569-605d-4dfc-a6f6-95caf717da84/Untitled.png)
    
    - I/O bound job
        - 입출력이 많아 사용자와 교류가 많음
        - many short CPU burst
    - CPU bound job
        - 연산이 많아 CPU 할당시간이 김
        - few very long CPU burst
- 여러 종류의 job이 섞여 있기 때문에 CPU 스케줄링이 필요하다
    - Interactive Job에게 적절한 response 제공 요망
    - CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

### 2. CPU 스케줄러와 디스패쳐

1. CPU 스케줄러
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다
    - 커널 내의 CPU 스케줄러의 코드 (운영체제 코드의 일부이다)
    - Process counter와의 차이?
2. 디스패처
    - CPU의 제어권을 CPU 스케줄러에게 선택된 프로세스에 넘김
    - =문맥교환
3. CPU 스케줄링이 필요한 경우 프로세스의 상태 변화
    1. Running → Blocked
        - ex) I/O 요청하는 시스템 콜
        - nonpreemptive (자진반납, 강제하지 않음)
    2. Running → Ready
        - ex) 할당시간 만료로 timer interrupt
        - preemptive(강제 빼앗음)
    3. Blocked Ready
        - ex) I/O 완료후 인터럽트
        - preemptive(강제 빼앗음)
    4. Terminate
        - nonpreemptive (자진반납, 강제하지 않음)

### 3. CPU 스케줄링의 성능 척도

- performance Index, Scheduling Criteria, Performance Measure
1. 이용률 CPU utilization
    - CPU가 작동한 시간과 작동하지 않은 시간의 비율
    - CPU는 대단히 비싼 자원이므로 높을수록 좋다
2. 처리량 Throughput
    - 단위 시간 당 처리량
    - 시스템 입장에서 얼마나 많은 일을 했느냐
    - 많을 수록 좋다
3. 소요 시간, 반환 시간 Turnaround time
    - 대기 시간과 사용 시간을 합친 시간
4. 대기 시간 Waiting time
    - CPU가 레디 큐에서 기다린 시간
5. 응답 시간 Response time
    - 프로세스가 CPU를 쓰러 들어와서(CPU Burst) 최초로 CPU를 얻기까지 걸리는 시간

→ 3, 4, 5는 고객 (프로세스) 입장에서 얼마나 시간이 걸렸느냐

→ 짧을수록 좋다

### 4. 스케줄링 알고리즘

1. FCFS First Comes First Served
    - non preemptive
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/75f007e4-b84d-4fe7-a61d-4d5b455c92ca/Untitled.png)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/72c16442-8b94-4c93-ba63-31e32f23c828/Untitled.png)
    
    - Convoy effect 호위효과
        - short process behind long process로 인해 대기 시간이 길어지는 것
2. SJF Shortest Job First
    - 각 프로세스의 다음번 CPu burst time을 가지고 스케줄링에 활용
    - 다음 CPU Burst time를 예측
        - 과거 input data, branch, user...등에 대한 추측만이 가능
    - 예측한 CPU burst time 이 가장 짧은 프로세스를 제일 먼저 스케줄
    - SJF의 두가지 경우
        - Nonpreemptive
            - 일단 CPU를 잡으면 이번 CPU burst가 완료될 때까지 CPU를 선점(preemptive)당하지 않음
        - Preemptive
            - 현재 수행중인 프로세스의 남은 burst time 보다 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
            - 이 방법을 SRTF Shortest Remaining Time First라고도 부름
    - Optimal (최적의 방법): 주어진 프로세스들에 대해 minimum average waiting time을 보장
    - 단점
        - 기아 현상 발생 가능
        - 다음 번 CPU Burst가 얼마일지 정확히 알 수 없음 (예측만 가능)
    1. Priority Scheduling
        - 우선순위가 높은 프로세스에게 CPU를 먼저 할당
        - 우선순위는 정수값이며, 작은 정수일수록 높은 우선순위를 가짐
        - Priority scheduling의 두 가지 경우
            - 프로세스가 작동하고 있을 때 우선순위가 더 높은 프로세스가 도착하면?
            - Preemptive(강제로 빼앗음)와 Nonpreemptive
        - SJF도 일종의 Priority Scheduling이다.
            - Priority → predicted next CPU burst time
        - 단점
            - 우선순위가 낮은 프로세스가 영원히 실행되지 않는 기아현상 발생 가능
            
            → 해결: Aging 기법
            
            대기 시간이 길수록 우선순위를 높게 배정
            
    2. Round Robin (RR)
        - 실제로 CPU 스케줄링에서 가장 많이 사용하는 방법의 근간
        - time quantum
            - 각 프로세스가 동일한 크기만큼 가지는 할당 시간
            - time quantum이 초과되면 CPU를 빼앗기게 되고, (선점, preemptive) ready queue의 제일 뒤에 가서 다시 줄을 섬
        - n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻는다.
            
            → 어떤 프로세스도 (n - 1)q time unit이상 기다리지 않음
            
        - 수행
            - 할당시간이 길다면: FCFS와 같은 결과
            - 할당시간이 짧다면: 잦은 문맥교환에서 비롯된 오버헤드가 커짐
        - 장점
            - 짧은 응답시간
            - long job과 short job이 섞여 있을 때 최상의 효율
            - 상대적으로 공평
        - 단점
            - 균일 작업들에서는 비효율

### 5. Multilevel queue

1. Multilevel queue
    - 단일 큐를 다루는 앞선 알고리즘과 달리 여러 개 큐가 있는 상황 (레디 큐를 여러 개로 분할)
    1. 분할된 큐
        1. foreground (interactive) : Round Robin
        2. background (batch - no human interaction): FCFS
        
        → 각 큐는 독립적인 스케줄링 알고리즘을 가짐
        
    2. 큐에 대한 스케줄링이 필요
        - 만약 Fixed priority Scheduling이라면?
            - serve all from foreground then from background
            - Possibility of startvation
        - Time slice
            - 각 큐에 CPU time을 적절한 비율로 할당
    - 다른 큐로 이동할 수 없음
    - 기아 현상 발생 가능
2. Mutilevel Feedback Queue
    - 프로세스가 다른 큐로 이동 가능
    - 기아 현상에 대한 위험을 줄임
3. Multiple-Processor Scheduling
    - CPU가 여러 개인 경우의 CPU 스케줄링
    - Homogeneous Processor인 경우
        - Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내 가게 할 수 있음
        - 특정 제약 조건이 있을 경우 어떻게 CPU를 할당할 것인가?
    1. Load sharing
        - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘
        - 별개의 큐를 두는 방법 vs. 공동 큐를 사용하는 방법
    2. Symmetric Multiprocessing(SMP)
        - 각 프로세서가 각자 알아서 스케줄링 결정
    3. Asymmetric Multiprocessing
        - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 그에 따름
    4. Real time scheduling
        - 프로세스들의 데드라인을 함께 고려하여 우선순위를 설정
        1. Hard real-time systems
            - 정해진 시간 안에 반드시 끝내도록 스케줄링
        2. Soft real-time systems
            - 주기적인 일정을 갖고 있는 프로세스들의 경우 많이 적용됨 (ex. 동영상)
            - 일반 프로세스들에 비해 높은 priority를 가짐
    5. Thread Scheduling
        1. Local Scheduling
            - User level thread(운영체제가 스레드의 존재를 모름)의 경우
                
                → 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
                
                → 프로세스 내부에서 결정
                
        2. Global Scheduling
            - Kernel level thread의 경우
                
                → 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정
                

### 5. CPU 알고리즘의 평가 방법

1. Queueing models
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1e2f860c-0cc4-4eb2-bf3d-8bc4c71dab4e/Untitled.png)
    
    - job의 도착률과 처리률이 확률분포로 도착했을 때 수식에 의해 performance index값을 계산함
2. Implementation & Measurement (구현과 성능측정)
    - 실제 시스템에 알고리즘을 구현하여 실제 작업에 대해서 성능을 측정 비교
3. Simulation (모의 실험)
    - 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과를 비교
    - trace: 모의 프로그램의 input이 되는 데이터로, 비교 기준이 되므로 추출해서 사용하는 경우가 많음
### 1. 메모리

- 메모리는 주소를 통해 접근하는 장치
- 메모리 주소의 단계
    1. 물리적 주소
        - 낮은 레벨에 운영체제
        - 높은 레벨에 사용자 프로그램
    2. 논리적 주소
        - 각 프로세스마다 독자적으로 가지는 주소 공간으로, 이 주소를 통해 물리적 주소에 접근
        - CPU가 보는 프로세스의 주소
        - 각 프로세스마다 0번지부터 시작
    3. Symbolic Address
        - 프로그래머가 다루는 레벨
        - 컴파일되면 logical address, physical address가 된다
- 물리적 주소와 논리적 주소간의 주소 변환이 필요함

### 2. 주소 바인딩

- 논리적 메모리 주소를 물리적 메모리 주소로 전환(매핑)하는 것
    - Symbolic Address → Logical Address → Physical Address
    - 매핑: 하나의 값을 다른 값으로 1:1 대응하는 것
- 언제 변환할 것인가가 주요 이슈

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a370786f-98b8-4f27-aebb-535174bdf80e/Untitled.png)

1. Compile time binding 컴파일 타임 바인딩
    - 컴파일 시점에 물리적 주소로 변환됨
    - 프로그램의 절대 주소로 적재됨
    - 물리적 메모리 위치를 변경하려면 컴파일을 다시 해야 하는 불편
    - 현대 범용 컴퓨터에서는 잘 쓰이지 않고, 원자로 제어와 같은 전용 컴퓨터에 사용
2. Load time bildning 로드 타임 바인딩
    - 프로그램을 실행시키는 시점에 물리적 주소로 변환됨
    - 로더가 물리적 메모리 주소를 부여
        - 로더: 사용자 프로그램을 메모리에 적재시키는 프로그램
        - 컴파일러가 재배치 가능 코드를 생성한 경우 가능
            - 재배치 가능 코드: 기준 위치 주소를 사용하는 코드를 의미하며, 컴파일할 때 프로세스가 어디에 적재할 지 위치를 모를 때 생성됨. 실제 바인딩은 적재할 때 까지 지연됨
    - 프로그램 종료 시까지 물리적 메모리 위치가 고정
3. Execution time binding 실행 시간 바인딩
    - 프로그램이 실행 시작한 후에도 그 프로그램이 위치한 물리적 메모리상의 주소가 변경될 수 있음
    - 기준 레지스터, 한계 레지스터, MMU의 하드웨어 지원이 필요
    - 기준 레지스터
        - 재배치 레지스터라고도 부름
        - 프로세스의 물리적 메모리 시작 주소를 보유
    - 한계 레지스터
        - 현재 CPU에서 수행중인 프로세스의 논리적 주소의 최댓값, 프로세스의 크기를 저장
    - MMU
        - 메모리 관리 유닛이라는 하드웨어
        - CPU가 프로세스의 논리적 주소 참조시 MMU는 논리적 주소에 기준 레지스터값을 더해 물리적 주소값을 얻어냄
        - MMU의 메모리 관리 정책
            1. 가져오기 fetch
                - 디스크에서 메모리로 프로세스를 반입할 시기를 결정
                
                | 종류 | 설명 |
                | --- | --- |
                | 요구 적재(페이징) | - 운영체제나 시스템 프로그램, 사용자 프로그램 등 참조 요청에 따라 다음에 실행할 프로세스를 메모리에 적재 |
                | 예상 적재(페이징) | - 시스템의 요구를 미리 예측하여 메모리에 적재
                - 요청한 페이지 외의 다른 페이지도 함께 불러들여 탐색시간과 회전 지연시간을 가지는 보조 기억 장치의 특성을 참조한다. |
            2. 배치
                - 가져온 프로세스와 데이터를 메모리의 어느 위치에 놓을지 결정하는 작업
            3. 재배치
                - 메모리가 꽉 차있어 새로운 프로세스를 위해 오래된 프로세스를 내보내는 작업
                - 시기 및 사용빈도에 따라 다양한 방법

### 3. 메모리 주소

1. 절대 주소와 상대 주소
    - 메모리는 운영체제 영역과 사용자 영역으로 나뉨
    - 절대 주소
        - 실제 물리 주소를 가리킴
        - 메모리 관리자 입장에서 보는 주소
    - 상대 주소
        - 세그먼트가 하나일 때는 논리 주소와 동일어
        - MMU 기법에서 프로그램 주소 공간이 물리적 메모리의 한 장소에 연속적으로 적재되므로, 사용자 프로세스는 실제 물리적 주소를 알지 못함
        - 상대 주소는 항상 0번지부터 시작

### 4. 메모리 관리

1. 메모리 오버레이
    - 운영체제로부터 지원을 받지 않음 (프로그래머가 구현하는 기술)
    - 물리 메모리보다 큰 프로세스를 동작시키기 위해 사용하는 기술
    - 전체 프로그램을 몇개의 모듈로 나누어 컴파일하고, 수행시점에 필요한 모듈만을 메모리로 적재하여 사용
    - 모듈을 적재하고 내리는 역할은 오버레이 드라이버가 담당
2. 스왑
    - 메모리에 올라온 프로세스의 주소 공간 전체를 디스크의 스왑 영역에 일시적으로 내려놓는 것
    - 스왑 영역
        - 백킹 스토어라고도 부름
        - 디스크 내에 존재하는 파일 시스템과는 별도로 존재하는 영역
        - 파일 시스템은 비휘발성이지만, 스왑 영역은 프로세스가 수행 중인 동안에만 저장
        - 다수 사용자 프로세스를 담는 큰 공간
        - 접근 속도가 보장되어야 함
    - 스와핑 과정
        - 중기 스케줄러가 스왑 아웃시킬 프로세스를 선정
            - 메모리에 존재하는 프로세스의 수를 조절하여 메모리의 여유 공간 마련을 위함
            - 너무 많은 프로그램이 메모리에 동시에 올라오면 프로세스당 할당되는 메모리 양이 작아져 시스템 전체의 성능이 떨어지기 때문
        - 스왑 아웃
            - 서정된 프로세스를 메모리에서 디스크로 내림
        - 스왑 인
            - 현재 메모리에 올라온 프로세스 중 처리가 끝난 프로세스를 내쫓고 스왑 아웃됐던 프로세스를 다시 메모리로 올림

### 5. 물리적 메모리의 할당 방식

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9546b9eb-753c-42b0-ab6d-ab0bdd36d84d/Untitled.png)

1. 메모리 영역의 구분
    - 물리적 메모리는 운영체제 상주 영역과 사용자 프로세스 영역으로 나뉨
    1. 운영체제 상주 영역
        - 인터럽트 벡터와 함께 물리적 메모리의 낮은 주소 영역 사용
        - 운영체제 커널이 위치
    2. 사용자 프로세스 영역
        - 물리적 메모리의 높은 주소 영역
        - 현재 챕터에서 다루는 영역

### 6. 연속할당

- 프로세스를 메모리에 올릴 때, 그 주소 공간을 여러 개로 분할하지 않고 물리적 메모리의 한 곳에 연속적으로 적재
- 물리적 메모리를 분할하여 프로세스를 한 곳에 적재
1. 고정 분할 방식
    - 물리적 메모리를 주어진 개수 만큼의 영구적 분할로 미리 나누어둠
    - 가변 분할 방식에 비해 융통성이 떨어짐
        - 각 분할에 하나의 프로세스를 적재해 실행
        - 동시에 메모리에 올릴 수 있는 프로그램의 수가 고정
        - 수행 가능한 프로그램의 최대 크기 또한 제한
    - 외부 조각과 내부 조각 발생 문제
        - 외부 조각
            - 프로그램의 크기보다 분할의 크기가 작은 경우, 해당 분할이 비어 있는데도 프로그램을 적재하지 못해 발생
            - 어떤 프로그램에도 배정될 수 없는 빈 공간
            - 만약 외부 조각보다 작은 크기의 프로그램이 도착하면 적재 가능
        - 내부 조각
            - 프로그램의 크기보다 분할의 크기가 큰 경우 해당 분할에 프로그램을 적재하고 남는 메모리 공간
            - 하나의 분할 내부에서 발생하는 사용되지 않는 메모리 조각
            - 내부 조각만큼의 프로그램이 있어도 이미 배정된 프로그램이 있기 때문에 공간을 활용할 수 없어 메모리가 낭비됨
2. 가변 분할 방식
    - 메모리에 적재되는 프로그램의 크기에 따라 분할의 크기, 개수가 동적으로 변함
    - 내부 조각은 발생하지 않지만 외부 조각은 발생할 가능성이 있음
        - 이미 메모리에 존재하는 프로그램이 종료될 경우 중간에 빈 공간이 발생하며, 이 공간이 새롭게 시작되는 프로그램이 크기보다 작다면 외부 조각 발생
    - 동적 메모리 할당 문제
        - 물리적 메모리 내 가용 공간 중 어떤 위치에 프로세스를 올릴 것인가?
        
        | 종류 | 설명 |
        | --- | --- |
        | 최초 적합 | - 사용 가능 공간 리스트에서 충분히 큰 첫번째 공백 분할 공간에 적재하는 방법
        - 속도 측면에서 가장 우월 (메모리를 순서대로 탐색하여 가장 먼저 발견되는 공간에 적재) |
        | 최적 적합 | - 사용 가능 공간 리스트에서 가장 작은 크기의 사용 공간을 작업에 적재하는 방법
        - 이용률 측면에서 가장 우월 (정확하게 맞는 위치에 들어가는 방식을 취하므로 애매하게 빈 공간을 창출하지 않음) |
        | 최악 적합 | 가장 큰 사용 가능 공간에 적재하는 방법 |
    - 컴팩션
        - 외부조각 문제를 해결하기 위한 방법
        - 물리적 메모리 중에서 프로세스에 의해 사용중인 메모리 영역을 한쪽으로 몰고 가용 공간들을 다른 한쪽으로 모아서 하나의 큰 가용 공간을 만듦
        - 현재 수행 중인 프로세스의 메모리 위치를 이동해야 하므로 고비용
        - 가급적 적은 수의 이동을 고려해야 함

### 7. 불연속할당

1. 페이징
    - 특징
        - 하나의 프로세스가 물리적 메모리의 여러 위치에 분산되어 올라갈 수 있는 메모리 할당 기법
        - 프로세스를 연속적으로 메모리에 배치하지 않고 페이지로 분할해서 적재함
        - 외부 조각이 발생하지 않음
            - 만약 CPU가 참조하고자 하는 페이지가 현재 메모리에 올라와 있지 않을 경우 페이지를 디스크에서 읽어와야 하기 떄문에 막대한 오버헤드가 발생
    - 페이지 테이블
        - 가상 메모리의 페이지들을 물리적 메모리로 매핑하는 테이블
        - 각각의 프로세스마다 독자적인 페이지 테이블 보유
        - 하나의 테이블만 있을 경우에는 매핑 주소를 찾는 데 시간이 많이 걸리지만, TLB가 있을 경우 TLB에만 있는지 확인하면 되므로
    - bit
        - 페이지 테이블의 엔트리마다 아래의 비트를 둠
            - protection bit
                - page에 대한 접근 권한 (read, write, ...)
            - valid-invalid bit
                - valid → 그 주소에 유효한 내용이 있음
                - invalid bit → 그 주소에 유효한 내용이 없음
    1. 2단계 페이지 테이블
        - 페이지 테이블이 1개만 있다면 엔트리 수가 많아 오버헤드가 존재하므로 오버헤드를 줄이기 위해 존재
    2. inverted page table
        - 프로세스마다 존재하는 페이지 테이블로 인한 메모리 낭비를 막기 위함
        - 물리적 메모리의 프레임하나마다 페이지 테이블이 존재
        - 여러 프로세스마다 중복된 페이지가 있을 수 있으므로, PID를 같이 갖고 있어야 함
        - 주소 변환을 위해 모두 검색해보아야 함
    3. shared page
        - shared code를 담는 페이지는 메모리에 하나만 올림
        - read only되어 있을 것
        - 동일한 논리 주소에 있을 것
        - 공유되는 부분 외에 다른 부분(Private data)는 논리 주소의 아무 곳에 와도 무방
        - 각 프로세스들은 독자적으로 메모리에 올림
2. 세그멘테이션
    - 분할 방식
        - 동일한 크기가 아닌 의미 단위로 분할하여 메모리에 배치하는 것
            - 분할 단위: 프로세스 주소공간의 코드, 데이터, 스택 별로 분할
        - 따라서 세그먼트의 크기가 다르기 때문에 미리 쪼개 놓는 것이 불가능
    - 세그멘테이션의 구성
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7d6b7e57-cf60-4d69-970a-5173f06f9f5d/Untitled.png)
        
        - 논리적 주소
            - segment-number / offset 2가지로 구성
            - offset: segment 내에서 위치를 알기 위해, 세그먼트 주소로부터 얼마나 떨어져 있는가를 나타냄
        - 세그먼트 테이블
            - base: 세그먼트의 시작 위치 저장
            - limit: 세그먼트의 길이 저장, offset이 유효한지 검사하여 trap을 걸기 위함
        - 세그먼트 테이블 베이스 레지스터
            - 물리적 메모리에서의 segment table의 위치 저장
        - 세그먼트 테이블 렝스 레지스터
            - 프로세스가 사용하는 세그먼트의 수 저장
            - 프로세스가 몇 개의 세그먼트로 구성되는가?
            - cpu의 세그먼트 요구가 유효한지도 검사하여 접근을 막음
- 단점
    - 연속 할당과 동일한 문제 발생 → 세그먼트 길이가 동일하지 않으므로 외부 조각 문제 발생 가능
- 장점
    - 의미단위로 분할하기 때문에 의미 단위로 처리해야 하는 일들에 강점
        - protection
        - sharing
    - 세그먼트테이블은 엔트리 수가 몇개 되지 않으므로 주소 변환을 위한 메모리 공간 낭비가 덜함

c. Segmentation with Paging

- 세그먼트가 여러 개의 페이지로 구성됨
- 세그먼트 크기가 페이지 크기의 배수
- 세그먼트를 구성하는 페이지 단위로 물리적 메모리에 올라감
- 의미 단위로 관리하는 것은 세그먼트 테이블을 이용

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/69ac1bfd-fa1c-487a-b8a8-4fb000849864/Untitled.png)

- 구성
    - 논리 주소
        - 세그먼트 넘버와 오프셋
    - 오프셋
        - 페이지 넘버와 오프셋
    
    → 세그먼트 길이(페이지 개수)를 넘는 페이지 참조 요청이 들어오면 중단
    
    → 
    
- 접근 순서
    1. 논리 주소의 세그먼트 번호로 세그먼트 테이블의 해당 번호에 접근
    2. 세그먼트 테이블에서 세그먼트 길이 확인 후 오프셋과 비교, 페이지 테이블 시작 위치 확인
    3. 오프셋에서 페이지 시작 주소를 확인하여 페이지 접근
- 장점
    - 외부 조각 문제가 없음
    - 공유등의 문제도 해소
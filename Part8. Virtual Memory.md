### Virtual Memory
* 물리적 주소와 논리적 주소를 아예 분리 시켜 생각하자
* 실행에 필요한 프로그램의 일부분만 올리자
* 그러므로 물리적인 주소공간보다 더 많은 논리적 주소를 가질 수 있다.
* 주소 공간이 여러 프로세스들과 공유된다.
* 또한 더 효율적인 프로세스 창출과 병렬 프로그램이 가능하다.
* Demand Paging 기법을 사용하여 virtual memory를 구현하자

### Demand Paging Concept
프로그램을 메모리에 올리는 두가지 방법
1. 프로그램 전체를 메모리에 올리거나
2. 필요한 페이지만 올리거나
->그러므로 필요할 때 필요한 페이지만 메모리에 올리자.  
장점: 디스크로부터의 아이오 작업이 적다. 메모리가 덜 필요하다

### Pager
Swapping은 프로세스 전체의 범위로 디스크와 메모리를 바꾸는 것이지만, Pager는 필요한 페이지들만 디스크와 메모리에서 맞바꾸는 작업이다 (swap in, swap out)  
	Objective: 사용하지 않을 페이지를 읽는 것을 피하고, swap 시간과 물리적 메모리양을 줄이자.  
	프로세스의 어떤 일부는 메모리에, 또 다른 일부는 디스크에  
이걸 구분하는 방법은 HW support로 구별하자  

### Valid-InValid bit
Page table에서 메모리에 있는 페이지는 v 메모리에 없고 디스크에 있는 페이지는 i  
Page table은 처음에 i로 초기화 (메모리에 페이지가 올라온게 없으므로)  
Page fault: 원하는 페이지가 메모리에 없고 디스크에 저장되어 있을 때 -> 페이지를 참조하는 데 valid bit가 i인 경우  
다른 말로는 trap(Software interrupt)라고 함. 참조해야할 페이지가 메모리에 없으므로 당연한 것  
OS Handling page fault  
1. 사용자가 page를 참조하고 자 하지만 page table의 validation bit는 i.
2. Validation bit가 i인 것을 확인하면 trap(software interrupt) – 이유 메모리에 올라와 있지 않은 페이지를 참조하려하는 것이므로
3. OS는 요구하는 페이지가 디스크에 존재하는 지 확인을 하고 있으면 가지고 온다.
4. 메모리의 free frame을 찾고 디스크에서 메모리로 페이지를 스왑한다.
5. 페이지 테이블의 해당 엔트리를 valid로 바꿔준다.
6. 페이지 폴트가 일어났던 인스트럭션 재시작한다.
* 이때 OS는 이게 메모리 밖 주소를 참조해 invalid인지 원래 valid인데 invalid로 한건지 구분해줘야함(다른 테이블을 봄으로써)

### Aspects of Demand Paging
* Demand page를 위한 Hardware support
  - Page table에 validation bit 추가
  - Secondary memory 추가
1) 빠른 속도의 디스크가 필요<br>
2) Swap할 space가 필요<br>
3) Restart Instruction -> Page fault가 난 이후로 다시 명령을 수행해야하므로

### Performance of Demand Paging
* EAT = (1-p)* memory access+p * page fault time  
-> Page fault time= page fault overhead+swap in + swap out+ restart overhead  
<br>여기서 말하고자 하는 것: 페이지 폴트가 일어난다는 것은 곧 디스크에 접근해야하는 것
<br>디스크 접근 = 엄청나게 오랜 시간이 걸리는 작업 -> page fault의 수를 줄이자

### Page Replacement
* IF Free frame이 없다면? -> 잘 사용하지 않은 페이지와 써야할 페이지를 바꾸자
* 어떤 페이지를 Replace할 것인가가 중요! -> Page fault의 수를 줄이고 자주 안쓰는 것을 위주로 바꾸자
### Page replacement algorithm
* victim page: 디스크로 보내지게 될 page
1. 이 알고리즘을 통해 victim page를 선출하고 그 페이지를 디스크로 보내어 free frame으로 만든다.
2. 요구된 페이지는 free frame에 로드하여 읽고 page table내용을 업뎃한다.
3. Process를 다시 실행한다.

### Modify Bit
Page fault가 일어날 때 두개의 페이지가 이동하게 된다. (in, out) <br>
하지만 page swap out 부분에서 transfer을 줄일 수 있다. <br>
원리: 메모리에 있을 때 수정된게 아니고 디스크에 있는 내용이랑 같다면 굳이 디스크에 가서 또 쓸 필요가 있는 가? -> 그래서 등장한 것이 Modify bit <br>
1. 처음에 0으로 세팅
2. page에서 수정이 된 흔적이 있으면 1로 세팅
3. 수정된 페이지만 즉 modify bit가 1인 페이지만 swap될 때 다시 쓰여지고 아니면 간단하게 삭제된다.
->이렇게 함으로써 page-transfer의 overhead를 줄일 수 있다. <br>

### Benefits of Virtual Memory
* Protection: 프로세스는 page를 통해 가상 주소만 접근할 수 있어 실제 메모리를 보호할 수 있음  
* Transparency: 프로그래머 입장에서 일련의 가상 주소공간만 알면 된다.  
* Resource exhaustion: 실제 메모리 보다 더 많이 사용할 수 있다.  
Demand paging 기법으로 원하는 page만 로드하면 되기 때문에 가능하다. <br>

### Page replacement Algorithm
* 어느 페이지를 바꿔야 가장 페이지 폴트가 적게 일어날까?
<br>할당된 frame개수 자체가 적으면 페이지도 할당될 수 있는 페이지 수도 적어지므로 fault가 일어날 확률이 높아진다.
1. FIFO Algorithm: 가장 오래된 page부터 replace 시키자
- Belady’s Anomaly
<br> 하지만 이 알고리즘에서는 프레임을 많이 할당해도 fault가 더 많이 일어나는 경우가 발생
<br> 이유는 가장 오래된 page라고 해서 자주 안쓰이는게 아닐 수 있다. 이런 경우, fault의 수가 증가 되므로 프레임이 많이 할당 되어도 문제가 발생할 수 있다.
2. Optimal Algorithm: 가장 오랫동안 사용 안할 page를 replace 시키자
<br>이 알고리즘은 가장 낮은 page-fault율을 보장한다.
<br>하지만, 미래를 알수는 없음 – 어떤 페이지가 가장 많이 사용안할지 계산하기 어려움
3. LRU Algorithm: 과거의 기록들을 활용해 미래를 예측하자(중요)
<br> Temporal locality: 가장 최근에 들어온 data는 또 다시 참조 될 확률이 높다.
<br> 이걸 이용해서 가장 오랫동안 사용되지 않았던 것을 replace하자
<br>구현에 있어서 문제가 있다.
- Counter: page가 참조 될 때 마다 카운터를 업데이트(들어온 시간으로)
<br>하지만 항상 최솟값을 찾아야하므로 솔팅을 해야하는 것이 문제 overhead가 일어남
<br><br>So, reference bit를 활용하자
1. 처음에는 0으로 초기화
2. Page가 참조 되면 1로 된다.
3. Replacement할 때 0인 것을 replacement를 해주게 되면 정확히 페이지의 순서는 모를지라도, 참조 된것인지 아닌지는 구분해낼 수 있다.

### Thrashing
페이징 오버헤드가 극대화 된 것 -> 시스템에서 아무것도 안하고 페이징만 하고 있는 현상
<br>즉 컴퓨터에서 대부분의 시간이 page-fault를 처리하는 데에만 시간을 쏟음
<br>CPU는 놀고 있고 하드 디스크는 계속 바꾸고만 있고.. 비효율적
<br>주로 참조되는 페이지의 범위가 전체 메모리 사이즈보다 클 때 일어남

### Thrashing 해결방법
1. 워킹 셋을 통해 해결.
<br>Locality의 사이즈를 정확히 알수 없지만 어떤 방법을 통해 범위를 구한다.
<br>그래서 그 범위가 메모리 사이즈보다 높아지지 않게 조절하는 것
2. Page Fault Frequency Scheme  
어떤 프로세스에서 fault가 발생하면 free frame을 더 부여하는 방식

### Memory mapped files
* 시스템에서 파일의 내용을 메모리 번지수와 매핑
<br>메모리 포인터를 가지고 쓰기만 하면 파일의 내용으로 바뀌게 됨
<br>Write의 overhead를 줄일 수 있어 효율적, Demand paging 기법을 사용
* Shared memory 가능
<br>여러 프로세스가 동일한 파일을 메모리에 매핑하여 공유할 수 있기 때문이다.

### Memory Mapped IO
* 디바이스 I/O 컨트롤러는 명령과 데이터를 가질 레지스터가 존재
* I/O에 빠르고 편리하게 접근하기 위해, 사용되는 방식
* Memory와 장치 register를 map 하는 것이다.

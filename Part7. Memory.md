### Memory
모든 프로그램들은 디스크에서 메모리로 올린 뒤 실행되어져야한다.<br>
메인 메모리는 CPU가 유일하게 직접적으로 접근할 수 있는 저장 공간<br>
-	처음에 메모리에 복사되고 실행 되어야함
-	레지스터와 캐쉬는 CPU의 일부로 보여질 수 있다

### 메모리의 보호는 정확한 연산을 보장하기 위해 요구된다.
-	유저 프로그램으로부터 OS를 보호하고
-	또 다른 프로그램으로부터 유저 프로그램을 보호한다.
각 프로세스들은 분리된 메모리 공간을 가져야한다.<br>
하드웨어 보호: base+limit registers로 쌍을 이루어 logical address space로 정의함<br>
즉 Base부터 limit registers를 더한 것이 프로세스에게 할당된 메모리의 양<br>

### Logical vs Physical Address Space
-	Logical Address: 유저 프로세스에서만 의미있는 주소
	항상 0으로 시작<br>
-	Physical Address: physical memory unit이 보는 주소 (실제 주소)

### Address Space
-	Logical address는 한 프로세스가 그 자신의 메모리에 접근하기 위해 사용하는 메모리 주소
-	MMU hardware를 통해 logical->physical로 바꾸어 실제 메모리에 접근 할 수 있도록 함
-	OS는 virtual address->physical로의 매핑을 결정한다.


### MMU
하드웨어 지원으로 런타임에 logical->physical로 바꾸는 것

### Binding of Instructions and Data to Memory
메모리에 어떻게 배치시킬것이냐, <br>
사용자 정의 함수가 메모리에 적재될 때 메모리 특정 주소를 갖는 것<br>
각가의 바인딩 과정은 한 주소 공간에서 다른 주소 공간으로 매핑 하는 것<br>
Address binding은 세개의 다른 단계 들에서 일어날 수 있다.<br>
	Compile time: <br>
	컴파일러가 실행 프로그램을 만들 때 이미 함수는 변수가 적재될 위치가 다 정해짐<br>
	옛날 시스템의 방식, 절대코드, 유지보수가 힘듬<br>
	Load time:<br>
	컴파일시 프로세스가 메모리 내의 어디로 올지 모르면 컴파링러는 일단 바이너리 코드를 relocatable code로 생성<br>
	심볼과 진짜 번지수와의 바인딩은 프로그램이 주 메모리로 실제 적재되는 시간에 이루어짐<br>
	만약 시작 주소가 변한다면 유저코드를 리로드 해야할 필요가 있음<br>
	Excution time:<br>
	실행 될 때 매핑이 이루어짐<br>
	하드웨어의 도움이 필요함(base+registers)<br>
	프로그램이 실행 되기 전까지 바인딩을 미룬다.<br>

### Dynamic Relocation
-	Run time binding: 프로그램이 실행될 때 논리 주소를 피지컬로 바꾸는 작업
-	논리 주소와 물리 주소를 엮는 것은 메모리 관리에서 중요한 파트
### Swapping
-	Input queue: 디스크에서 메모리로 올라가고자하는 프로그램들의 집합
-	Out of memory가 일어날 경우 해당 프로세스를 디스크에 옮기고 디스크에 있는 실행하고자 하는 프로세스를 메모리에 올린다.

### Contiguous Memory Allocation
메인 메모리는 두개의 파티션으로 나뉘어 진다.
-	OS에 거주하는 low memory(커널)
-	유저 프로세스 high memory(유저 공간)
### Relocation-register scheme
-	Base + limit register는 논리 주소 공간을 정할 수 있다.
	Relocation register: 베이스랑 같은 의미이며 물리주소의 가장 작은 값을 의미<br>
	Limit register: 논리 주소의 범위를 포함한다. – 각 논리 주소는 limit register 주소값보다 작아야한다.<br>
-	이 두가지를 통해 주소를 보호하자(커널과 유저가 사용하는 주소에서)
### Memory Allocation: Contiguous Allocation
-	Multiple-partition allocatino
	파티션의 수에 의해 멀티프로그래밍의 정도가 제한된다.<br>
	파티션의 사이즈는 다양하게 존재 (효율성)<br>
	Hole: 이용 가능한 메모리 블락<br>
	메모리에 군데 군데 있을 수 있다. (size도 다르고 위치도 다 다르고)<br>
	프로세스가 메모리에 할당 될 때 큰 Hole을 기준으로 할당이 됨<br>
	OS는 할당된 파티션과 Hole에 대한 정보를 유지<br>

### External Fragmentation
	전체 메모리 공간으로 보면 메모리에 올릴수 있는 데 홀의 크기가 부분 부분 작아서 들어가지 못하는 현상<br>
	Compaction을 통해서 줄일 수 있다. -> relocation이 가능하고 실행 시간에 수행 될수 있을 때만 가능-> 그냥 프로세스들을 딱딱 붙히는 것<br>
	Non contiguous: 논리 주소 공간을 자유롭게 배치 할당된 주소 영역이 아니라 매핑 되는 데로 ㄱㄱ <br>

### Paging
비 연속적으로 메모리를 관리하는 체계
* 나누는 방법
물리적 주소를 고정된 사이즈로 나눈 것->frames<br>
논리적 주소를 같은 사이즈로 나눈 것->pages<br>
페이지 테이블: 프레임과 페이지가 mapping의 정보가 담겨있는 테이블<br>
n개의 페이지가 있다면 n개의 페이지 사이즈만큼 프로그램을 로드 할 수 있음<br>
페이지 사이즈는 하드웨어에 의해 정의된다. 2^m이 논리 주소의 사이즈이고 페이지 사이즈가 2^n이라면 <br>
-	Page number(p): m-n 사이즈 만큼 할당이 되고(페이지 테이블의 인덱스가 저장되는 곳)
-	Page offset(d): n 만큼 할당이 된다(논리 주소가 적힌곳)

### Why paging?
-	프로그래머가 phsyical한 메모리들을 볼 필요가 없이 코딩할 수 있다.
-	실제로는 번역 해야해서 효율적이지 않지만 프로그래머는 transparency하게 알 필요 없어서 contiguous하게 메모리를 접근하는 식으로 코딩이 가능

Paging에서도 MMU를 사용하기 때문에 프로세스마다 레지스터를 가지고 있어야 하고, page table을 가지고 있어야한다.


### Free-frame management
프로세스의 첫번째 페이지는 프리 프레임 리스트에 리스트 된 첫번째 프레임에 로드된다.<br>
그리고 할당된 프레임 넘버를 페이지 테이블에 할당한다.<br>

### No external fragmentation
-	Non contiguous한 메모리 할당으로 external fragmentation은 일어나지 않는다.
-	하지만 internal fragmentation이 존재

### Implementationn of page table
-	 각 프로세스마다 페이지 테이블이 존재
-	페이지 테이블은 메인 메모리에 저장
-	Page table base register는 페이지 테이블이 저장된 주소(시작점)를 저장 in register
-	PTBR: 프로세스에 있는 페이지 테이블 포인터
-	PRLR: 프로세스에 있는 페이지 테이블의 사이즈를 알려주는 것
-	Context switch가 일어나면 PTBR을 바꿔준다.

### Page Table in Main Memory
페이지 테이블을 메모리에 올리는 것에 문제 – 항상 두번씩 메모리에 접근해야함<br>
이유: <br>
1. 페이지 테이블에서 프레임 번호를 확인
2. 번호를 확인하고 그 프레임에서 데이터를 읽어옴
이 문제를 해결하기 위해 associative memory 또는 TLBs를 사용<br>
TLB는 캐쉬와 같은 개념으로 사용된다. (최근에 참조하였던 페이지 테이블 엔트리를 저장하는 것)<br>
TLB는 레지스터와 같이 속도가 빠른 하드웨어에 저장 시킨다.<br>



### TLB
CPU chip안에 정말 빠른 하드웨어로써 존재하며 서치 키는 논리 주소 결과는 프레임 주소를 반환한다. <br>
TLB hit: TLB에 찾고자 하는 게 있음, TLB miss: TLB에 찾고자 하는 게 없음<br>
Miss의 경우 페이지 테이블에 추가적으로 접근해야함 접근 후 TLB를 업데이트<br>

### Effective Access Time(EAT)
Hit ratio: TLB에서 페이지 넘버가 hit할 확률<br>
식: a*(TLB+data/instruction)ns+(1-a)(1+page table+data/instruction)ns<br>
왼쪽은 메모리에 한번만 접근 + 오른쪽은 메모리에 두번 접근<br>

### Memory Protection
Valid invalid bit를 사용함으로써 메모리를 보호<br>
Valid bit: 프로세스의 논리 주소 공간으로 사용할 수 있는 페이지 -> 이용 가능한 페이지<br>
Invalid bit: 논리 주소공간에 존재하지 않은 페이지를 표시<br>

### Internal fragmentation
메모리 마지막 부분에 해당하는 사이즈만큼 할당 받지 못하는 페이지에서 발생

### Shared Page
가정: 40명의 유저가 동시에 같은 프로그램을 실행한다고 했을 때 그 프로그램에 필요한 페이지 테이블은 하나만 있어도 충분 코드를 공유함으로써 필요한 메모리양을 줄일 수 있음<br>
Ex) 40명이 150KB코드와 50KB데이터 짜리 프로그램을 돌림 -> 8000KB필요<br>
코드를 공유함으로써 공유되는 데이터 부분은 그대로 돌림<br>


### Why Paging?
메모리 보호기능과 공유 메모리 기능을 제공한다.

### Internal Fragmentation
페이지의 경우 고정된 사이즈로서 메모리에 할당 되기 때문에 발생<br>
할당된 페이지가 요구된 메모리보다 클수 있다.<br>
Ex) page=4KB, 프로세스는 9KB 그러면 12KB의 3page가 필요<br>
그런데 3KB는 놀고 있는 경우가 발생 이게 Internal Fragmentation<br>

### Page Table의 구조
Hierarchical Page Tables: 2^20 즉 4MB짜리 피지컬 메모리를 필요라 하는 페이지 테이블이 존재<br>
너무 많음 So 계층형으로 나누어 1000 1000으로 나누어 100만번 봐야할걸 2000번으로 줄이자<br>
두개의 페이지 테이블 존재 (Outer와 Inner, Outer는 인덱스를 위한 테이블)<br>

### Inverted Page Table
페이지 테이블이 너무 클 때 실제 메모리에 하나의 엔트리만 유지하자<br>
메모리 한 프레임마다 한 항목씩 할당<br>
하나의 페이지 테이블만 존재(프로세스가 여러 개일지라도)<br>
-	메모리에서 훨씬 작은 공간을 점유한다: 논리 페이지마다 항목을 가지는 대신 물리프레임에 대응되는 항목만 테이블에 저장하기 떄문
-	서치가 느리다. 테이블을 리니어하게 다 찾아봐야하기 때문
-	Hash table, TLB사용으로 서치 문제 해결

I/O Systems
### Memory and I/O buses
CPU, Memory, I/O는 bus를 통해서 서로 통신한다.<br>
메모리의 일부분을 I/O에 Map을 시키자<br>

### Device Controller
* 포트, 버스, 디바이스를 작동시킬 수 있는 전기장치의 집합체 = Device controller
* 프로세서가 I/O에게 명령을 주는 방법
<br>-> 디바이스 컨트롤러는 OS(디바이스 드라이버)가 명령, 주소, 데이터를 기록한 레지스터를 가지고 있다.

###어떻게 프로세스는 I/O와 통신을 하는 가?
1. Direct I/O Instructions
* CPU가 I/O 명령을 실행하는 것
* CPU가 직접적으로 디바이스 레지스터에 명령을 쓰는 것
* CPU가 I/O연산이 완료될 때까지 기다리거나 인터럽트 될때까지 자기 할거 하는 것
- Polling과 Interrupt-driven
2. Memory Mapped I/O
* 디바이스 컨트롤 레지스터들은 메모리 주소공간과 mapping 되어있다.
* CPU는 I/O 요청을 맵핑된 메모리 지역을 읽고 씀으로써 실행할 수 있다.
3. DMA
* 디바이스가 CPU의 간섭 없이 직접적으로 메모리에 접근할 수 있게 하는 것

### 다이렉트 아이오와 Memory Mapped I/O
* 다이렉트의 경우 CPU가 컨트롤러에게 데이터를 전송한다는 요청을 하면 메인 메모리에 데이터 하나씩 전송해서 처리하지만
* 메모리 멥 아이오의 경우, 자신의 메모리 공간에 데이터를 저장하는 것은 디바이스 컨트롤러에 바로 올리는 것과 같으므로 간편해지고 빨라짐
-> 즉 I/O디바이스 접근 하기위한 특별한 명령어를 사용할 필요가 없어짐

### I/O Port Registers
1. Status register: CPU가 컨트롤러에게 I/O요청을 보낼 때 이 컨트롤러가 idle인지 아닌지 확인(busy bit). 디바이스 에러가 일어났는지 확인도 함
2. Control register: 명령을 실행하기 위해 준비 되어있는 지 확인하는 bit(command ready bit)
3. Data in register: CPU가 I/O에서 data를 읽어오기 위한 레지스터
4. Data out register: CPU가 I/O에 결과를 쓰기 위한 레지스터

###Polling
* 폴링에서는 CPU는 능동적이고 I/O는 수동적으로 행한다.
1. 상태 레지스터: 컨트롤러가 세팅하는 비트 바쁘면 1 놀면 0
2. Command ready bit: CPU가 세팅하는 비트, 일을 시키면 1, 안시키면 0
* Busy wait Cycle
1. CPU가 I/O작업을 요청한다.
- 만약 비지 비트가 1이면 0일 때까지 계속 체크
2. 만약 비지 비트가 0이 되고 컨트롤러가 작업을 수행하면
- CPU는 data out register에 바이트를 쓰고, Command ready bit를 1로 바꾼다
- 또한 컨트롤러도 비지 비트를 1로 다시 세팅
3. 아이오 연산이 끝날 때까지 CPU는 주기적으로 비지 비트를 관찰
- 아이오가 완료: 비지 비트, command ready bit 모두 0

### Interrupt Driven I/O cycle
* CPU는 패시브 I/O 디바이스는 액티브
* CPU는 디바이스에 명령을 발행, 완료가 되면 인터럽트가 되어 완료를 알게 됨.
1. CPU는 read(I/O) 명령을 발행
2. I/O 컨트롤러는 “CPU가 다른 일을 수행하는 동안” 데이터를 얻어온다.
3. I/O가 완료되면 컨트롤러는 CPU를 인터럽트한다.
4. 인터럽트 핸들러는 인터럽트 벡터를 이용해 데이터를 처리하고
5. CPU는 다시 재개한다.

### Interrupt Example
Ex) Page fault가 발생한 경우,<br>
* Interrupt Handler가 하는 일
1. 프로세스의 상태를 PCB에 저장 시킨 후
2. 인터럽트 된 프로세스를 waiting queue로 이동(I/O작업을 기다리는 queue)
3. I/O 리퀘스트를 보내고 page를 fetch하기 위한 아이오 작업이 수행된다.
4. I/O 작업이 완료되면, 다시 인터럽트를 보내어 아이오 작업이 끝났음을 알리고 페이지 테이블을 업데이트
5. 그 후 페이지 폴트가 일어났던 프로세스를 다시 실행 시킴

### Interrupt Example 2
Ex) 프로그램 library가 커널에 system call을 요청 -> trap이 발생한 경우
* Interrupt Handler가 하는 일
1. PCB에 프로세스 상태 저장,
2. 커널모드로 변경
3. 서비스를 돌리기 위한 커널 루틴을 올리면서 서비스 실행

### Interrupt Priority Level
* 인터럽트 종류들이 다양하고 수많은 인터럽트가 일어난다 -> 어느 인터럽트부터 실행 할지
* 우선순위에 따라 선점당할 수도 할 수도 있다.
<br>인터럽트 우선 순위
<br>전원 공급이 이상 → CPU의 기계적인 오류 → 외부 신호에 의한 인터럽트 → 입출력 전송 요청 및 전송 완료, 전송 오류→ 프로그램 검사 인터럽트 → 슈퍼바이저 호출(SVC 인터럽트)

### DMA
* CPU의 간섭없이 I/O 컨트롤러와 메모리를 직접적으로 연결하는 것
* DMA 수행 과정
1. CPU에서 DMA 제어기 초기화 (메모리 시작주소, 크기, 입출력 선택 등)
2. I/O장치에서 DMA요청
3. DMA controller에서 BUS요청
4. CPU가 bus grant(승낙)
5. DMA 수락 (I/O장치에게)
6. I/O 장치와 메모리끼리 직접적으로 자료 전송,
7. 끝나면 I/O장치가 DMA controller에게 ACK을 보냄
8. Ack을 받고 DMA controller가 CPU에게 완료 인터럽트

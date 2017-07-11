# System Structure
* Welcome to the OS wiki!
<h3>Kernel</h3>
<ul>
<li>오에스에서 가장 핵심 파트</li>
<li>자원 관리 기능을 지원</li>
<li>컴퓨터 켜짐과 동시에 항상 메모리에 상주한다.</li>
</ul>
<h3>OS service</h3>
<ul>
<li>유저 인터페이스: 유저가 컴퓨터를 사용하기 편하도록 인터페이스 제공</li>
<li>프로그램 실행: 시스템은 메모리로 프로그램을 Load할 수 있어야 하며 프로그램을 실행 시킬 수 있어야한다.</li>
<li>I/O작업: 파일 또는 I/O장치가 연루된 I/O작업이 요구될 수 있다.</li>
<li>파일 시스템 조작: 파일을 읽고 쓰는 것을 관리</li>
<li>커뮤니케이션: 네트워크를 통해 정보를 주고 받을 수 있다. (Shared memory, message passing)</li>
<li>에러 감지: 일어날 수 있는 에러들을 파악, 지속적으로 연산하기 위해 OS는 에러를 수정할 수 있어야한다.</li>
</ul>
<h3>Computer System Operation</h3>
<ul>
<li>I/O 장치들은 CPU cycle보다 더 길다.</li>
<li>그리고 CPU와 I/O 장치들은 독립적으로 동시에 수행 될 수 있다.</li>
<li>Interrupt를 통해 I/O작업이 끝났는지 아닌지 확인할 수 있다.</li>
</ul>
<h3>Interrupt</h3>

* CPU는 Interrupt가 들어오면 하던 일을 멈추고 Interrupt handler로 점프한다.
* Interrupt를 통해서 CPU와 Device controller 사이에 communication을 한다.
<h3>Interrupt Handling</h3>

* OS는 CPU상태를 레지스터와 PC에 저장함으로써 보존한다.
* 어떤 인터럽트 타입이 일어났는지 결정한다.
* 각 인터럽트 타입에 따라서 어떤 행동을 취할지 설정한다.
<h3>Interrupt Handling</h3>

*	하던 일을 멈춘다
* Fixed memory location으로 실행을 옮긴다
* Fixed memory location은 인터럽트를 위한 서비스 루틴이 어디에 있는지 알려주는 시작주소를 참조한다.
* 서비스루틴을 실행한다.
* 서비스루틴을 완수하고 CPU는 인터럽트 된 연산을 다시 수행한다.
<h3>Multiprogramming</h3>

* 여러 일들을 동시에 메모리에 올리자
* 잡 스케쥴링에 의해 하나의 일을 선택하자
* I/O작업이 필요한 경우 다른 일로 바꿔버리자

<h3>Dual mode operation</h3>

* OS 자체와 시스템 요소들을 보호한다.
* 올바르지 않은 프로그램이 올바르지 않은 행동을 하지 못하게 하도록
* (예 일반 유저 프로그램이 커널이 하는 짓을 하는 것을 막음)
* 모드 비트 -> 유저 모드와 커널 모드로 바꿀 수 있다. 하드웨어에 추가됨
* Privileged instructions -> 커널모드에서만 할 수 있는 instruction(디스크 접근, 메모리 접근)
<h3>System Call </h3>

* 커널에서 유저로 바꿀 수 있는 call, 유저가 Privileged instruction이 필요할 때 system call을 함으로써 접근이 가능하다.
* 이렇게 함으로써 잘못된 접근을 막을 수 있다.
* 사용자 프로그램의 메모리 주소 공간을 넘어서 메모리에 엑세스 할 수 없게 함.
* 또한 프로그램을 비정상적으로 종료할 수 없게 한다.
<h3>API</h3>

* 프로그래머가 시스템 콜이 어떻게 구현되었는지 알 필요 없다.
* 시스템 콜은 OS에 따라 달라지지만 API는 호환성이 좋다


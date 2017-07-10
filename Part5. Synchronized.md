### Synchronization
싱크로나이제이션을 해야하는 이유: 공유된 데이터를 병렬적으로 접근하는 것은 데이터의 불일치를 초래할 수 있음
<br>Ex) 한 프로세스가 있는데 각 쓰레드에서 데이터부분을 지맘대로 접근 난리남
<br><br>즉 프로세스들끼리 같이 돌아갈 때 순서대로 실행될수 있음을 보장해야한다
<br>적절한 순서가 없이 자기 멋대로 데이터 영역에 접근하여 데이터를 연산해버리면 원래 원하는 값을 얻을 수 없고 어떤 값이 나올지 모르는 race condition 현상이 일어나게 된다.
### Race condition
병렬적으로 실행될 때, 비록 프로듀서와 컨슈머 루틴이 정확할지라도 정확히 작동하지 않을 수 있다.
1. 여러 프로세스들의 접근과 조작(같은 데이터를 동시에)
2. 실행결과는 특정 순서에 따라 달라질수 있다.
<br>이것을 막기위한 것이 Synchronized 즉 동기화이다.
<br><br>그럼 왜 이 race condition이 일어날까?
* 프로세스들은 스케쥴링에 의해서 빠르게 바뀌면서 병렬적으로 일을 처리한다(효율성을 높이기 위해)
* 한 프로세스는 다른 프로세스가 스케줄되기 전에 부분적으로만 완료될수 있다.
* 분리된 프로세스에서 두개의 프로세스들이 동시에 실행 되어 data영역을 공유할 수 있다.
### Critical-Section Problem
공유되는 데이터에 접근하는 코드의 일부를 critical section이라고 부른다.
<br><br>즉 한 프로세스가 이 critical section에 들어오게 되면, 다른 프로세스들이 접근하는 것을 막자
### Critical section의 기본구조
`do{
Entry section
	Critical section
Exit section
	Remainder section
}while(true);`

### C.S 요구사항
1. ME: 하나의 프로세스가 크리티컬에 있으면 다른 프로세스들이 절대 들어와선 안된다.
2. Progress: deadlock이 있어서는 안된다. CS에 아무도 없는데 못들어가서도 안된다.
3. Bounded waiting: 각 프로세스들은 유한한 시도안에 CS에 들어갈수 있어야한다.(starvation free)

### CS algorithm
1. 피터슨 알고리즘
<br>Wants[i] 변수를 활용하여 cs에 들가고자 하는 프로세스들을 관리
<br>BUT while(wants[])쪽에서 인터럽트가 나서 둘다 true가 되고 둘다 못들어가는 상황이 발생
<br>하드웨어적인 요소를 추가 및 not_turn 변수를 추가(다른 프로세스들이 들어가길 원한다면 양보)
<br>`While(wants[j]&not_turn==1);`로 변경해서 인터럽트나서 변경이 되어도 하나는 돌아갈 수 있게끔
이렇게 해서 문제는 해결되지만 결국 두개의 프로세스만 가지고 해야하고 비용도 비싸다.
<br>그리고 소프트웨어로 해결하는거라 느리고 인터럽트가 많이 일어날 수 있다(not_turn에서도)
<br>그러므로 Hardware요소를 추가하자(Atomic-인터럽트가 걸리지 않는) lock
<br>Atomic한 작업들은 OS에게 system call을 하면서 할수 있도록 요청(하드웨어적인 요소이므로)
<br>Acquire와 release 함수가 있는데 둘다 atomic한 명령어이다.
<br><br>하지만 이 두 함수다 while 무한 루프로써 lock을 거는 것이기 때문에 CPU가 다른일을 못하게됨
<br>물론 컨텍스트 스위치가 일어나지 않게하고 짧은 시간의 일에선 좋을것
### 세마포어
* S변수: 공유되는 프로세스들의 수
<br>Wait()함수와 signal()함수를 통해서 이루어짐 뮤텍스랑 구조가 비슷
<br>비지웨이팅을 피하자
<br><br>Wait()함수에 웨이팅 큐에 프로세스를 넣은 코드를 삽입 와일문 대신
<br>Signal()함수에 wakeup()함수를 집어넣어 웨이팅 큐에서 가져오자
<br><br>But deadlock이 발생할수 있음 두개의 프로세스와 자원이 있을 때 s와 q를 1로 두면 각각 프로세스가 s와 q를 0으로 만들고 나면 빠져나오지 못하므로
### priority inversion
<br>Low한 프로세스가 돌다가 세마포어로 크리티컬 섹션에 잡았는데 스케쥴링 하다보니 high한 놈이 와서 preemtive함.
<br>근데 높은 프로세스도 크리티컬 섹션에 들어가야되는데 낮은 프로세스가 이미 락을 걸어놔서 들어가지도 못함.
<br>이경우 우선순위가 역전되는 경우가 발생.
<br>근데 또 중간 프로세스가 계속 들어오게 되면 중간거를 실행하느라 CS 반납도 못하고 계속 대기하게 생김.
<br>그러다보면 우선순위 상은 실행도 못하게 됨
#### 해결 방안: Priority inheritance protocol
낮은 프로세스가 세마포어를 가지고 있고 높은 프로세스가 세마포어를 요청하는 상황중인데 중간 프로세스가 선점하려고 할 때, 낮은 프로세스는 임시적으로 높은 프로세스의 우선순위를 계승하여 후다닥 cs를 처리하고 반납하고 높은 프로세스에게 cs입장권을 준다.
<br>그러면 낮은 프로세스는 선점 당하지 않고 빠르게 일을 끝내고 cs를 반납할 수 있으므로 우선순위 역전 현상이 오래걸리지 않아 빠르게 끝날 수 있다.
<br><br>하지만 데드락이 걸릴 수 있다. 낮은 프로세스가 s를 가지있는 상태에서 높은 프로세스가 들어왔다고 하자.
<br>높은 프로세스로 선점이 되고 s2를 요청하고 획득한 후 s를 다시 요청하게 되면 락에 걸려 이 프로토콜로 인해 낮은 프로세스가 실행이 된다.
<br>근데 이놈이 s2를 요청 하게 되면 서로 반납하지 못하므로 데드락에 걸리게 된다.
### Bounded Buffer Problem
<br>Empty: 버퍼에 비어있는 수(즉 만들수 있는 수)
<br>Full=0;(버퍼에 들어갈수 있는 수가 없다)

프로듀서의 경우
`버퍼에 들어갈수 있는 지 확인하고,      	wait(empty)`<br>
`크리티컬 들어갈수 있는 지 확인하고, 	wait(mutex)`<br>
`수행`<br>
`CS 반납하고				signal(mutex)`<br>
`하나 늘리고				signal(full)`<br>

컨슈머의 경우
`쓸거 있는 지 보고			wait(full)`<br>
`크리티컬 들어갈 수 있는 지보고		wait(mutex)`<br>
`수행`<br>
`CS반납하고				signal(mutex)`<br>
`하나 줄이고				signal(empty)`<br>
`
### Readers-Writers Problem
* Readers: 오직 읽을 수 만 있다
* Writers: 읽고 쓸수 있다.
* 이 두가지 개념이 서로 서로 깨지지 않게 하자
* 쓰고 있을 때 wrt=0이 된다. 라이팅이 수행되는 중 리드 프로세스가 수행 될 때 뮤텍스는 통과하지만 이프문에서 걸려버리고 라이트를 기다리게 됨
### Readers Process
`do {<`br>
`wait (mutex);`<br>
`readcount ++;`<br>
`if ( readcount == 1 )`<br>
`wait(wrt);`<br>
`signal(mutex)`<br>
`// reading is performed`<br><br>

`wait(mutex);`<br>
`readcount --;`<br>
`if ( readcount == 0 )`<br>
`signal(wrt);`<br>
`signal (mutex);`<br>
} while (TRUE)`<br>

### Writers Process
`do{`
  `wait();`
  `CS;`
  `signal();`
`}while(true);`

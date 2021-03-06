### Thread
* Lightweight Process (LWP)
* CPU 이용(utilization)의 기본 단위
### MultiThreaded process
* 하나의 프로세스에 여러 개의 쓰레드가 올라감
* 데이터 영역, 코드 영역, 파일 영역은 공유하지만 개별적으로 레지스터와 스택영역이 존재
### 멀티 쓰레드의 장점
* 하나의 프로세스가 멀티 프로세서의 이득을 취할 수 있음. 여러 개의 쓰레드들을 각각 프로세서에 올려 돌릴 수 있다.  
-> throughput 증가, parallelism
* 프로세스의 한 파트가 중단되어도 다른 파트 쓰레드들이 실행되므로 Responsiveness하다.  
-> response time 감소, concurrency
* IPC와 비교하였을 때 리소스 공유하기가 더 쉽다.
> __ex) thread 워드문서작성시 오탈자는 자동으로 수정이 된다.__
### Thread vs. Process
* fork()를 사용하여 새로운 process 생성시 시간과 메모리가 많이 요구된다.
* 하지만 pthread_create()를 이용하여 thread 생성에는 시작 시간과 메모리가 덜 소요된다.  
### 커널 쓰레드와 유저 스레드
* 유저 쓰레드 N : 커널 스레드 1 ->  유저 쓰레드는 유저 스페이스에서 library로 돌아가기 때문에 커널 단에서는 하나의 스레드로 인식.
그러므로 어떤 한 유저 쓰레드가 블락에 걸리면 다른 유저 쓰레드들은 커널 스레드로 접근 할 수 없으므로 멀티쓰레드의 장점인 병렬성을 살릴 수 없다.
* 유저 쓰레드 1 : 커널 스레드 1 -> 유저 쓰레드당 커널쓰레드 하나이므로 병렬성과 멀티 프로세싱에 적합, 하지만 모든 쓰레드 연산이 커널과 관련: 쓰레드이지만 헤비하게됨
#### But 쓰레드의 동기화를 맞추지 않으면 연산 결과가 다르게 나올 수 있음<br>
Ex) 데이터 글로벌 변수는 1, 두개의 쓰레드에서 +1 연산을 실행한다고 가정<br>
1. 그냥 순차적으로 2 3
2. 프린트에프에서 인터럽트 -> 3 3
3. storeword에서 인터럽트 -> 2 2

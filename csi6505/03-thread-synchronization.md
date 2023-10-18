# Thread Synchronization
#Resource #Resource/CS #thread #synchronize #mutex #spinlock #atomic #multicore 

### Race conditions


## Lock-based Thread Synchronization

### POSIX Mutexes

#### Deadlocks


### Mutual Exclusion Algorithm
컴퓨터는 시간을 tick 단위로 받아들인다. 한 tick 안에 두 개의 event가 발생한다면, 실제 시간으로는 동시에 발생하지 않아도 컴퓨터는 동시에 발생한 이벤트로 인식한다.

#### Mutual execution Property
- Critical section이 겹치면 안 된다.

#### Examples
**1. LockOne**
- l각자의 lock을 사용
- 내 lock을 걸고 다른 lock이 걸려있는지 확인 후에 critical section에 진입 가능

e.g.![[Pasted image 20231011134840.png]]
하지만 이것 역시 문제가 있음
![[Pasted image 20231011135228.png]]
1 irreflexive. cycle이 됨
2. 둘이 동시에 flag를 raise하면 deadlock에 빠짐
![[Pasted image 20231011140623.png]]

**2. LockTwo**
![[Pasted image 20231011141609.png]]
- 

#### Peterson's Lock
- LockOne과 LockTwo를 합침
- Mutual Exclusion
- Deadlock free
- Starvation free

단점
![[Pasted image 20231011142617.png]]
- 각 스레드가 lock을 가져서 한 스레드가 실행되려면 나머지 다른 스레드들이 희생되어야 한다.
- N개의 스레드 중 N-1 개가 희생됨
 
#### Filter Lock
- one victim & 2 flags variables
- 하지만 얘도 lock level이 너무 커짐

둘의 단점
1. n 개 스레드의 mutual execution을 지원하기 위해 2n개의 공유 변수가 필요함
2. weak liveness properties:
	- starvation freedom은 언젠가 스레드가 critical seciton에 진입할 수 있음을 의미.
	- lock level이 너무 커짐. bound가 필요함.
=> upper bound를 설정해서 너무 많은 스레드가 lock걸리지 않게 하고 싶음

##### r-bounded Waiting
- r bound를 걸어 weak livenesss를 막음.
- 언젠가 진입하지 않고 상한선이 존재
- r번 overtake 해야함

#### Bakery Lock
- 은행의 번호표와 유사. (해외에서는 빵집에서 번호표를 뽑는 곳이 있다.)
- First-Come-First-Served


- label은 번호표처럼 항상 증가

결론
- 
- 성능이 좋지 않아서 하드웨어의 atomic support가 필요하다.


126p 14에서 그래프가 꺾이는 이유: 2-socket 시스템이라서 한 소켓에 다 로딩한 후 다른 소켓에 실어서 overhead 발생


## C++ Mutexes
- C++11부터 멀티스레딩이 추가됨
- C++17은 다양한 lock library를 제공
	- `std::mutex`
	- `std::lockguard`
	- `std::scoped_lock`
	- `std::recursive_mutex` : helps to prevent self-deadlocks
	- `std::shared_mutex` (since C++17)
	- `std::shared_timed_mutex` (since C++14) 

#### `std::lock_guard`
![[Pasted image 20231018131703.png]]
**Basic lock ownership wrapper**
- RAII (Resource Acquisition Is Initialization) 방식으로 resouce의 소유를 object의 lifetime동안으로 묶음.
- Object가 release되면 lock도 unlock됨
- #3, #4 의 mutual exclusion을 위해
	- constructor에 mutex 제공
	- destruction에 mutex unlock


## Locks Using Atomic Operations
### Test-And-Set Lock
```cpp
bool locked = false; // atomic register

void lock() {
	while (test_and_set(&locked));
}

void unlock() {
	locked = false;
}
```
- single flag field per lock
- Acquire lock by **atomically** settting flag (false -> true)
- Read & Write
	- Read the flag: return whether it was already set before the call
	- Write the flag: set flag (false -> true)
- Reset flag to unlock
- 성능이 안 좋음 -> cash 문제
	- test-and-set call이 다른 코어 cash에 복사된 값을 무효화함
	- High contention on memory interconnect
	 - 근본적인 문제임!

```cpp
volatile bool locked = false; // atomic register

void lock() {
	do {
		while (locked);
		if (!test_and_set(&locked)) return;
	while (true);
}

void unlock() {
	locked = false;
}
```


## Locks with Condition Variables


## Lock contention & Lock Granuality


## Barriers
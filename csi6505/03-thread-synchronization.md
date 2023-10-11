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

#### Filter Lock


#### Bakery Lock


### C++ Mutexes


### Locks Using Atomic Operations


### Locks with Condition Variables


### Lock contention & Lock Granuality


### Barriers
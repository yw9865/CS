# Lecture 7: Kernel Synchronization
#resource #resource/CS #csi3107 #system-programming #linux #synchronize #semaphore #spinlock #interrupt #mutex

- 목차

---

<aside>
💡 핵심 정리

1. KCP의 의미와 3가지 동기화 이슈
2. spin lock vs semaphore
    - 매커니즘, context, CPU환경, 적절한 기간, preemption, holder 개수
3. INTR을 막기 위해 UMP와 SMP에서 사용할 수 있는 방법
</aside>

- 정답
    1. kernel control path란 커널 모드에서 수행하는 일련의 커널 코드로, interrupt-safe, SMP-safe, preemption-safe 3가지를 충족해야 한다.
    2. spin lock vs semaphore
        
        
        |  | spin lock | semaphore |
        | --- | --- | --- |
        | 매커니즘 | non-blocking lock | blocking lock |
        | 사용 가능 context | INTR context에서도 사용 가능 | PROC context에서만 사용 가능 |
        | CPU 환경 | SMP에서만 가능 | UMP, SMP 둘 다 가능 |
        | 기간 | 짧은 기간에 적절. switch 오버헤드가 없음 | 긴 기간에 sleep시켜 CPU 사이클 낭비를 막음 |
        | kernel preemption | preemption 불가능 | preemption 가능 |
        | lock holder 개수 | 한 프로세스만 lock hold 가능 | counting semaphore로 동시 허용 |
    3. ㅇㅇ
        
        
        |  | excpetion | interrupt | defferable function(softirq, tasklet) |
        | --- | --- | --- | --- |
        | UMP | 세마포어 | INTR disable | 문제 없음 |
        | SMP | 세마포어 | INTR disable + spin lock | spin lock |

---

커널 동기화 primitive 요약

- atomic operation, barrier
- locking
    - non-blocking lock : spinlock, RWlock
    - blocking lock : semaphore, RWsemaphore, Mutex, Completion, BKL
- RCU
- interrupt disabling, preemption disabling

|  | non blocking | blocking |
| --- | --- | --- |
| 프로세서 | SMP에서 효율적 | UMP |
|  |  |  |

# 커널 동기화 개요

  커널 동기화 이슈는 멀티 프로세서 환경이 아닐 때도 존재했다. 커널의 데이터는 공유되기 때문에 인터럽트, 여러 CPU, 프로세스 등이 커널 데이터를 동시에 접근할 수 있기 때문이다. 그 때문에 동기를 위한 primitive가 발전되어 왔다.

**Kernel Control Paths (KCP)** 

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled.png)

- 커널 모드에서 수행하는 일련의 커널 코드
    - 인터럽트 핸들러, 시스템 콜 핸들러, exception 핸들러 등이 해당한다.
- KCP가 꼬이기(interleaving) 시작하면서 동기 이슈가 생겼다.
- 커널 동기화는 **race condition**을 막기 위해 필요하다.
    - 커널 모드 작업 중 인터럽트가 발생했는데, 커널 모드 작업 중 다룬 데이터를 건드린다면(critical section) race condition 발생

**커널 동기의 목표**

커널 동기에는 크게 3가지 이슈가 있고, 3가지를 해결해야 한다.

- **interrupt-safe** : 인터럽트 핸들러의 동시 접근으로부터 안전해야 한다.
- **SMP-safe** : 멀티 프로세서의 동시성으로부터 안전해야 한다.
- **preemption-safe** : 동시 커널 preemption으로부터 안전해야 한다. (프로세스 스케쥴)

커널은 동기화 primitive들을 가지고 3가지 경우에 대해 잘 다뤄야 한다.

**커널 동기의 primitive**

- atomic operation, barrier
- locking
    - non-blocking lock : spinlock, RWlock
    - blocking lock : semaphore, RWsemaphore, Mutex, Completion, BKL
- RCU
- interrupt disabling, preemption disabling

---

# Atomic operations, Barrier

## Atomic operation

- 하드웨어의 도움을 받아 ***atomic***을 보장한다.
    - atomic : 중간에 개입 없이 명령어들이 실행된다.
- 주로 다른 정교한 primitive를 구현하기 위한 요소로 쓰인다.

## Barrier

- KCP들이 어느 시점까지 도달할 때까지 진행을 막는다.
    - 말 그대로 KCP 앞에 벽을 세워 기다리는 것.
- 현대의 컴파일러와 프로세스는 최적화 옵션을 수행한다. 이 과정에서 순서가 재정렬될 수 있다.
    - compiler barrier : 컴파일러가 최적화 수행 중 동기를 깰 수 있으므로 컴파일 과정에서 순서 변경을 막는다. (compile time)
    - memory barrier : 프로세서가 임의로 순서를 정하지 않도록 순서를 정해준다. (execution time)

---

# ⭐Locking

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled%201.png)

- KCP가 lock된 공유 자료구조나 critical region에 접근하면, 풀리기 전까지 기다린다.
    - atomic operation은 크고 복잡한 critical region을 다루기에 충분하지 않다.
    - 좀 더 일반적인 매커니즘
- lock을 중심으로 직렬화 된다.
    - 그 때문에 성능도 감소한다.
- 2가지 종류의 lock이 있다.
    - **non-blocking lock** : spin lock
        - busy-waiting
        - 멀티 프로세서에서 좋다.
    - **blocking lock** : kernel semaphores
        - 싱글 프로세서나 멀티프로세서 둘 다 가능하다
        - 스케쥴링을 동반해서 오버헤드가 크다.
- 이처럼 lock은 성능과 연관되어 있기 때문에 개발자가 적절하게 사용해서 성능과 안정성을 챙겨야 한다. 그렇다면 언제 lock을 사용해야할까?

**lock 사용 기준**

- 데이터가 전역인가?
- 데이터가 process context와 interrupt context에서 공유되는가?
- 데이터가 다른 두 interrupt handler에게 공유되는가?
- 현재 프로세스가 block되는가? 된다면 공유 데이터는 어떤 상태로 남는가?
- 프로세스가 공유 데이터 접근 중에 preempt된다면, 새로 스케쥴된 프로세스는 그 데이터에 접근할 수 있는가?

커널 개발자의 미션은 *lock contention*과 *scalability* 지원이다.

- **Lock contention (성능)**
    - lock된 데이터에 다른 프로세스가 접근하는 것
    - 너무 많은 경쟁은 병목을 불러오므로, 적절히 사용해야 한다.
- **Scalability (확장성)**
    - CPU, 메모리 수를 늘리는 등 자원을 투자할 때, 성능이 비례하게 상승하야 한다.

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled%202.png)

- 이를 위해 *coarse-grained*인지 *fine-grained*인지 lock의 범위도 개발자가 판단하며 걸어야한다.
- 보통 크게 걸고 점점 범위를 좁혀가면서 최적화함

## ⭐Spin Locks

- **non-blocking lock**의 기본적인 형태
- Spin lock은 **busy-wait** loop를 돈다.
    - busy-wait는 프로세스가 루프를 돌면서 기다린다는 것
    - **멀티 프로세서(SMP) 환경**에서 효율적으로 사용 가능
        - 싱글 프로세서(UMP) 환경에서 busy-wait는 깨워줄 사람이 없기 때문에 무한 루프에 빠지게 되므로 사용할 수 없다.
    - **인터럽트 핸들러**에서 사용 가능
        - (REMIND) interrupt context에서는 양보해준 프로세스에게 돌려줘야할 의무가 있으므로, block되면 안된다. 그러므로 blocking lock은 사용할 수 없지만 non-blocking lock은 사용 가능하다.
    - **(local)** **Interrupt disable**과 같이 사용
        - 만약 spin lock 도중 인터럽트가 들어올 수 있기 때문에, INTR disable과 같이 사용해야 한다.

### Read/Write Spin Locks (RWLock)

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled%203.png)

- 공유 영역을 읽기만 하면 문제가 생기지 않으므로, 읽기 요청은 동시 접근을 허용
- 쓰기 요청은 하나만 접근 가능. 나머지는 기다려야함.

### ⭐인터럽트 핸들러에서 Spin lock

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled%204.png)

- spin lock은 block하지 않기 때문에 인터럽트 핸들러에서 사용할 수 있다.
- 다만 spin lock을 걸기 전에 local interrupt disable을 해야 한다.
    - spin lock을 통해 다른 CPU의 접근을 막을 수 있다.
    - 하지만 spin lock으로 busy-wait loop 중에 인터럽트가 들어와 같은 영역에 접근한다면 lock으로 잠긴 부분을 접근하므로 deadlock에 걸리게 된다.

## ⭐Kernel Semaphores

- **blocking lock, “sleeping lock”**
    - wait(down) / signal(up) 2개의 동작으로 재우고 깨움.
    - INTR context가 아닌, system call 혹은 workqueue 같은 것은 block되어도 상관 없다.
- 프로세스간 **스케쥴링을 동반한 lock**이기 때문에 무겁다.
    - 프로세스 스위치 발생 → OS가 감당할 로드가 큼
    - fine-grained한 공간, 짧은 기간이면 오버헤드가 크기 때문에 쓰지 않는다.
- INTR context에서 사용할 수 없다.
    - *INTR handler, sortirq, tasklet*
- 하나의 접근만 허용하는게 아닌(binary semaphore), 여러 접근을 허용할 수도 있다. (counting semaphore)

### MUTEX & Counting Semaphore

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled%205.png)

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled%206.png)

- 접근 전에 down을 해서 count가 음수가 되면 sleep
- count가 1이면 binary, 1보다 크면 counting

### Read/Write Semaphores

- RWSpin lock처럼 write 요청만 wait

### Mutexex

- 세마포어의 기능을 제한시켜 특정 상황에서 성능이 더 좋게끔 만듦
    - 기존의 세마포어는 성능 이슈와 너무 일반화된 프레임워크였다.
    - binary semaphore와 비슷하지만 더 간단한 인터페이스와 좋은 성능, 제한된 사용처를 가짐

### Completion

- SMP 환경에서 사용할 때, 다른 CPU에서 동시에 up, down을 하게 되면 문제가 생겼다.
- 이를 위해 세마포어간 동기화를 해주는 커널 세마포어를 만들었다.
    - 동시에 두 up, down 요청에서만 효과적인 것이 아니라, 5개의 wait 세마포어를 동시에 up 시킬수도 있게 되었다.

### 세마포어의 데드락

- 세마포어는 좋은 동기화 도구지만, up down 순서를 개발자가 잘못 사용하면 데드락이 발생할 수 있다.
- 만약 2개 이상의 세마포어를 동시에 사용한다면, nested lock에 순서를 부여하여 circular wait를 막아 데드락을 피한다.

## ⭐Spin locks vs Semaphores

|  | spin lock | semaphore |
| --- | --- | --- |
| 매커니즘 | non-blocking lock | blocking lock |
| 사용 가능 context | INTR context에서도 사용 가능 | PROC context에서만 사용 가능 |
| CPU 환경 | SMP에서만 가능 | UMP, SMP 둘 다 가능 |
| 기간 | 짧은 기간에 적절. switch 오버헤드가 없음 | 긴 기간에 sleep시켜 CPU 사이클 낭비를 막음 |
| kernel preemption | preemption 불가능 | preemption 가능 |
| lock holder 개수 | 한 프로세스만 lock hold 가능 | counting semaphore로 동시 허용 |
- 보호할 범위의 크기
- PROC context vs INTR context
- UMP vs SMP

에 따라 선택할 수 있는게 다르다.

## The Big Kernel Lock (BKL)

- 오래된 개념으로 지금도 남아있긴 하지만 사용하지 않음 (obsolete concept)
- 큰 커널 lock으로 커널 모드에서 한 CPU만 동작할 수 있음.

---

# Read-Copy-Update (RCU)

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled%207.png)

- Write하면서 동시에 Read할 수 있음.
- 다만 연결 리스트처럼 포인터 자료구조에서만 사용 가능

**동작과정**

- 읽는 도중, write가 이루어지면 복사해서 2개를 유지한다
- write한 데이터를 다 읽었다면, 포인터를 변경해서 다음부터는 update된 데이터를 읽게 한다.

---

# Interrupt/Preemption disable

## Preemption Disabling

- 기본적으로 preemptive kernel이어도 spin lock이 걸려 있으면 preemption을 할 수 없다.
    - 공유 데이터에 대한 접근은 막을 수 있더라도, preemption시에 원래 프로세스가 수행하던 CPU 안에 로컬 캐시된 값, 레지스터 값들은 lock으로 보호할 수 없다.
- hardware context를 보호하기 위해 preemption(스케쥴링)을 막는다.
- spin lock이 없어도 preemption이 일어나면 안되는 상황에 따로 사용하기도 한다.
    - 예시) 프로세스 각자의 데이터를 다룰 때, 공유 데이터는 아니지만 프로세스 A가 다루던 변수를 스케쥴되어 B가 다루게 되면 문제가 생길 수도 있다.

## Interrupt Diabling

- Local CPU환경에서도 인터럽트 때문에 KCP가 꼬인다

⇒ KCP가 꼬이는 인터럽트를 막아버리는 무식한 방법 (하지만 효과적임)

- SMP에서 (local) INTR disable은 다른 CPU에게 어떤 영향도 주지 않는다. spin lock으로 다른 CPU의 접근을 막아야 함.

---

# ⭐언제 어떤 primitive를 사용해야 하는가?

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled%208.png)

|  | exception | interrupt | deferable function(softirq, tasklet) |
| --- | --- | --- | --- |
| UMP | 세마포어 | INTR disable | 문제 없음 |
| SMP | 세마포어 | INTR disable + spin lock | spin lock |

![Untitled](Lecture%207%20Kernel%20Synchronization%20e986fcbb9e8f4a6a9f2d97f30f719e6c/Untitled%209.png)

---

[Lecture 6: Signals](https://www.notion.so/Lecture-6-Signals-3364c7383056411c8a400d17e5a2af84?pvs=21) [Lecture 8: Memory Addressing](https://www.notion.so/Lecture-8-Memory-Addressing-b6a354b270ac4d0d879b5b188d343208?pvs=21)
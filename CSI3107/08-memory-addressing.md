# Lecture 8: Memory Addressing
#Resource #Resource/CS #csi3107 #system-programming #linux #memory

- 목차

---

<aside>
💡 핵심 정리

1. 인텔 아키텍처에서 GDT의 크기
2. 리눅스가 세그먼트를 어떻게 사용하고 왜 그렇게 사용하는지
3. 32비트 머신에서 10, 10, 12로 PGD, PTE, OFFSET이 나뉘면 페이지 테이블의 개수와 페이지의 크기는?
4. 32비트 머신에서 extend하여 10, 22로 나뉘면 페이지 테이블의 개수와 페이지의 크기는?
</aside>

- 정답
    1. GDT는 8바이트의 segment descriptor를 13비트를 통해 구분할 수 있으므로 2^13 * 8바이트 = 64KB이다.
    2. 세그먼트는 특정 아키텍처에서만 쓰이는 인기 없고 복잡한 개념이여서, 리눅스는 간편하고 호환성있게 설게하기 위해 세그먼트 활용을 최소화 했다. 8142개 중 30개 언저리의 세그먼트를 사용하며 크게 4개의 세그먼트를 사용하고 모든 세그먼트의 크기를 linear address space와 똑같은 크기(4GB)로 통일했다.
    3. PGD 1개 + PT 2^10개. Page Table은 총 1025개
        
        페이지는 PTE가 2^10개 있으므로, 페이지는 2^10개 있고 각 페이지의 크기는 OFFSET만큼, 즉 2^12B = 4KB이다.
        
    4. PGD 1개, 페이지 테이블은 1개
        
        PGD 엔트리는 2^10개, 페이지는 2^10개. 페이지 크기는 2^22B = 각 4MB
        
        ![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled.png)
        

8장에서는 메모리에 관한 자세한 내용을 다루기 전에 하드웨어가 제공하는 기능을 다룰 예정

**Overview**

![Memory Management Units (MMU) in x86](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%201.png)

Memory Management Units (MMU) in x86

# Segmentation (x86)

인텔 아키텍쳐에서는 메모리 주소를 3가지로 분류한다.

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%202.png)

- Logical address
    - 세그먼트(segment)의 주소
    - 특정 세그먼트(code, data 등)의 몇 번째 offset으로 나타냄
- Linear address (or virtual address)
    - 세그멘테이션의 결과
    - 0~4GB의 주소로 나타남(32비트 프로세서 기준)
- Physical address
    - 실제 메모리에 위치하는 주소

## Segmentation in Hardware (x86)

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%203.png)

- 세그먼트 유닛이 logical address를 linear address로 변환한다
    - logical address = segment selector, offset
        - segment selector : 16비트의 세그먼트를 구분하는 레지스터
        - offset : CA dependent함. 32비트의 세그먼트 안에서의 주소
    
    ![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%204.png)
    
    - segment selector는 16비트지만, 하위 3비트를 다른 정보를 나타내는데에 사용
        - 13비트로 세그먼트 선택 가능 → 최대 8142(2^13)개 세그먼트 존재 가능
- x86은 자주 쓰이는 세그먼트에 대한 레지스터를 따로 마련해 둠
    - cs, ds, ss 레지스터는 각각 코드, 데이터, 스택 세그먼트 영역에 대한 포인터이다.

**Segment Descriptor**

- 각 세그먼트는 8바이트의 ******************Segment Descriptor******************가 나타낸다.
- 세그먼트 디스크립터는 Global Descriptor Table(GDT) 혹은 Local Descriptor Table(LDT)에 있다.
    - GDT는 그러면 크기가 8바이트인 최대 8142개의 세그먼트를 갖고 있을 수 있다
    
    ⇒ GDT : 8K * 8B = 64KB
    

**GDT and LDT**

- 보통 GDT 하나가 정의됨
- UP : GDT 1개, MP : GDT 각 CPU 만큼 존재

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%205.png)

- **GDTR**이 GDT의 베이스 주소와 GDT의 크기를 알리는 16비트(→64KB 표현 가능)의 table limit을 갖고 있음
    - IDT의 베이스 주소 정보를 갖는 IDTR과 동일한 역할.

## Segmentation in Linux

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%206.png)

리눅스는 세그먼트 활용을 최소화했다.

- 세그먼트가 특정 아키텍처에서만 쓰이는 인기 없고 복잡한 구조이다
    - 간단한 메모리 관리
    - 호환성 (portability)
    
    을 위해 리눅스는 세그먼트 대신 페이징 기법을 적극 활용했다.
    

**리눅스에서 세그먼트는**

- 모든 세그먼트의 크기가 linear address space와 같은 4GB이다.
- 한 세그먼트의 크기를 8B씩 할당하면 최대 8192개를 가질 수 있지만, 리눅스는 4개의 세그먼트만 사용한다.
    
    ![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%207.png)
    
- 각 세그먼트는user code, user data, kernel code, kernel data 영역을 나타낸다.
- 4개의 세그먼트 모두 4GB로 크기는 같지만, *privilege level(*접근 권한*)*이 다르다.

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%208.png)

IDT와 굉장히 유사. 연계해서 생각할 것

- IDT에서 몇 번째 엔트리인지 인터럽트 벡터 n이 정해지면, segment selector를 통해 GDT의 세그먼트 정보에 접근하고, 커널 코드 세그먼트에 바로 접근할 수 있다.

# Paging

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%209.png)

  페이지의 핵심 철학은 연속된 linear address를 불연속된 physical address에 맵핑할 수 있다는 것이다.

- 물리 메모리의 크기가 가상 메모리의 크기보다 작아도 된다.
    - 100GB 램을 가지고 있더라도, 프로세스 하나당 4GB씩 램에 할당한다면, 25개의 프로세스밖에 실행하지 못한다.
    - 즉, 4GB의 linear address space를 물리 메모리에 전부 올리는 건 현실적이지 않다.

## Paging in Hardware (x86)

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%2010.png)

## Paging in Linux

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%2011.png)

- 멀티 레벨 페이징 지원
    - 공간을 효과적으로 사용할 수 있지만 2단 접근을 해야해서 시간상 오버헤드 발생
- portability와 scalability가 극대화 시키기 위해 환경마다 코드의 변화를 적게, general하게 코딩했다.

### Page Directory and Page Table (multi-level paging)

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%2012.png)

### Kernel + Hardware

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%2013.png)

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%2014.png)

MMU의 적절한 도움을 받아 페이징을 활용한다.

### x64에서 페이징

![Untitled](Lecture%208%20Memory%20Addressing%20b6a354b270ac4d0d879b5b188d343208/Untitled%2015.png)

- 64비트는 복잡하다

---
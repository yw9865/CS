# Control Flow
#Resource #CS #csi8105 #compiler #Resource/CS 

**Index**
1. Control transfer
2. Control flow
3. Execution -> Dynamic control flow
4. Compiler -> Static control flow
5. Control flow analysis

## Basic Block (BB)

코드를 실행할 때, a sequence of **consecutive** operation
- 코드에 들어가는 entry
- 다른 코드로 jump하는 코드의 끝
- halt, branch의 가능성이 없어야 함.

**찾는 법**
- 첫 번째 operation = BB의 시작
- Branch의 destination이 되는 곳 = BB의 시작
- Branch = BB의 시작
## Control Flow Graph

*Definition:*
> Basic Block의 실행 순서를 그래프로 그림.

- 모든 BB는 다른 BB로 가는 edge를 가질 수 있음
- 시작과 끝에 pseudo vertex인 *entry node*와 *exit node*가 있을 수 있음.

**Dominator(DOM)**
> 어떤 노드 y를 거치기 위해 노드 x를 무조건 거쳐야 한다면, **a node x dominates a node y**

*properties*
- $X \to X$ : 자기 자신을 dominate함
- $X \to Y \to Z \Rightarrow X \to Z$ :
- $X \to Y ~\And~ Y \to Z \Rightarrow X \to Y | Y \to X$

### Dominator Analysis
- 실행할 순서를 정하기 위해 중요함

##### Initalization
- 모든 블록을 dom으로 가정
##### Iterative computation
```c
while change, do
	change = false
	for each BB (except the entry BB)
		// 나를 항상 dom 하기 때문에 BB
		// predecessor들의 dom을 교집합 
		tmp(BB) = BB + {intersect of Dom of all predecessor BB's}
		if (tmp(BB) != dom(BB))
			dom(BB) = tmp(BB)
			change = true
```

1. Initialization
	- Dom(entry) = entry 
	- Dom(everything else) = all nodes
2. Iterative
	- Entry node를 제외한 모든 노드에 대해 
		- tmp(BB) = BB(자기자신) + {모든 predecessor Dom의 교집합}
	- 변화가 없을 때까지 반복.
		- 모든 블록을 한번 실행했다 하더라도 back link 때문에 한번 더 해 봐야함.

**주의**
- loop를 만들기 위해 존재하는 back edge  주의

- `change`를 flag로 쓰고 while loop
- 마지막 블록까지 한번 실행했다 하더라도 back link 때문에 한번 더 해 봐야함
- predecessor, producer <-> successor, consumer 

#### Immediate Dominator

- 나에게 가장 가까운 dominator

#### Dominator Tree

- parent node만 따라가면 dominator set을 얻을 수 있음 -> immediate node만 저장하면 됨

#### Post Dominator (PDOM)

- Reverse of dominator


#### Dominator 

- dependency를 파악하는게 중요
- **loop detection** : 코드 최적화를 위해 먼저 실행되는 블록이 중요함. dependency 파악 등

## Loop analysis

### Natural Loops

**Properties**

1. single entry point called the ***header*** - 걍 loop 시작
	- Header *dominates* all block in the loop
2.  ***backedge*** : loop을 이루기 위해 header로 되돌아가는 edge가 최소 1개 있어야 함

### Loop Detection

-  소스 블록의 **타겟**이 전에 실행된 블록인가 -> back edge

c.f. loop = loop header + loop body
loop body의 모든 basic dominator는 loop header

example in 13page
loop은 2개
nest level 2개
B

**Loop 구성**
1. Header, LoopBB : 모든 loop body의 dominate = loop body의 모든 BB는 header를 거침
2. Backedge, BackedgeBB: 
3. Exitedge, ExitBB: outgoing edge. Loop 밖 BB로 향하는 edge
4. Preheader, Preloop: 
	- 헤더 위 **새로운** 블록 - profiling을 위해 새로 추가됨
		- trip count (loop iteration) 측정 = # of header invocation / # of preheader invocation
	- 루프 실행할 때 프리헤더 실행됨
	- 루프 iteration에서 프리헤더는 실행되지 않음
	- 프리헤더의 모든 edge는 loop header로 감.


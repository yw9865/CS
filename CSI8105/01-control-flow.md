# Control Flow
#Resource #CS #csi8105 #compiler #Resource/CS 

## Control Flow Graph

Donimate analysis
- loop을 만드는 back edge는 생각하기

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
- `change`를 flag로 쓰고 while loop
- 마지막 블록까지 한번 실행했다 하더라도 back link 때문에 한번 더 해 봐야함
- predecessor, producer <-> successor, consumer 

#### Immediate Dominator
- 나에게 가장 가까운 dominator

#### Dominator Tree
- parent node만 따라가면 dominator set을 얻을 수 있음 -> immediate node만 저장하면 됨


#### Dominator 
- dependency를 파악하는게 중요
- **loop detection** : 코드 최적화를 위해 먼저 실행되는 블록이 중요함. dependency 파악 등

Dominator
- execute before
- 

Post dominator
- execute after

### Natural Loops

**Properties**
1. singole entry point called the ***header***
	- Header *dominates* all block in the loop
2. 

loop detection: 소스 블록의 타겟이 전에 실행된 블록인가만 보면 됨

c.f. loop = loop header + loop body
loop body의 모든 basic dominator는 loop header

example in 13page
loop은 2개
nest level 2개
B
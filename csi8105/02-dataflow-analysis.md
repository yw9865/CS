# Dataflow Analysis



data analysis introduction
reaching defins = OR

available defns = AND


## Live Variable (Liveness) Analysis
데이터의 생성과 소멸을 판단하여
1. ㅇㄹㅇㄹ
2. 레지스터를 최소한으로 사용


liveness를 확인할 때 프로그램 밑에서부터 분석하면서 
- 가장 마지막으로 사용된 순간 (소멸)
- 가장 처음에 선언된 순간 (생성)

밑에서부터 보는 것은 Instruction도 destination 레지스터 -> source 레지스터 순으로 봄
e.g. ADD r3, r1, r2 -> r3, (r1,r2) 순으로 봄 

- GEN: BB 안에서 생긴 변수 집합
- KILL: BB가 죽이는 변수 집합
	- KILL은 BB안에서 정의되는 변수 집합과 같음
- IN: BB의 entry point에서 들어오는 변수 집합
	- `IN = (GEN + (OUT - KILL))` : definition을 찾지 못한 변수
- OUT: BB의 exit point에서 나가는 변수 집합
	- 다른 BB에서 원래 있던 변수를 사용한다면 이 BB의 IN인 거고 다른 어떤 BB에서 OUT에 속해있을 것
	- `OUT = Union(IN(succs))`
### Computing GEN/KILL Sets

Algorithm

```
for each BB X, do
	GEN(X) = 0
	KILL(X) = 0
	
```

1. 각 BB 안에서 reverse sequential order로 각 operator를 분석
2. Operator의 destination들을 분석하면서
	- `GEN(X) -= dest` - 
	- `KILL(X) += dest` - 
3. Operator의 source operand들을 분석하면서
	- `GEN(X) += src`
	- `KILL(X) -= src` 



![[Pasted image 20231026104320.png]]

GEN(BB3): r1 = 
KILL(BB3): r3, r7, r2 = 새로 이용한, 재정의한 변수
=> BB4에서 쓰이는 r3, r7은 BB3에서 kill되기 때문에 BB1까지 신경쓰지 않고 BB3만 보면 된다.

 
## Reaching Definition Analysis 
(시험엔 안나옴)
- Use는 전혀 상관 안하고 definition이 어디까지 도달하는지만 봄
- c.f. liveness는 가장 마지막 use와 def를 봄
- kill될 때까지 봄 = 재정의될 때까지 봄
- 레지스터 수가 적은 시스템에서는 liveness가 훨씬 효율적이지만 reachinng definition은 편하게 찾을 수 있다.
- **Forward dataflow analysis**를 하기 때문에 훨씬 빠름


## DU/UD Chains

DU: Def-Use chain
UD: Use-Def chain


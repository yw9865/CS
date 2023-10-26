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
	- GEN, KILL은 BB안에서 정의되는 변수 집합과 같음
- IN: BB의 entry point에서 들어오는 변수 집합
- OUT: BB의 exit point에서 나가는 변수 집합
	- 다른 BB에서 원래 있던 변수를 사용한다면 이 BB의 IN인 거고 다른 어떤 BB에서 OUT으로 


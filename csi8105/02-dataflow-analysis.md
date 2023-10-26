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

- GEN: BB 안에서 새로운 정보가 생김
- KILL: BB에 의해 죽는 데이터의 집합
- IN: BB의 entry point에서 들어오는 변수 집합
- OUT: BB의 exit point에서 


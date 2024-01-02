# Parallelism
#multicore #parallelism #resource #resource/CS 


1. Task parallelism: executing different task
2. Data parallelism: perform same task, different data items at the same time
3. Stream parallelism: video처럼 스트림으로 처리되는 작업들. split - join.
	- producer와 consumer가 존재

e.g.
1. task parallelism: 한쪽에서 토마토를 준비하고 다른 쪽에서 양상추를 준비한다
2. data parallelism: 두 명이 토마토를 절반씩 나눠서 준비한다.


1. Task parallelism: 스레드로 나눔
2. Data parallelism: SIMD vectorization
3. Stream parallelism: Actor code


### **Concurrency pattern - Pipeline**
- large, identical data collection이 처리됨
- 데이터는 N개의 연속적이고(consecutive) 독립적인(independent) 일(stage)로 나뉠 수 있어야 함.
=> 각 코어가 한 stage를 일함

- 스테이지 사이에 복사하는 과정이 필요 없음
	- shared memory에 데이터를 넣고 그 안에서 modification이 순차적으로 일어나기 때문
- cache-coherence 오버헤드는 데이터 크기에 따라 결정됨
	- 결코 무시할 수 없음!
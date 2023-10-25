# Parallelism
#multicore #parallelism #Resource #Resource/CS 


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
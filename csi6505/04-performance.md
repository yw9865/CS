# 04. Performance of Parallel Programs
#resource #resource/CS #thread #multicore #performance 

## The fundamental laws of parallelism

Limits to Performace Scalability
- 모든 프로그램이 "embarrassingly" 병렬적이지 않다.
- 프로그램에는 sequential part와 parallel part가 존재
	e.g.)
	1. Sequential part 1: read the input data from file
	2. Parallel part: perform the grayscale conversion
	3. Sequential part 2: write the converted data to the output file

### Amdahl's Law & Gustafson-Baris' Law

![[Pasted image 20231108141802.png]]



### Sacalability


## Source of Performance Loss

### False sharing case study

Thread-private data

Padding


Thread local variable and only add partial sum to global total_sum variable
- 스레드끼리 공유되는 변수는 메모리(캐시)에 존재. 
- 그에 비해 sequential program은 register에 total_sum이 존재해서 빠름. (LD, STORE가 없음)
- 심지어 이러면 gcc가 loop를 보고 SIMD 벡터 사용 최적화도 할 수 있음.


**Overhead ot Thread Creation**
- 처리해야할 데이터의 양이 많지 않다면 thread creation overhead가 더 클 수 있다.
- thread creation overhead는 만드는 스레드 개수가 많을수록 당연히 커진다.


### The 4 sources of performance loss
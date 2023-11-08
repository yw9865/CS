# 04. Performance of Parallel Programs
#Resource #Resource/CS #thread #multicore #performance 

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

### The 4 sources of performance loss
# 2.5 Exceptions in Java
#Resource #Resource/CS #java #exception #csi4104 #compiler #exception-handling

- 프로그램에 error가 날 때마다 프로그램이 꺼지게 둘 순 없다.
- 사전에 막아도 예상치 못한 error가 발생할 수 있다.
- 오늘날 프로그래밍 언어는 exception handling을 지원한다.


## JAVA
- java에선 모든게 object
- exception 역시 object.
- exception이 발생하면 **exception object**가 생성됨.
- error handler로 control flow가 넘어감


### e.g. Arithmetic Exception
![[Pasted image 20231017161445.png]]


## General Format
![[Pasted image 20231017161941.png]]
`try`
- exception이 예상되는 block

`catch`
- `try`문에서 발생한 exception에 따라 실행할 block

`finally`
- **exception의 발생 여부와 관계없이** 실행됨.
- 심지어 `try`나 `catch` block에서 `return`이 실행되어도 `finally`문이 실행됨o

### Throwing of Exceptions Programmatically
```java
if (currentToken.kind != Token.the)
	throw (new SyntaxError("Article expected!"));
```
- `throw`를 사용하여 **programmatically** 하게 exception을 throw/raise할 수 있다.
	- JVM이 `SyntaxError` exception object를 instantiate하고 throw함.

#### Propagation of Exceptions
![[Pasted image 20231017162847.png]]
- method가 catch-block이 없는 경우,
	- method가 종료됨
	- exception은 caller에게 propagated back됨
	- caller도 없는 경우 caller가 종료되고 caller의 caller에게 exception 전달됨
-  global scope에서 `catch` block이 없으면 프로그램이 종료됨

## Exception Class Hierarchy
![[Pasted image 20231017163508.png]]
- Java method는 일어나는 exception을 리스트화해야한다.
- exception은 method의 `throws` clause에 리스트화된다.
- `Error`와 `RuntimeException` class는 **unchecked exception**이 될 수 있고 이는 리스트화되지 않는다
	- not verified by the compiler

## Exception이 없다면
```c
int foo(FILE *f) {
	if (!f) return -1;
	return value;
}
```
- C처럼 error handling code를 중간에 끼워 넣어야한다.
	- 지저분해보임
	- 잊기 쉬움
 ![[Pasted image 20231017164804.png]]
- RD parser에서 유용하다
	- recursive하게 얽히는 RD parser에서 매 method마다 if condition으로 error handling을 하지 않아도 된다.
	- `try-catch`를 사용하면 `catch`가 나올 때까지 backpropagate되기 때문에 편하다.
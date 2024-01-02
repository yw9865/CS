# 3. Semantic Analysis
#resource/CS #resource #compiler #CFG #semantic #csi4104 #AST

## Overview


**Syntax vs. Semantics**

`101 + 1`이 갖는 semantics
- syntactically correct
- But meaning
	- In binary: `101 + 1 = 110`
	- In decimal: `101 + 1 = 102`
	- As strings: `"101" + "1" = "1011"`

`+` opeator
- Addition
- String concatenation

**Context-sensitive Information**

- variable, method, array, class or package 판별
- 변수 `x`가 사용 전에 선언 되었는지 여부
- 변수 `x`의 definition이 2개일 때, 어떤 definition을 따라야할 지 (identification)
- Type-consistent: type이 같은 변수끼리 연산해야 한다
- 배열의 dimension이 선언과 사용이 맞는지
- 배열의 index사 bound를 넘지 않았는지.
- 함수의 parameter에 맞게 argument를 넘겼는지. (개수, type)
- `break` 혹은 `continue` 에서 어떤 condition 혹은 loop block 기준인지

이러한 context-sensitive한 정보들은 CFGs로 특정지을 수 없다.
Semantics는 static, dynamic으로 분석된다.

- static : compile-time
- dynamic : run-time

### Static semantics


### Dynamic semantics



## Static semantics


### Semantic constraints


#### Scope rules



#### Type rules





### Two subphases in static semantics analysis



#### Identification (scope rules)


#### Type checking (type rules)



### Standard environments




## Attribute Grammars


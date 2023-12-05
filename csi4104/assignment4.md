# Assignment 4


## MiniC Semantic Analysis

### 1. Identification

- scope stack has already been implemented
- Use scope stack to associate identifier with their declaration.

#### Scope stack classes

1. `MiniC.SemanticAnalysis.IdEntry.java` : **internal data structure** for stack entries of the scope stack
2. `MiniC.SemanticAnalysis,ScopeStack.java` : **define all methods** that the scope stack supports

Scope stack TODO

`enter()`

- semantic analyzer가 declaration AST (`FunDecl`, `VarDecl`, `FormalParamDecl` ASTs)를 들릴 때마다 `enter()`
- 한 블록 안에 중복된 identifier가 있으면 안되므로 `enter()`시 identifier가 있으면 `false` 반환
	- parameter와도 중복검사 해야함.

`retrieve(I)`

- SemAna이 applied occurrence of an identifier I를 방문할 때마다,  identifier I의 선언을 scope stack에서 반환
	- Scope stack은 I declaration에 해당하는 AST subtree pointer 반환
	- 이 포인터는 Identifier I를 위한 ID node와 저장됨.
- declaration이 없으면 `nullptr` 반환



### 2. Type checking
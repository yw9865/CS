#Resource/CS #Resource #compiler #CFG #syntax #csi4104 

# Syntax and Semantics of Programming Languages


# Specifying the Syntax of a programming language

## CFGs, BNF and EBNF
### Context-Free Grammar
 - Terminal과 non-terminal로 나뉨
 - non-terminal은 derivation이 가능

#### Conventions
**Start symbols**: $S$. 첫 production에서 left-hand side에 쓰임

**Nonterminals**: 한 글자 대문자로 쓰거나 소문자 이름. A, B, sentence, expr


**Terminals**: 굵은 이름, 혹은 digits, operators, punctuation characters. **ID**, **INTLITERAL** 1, +, \[ 
—-
###


## Grammar transformations

#### Left factorization
$$N::=XY|XZ \Rightarrow N::=X(Y|Z)$$


#### Elimination of Left Recursion

$$N::=X|NY \Rightarrow N::=X~Y^*$$

#### Substitution of non-terminal symbols

$$N::= X \Rightarrow N::=X$$
$$M::=\alpha~N~\beta \Rightarrow M:== \alpha~X~\beta$$


# Parsing

#### Parse Trees
- internal node는 모두 non terminal
- leaves는 모두 terminal
- Tree는 bottom-up으로 읽음.


#### Ambiguous Grammars
- operator precedence
- Operator associativity

- 한 sentence에서 여러 개의 parse tree가 나타날 수 있다.
- 이떄 parse tree 간 의미하는 바가 달라질 수 있다.
	- e.g) $(a*b)+c$ or $a *(b +c)$
- operation 간 순서를 강제해서 해결
	- e.g.) expr ::= term | expr add_op term
	- Term ::= factor | term mult_op factor
	- Factor ::== id | number | - factor | ( expr )
	- tree는 왼쪽으로만 자라날 수 있어 left-to-right associativeity 성립

**Ambiguous if-then-else**
- if-then-else도 ambiguity가 있다.
- 가장 가까운 if에 else가 걸린다.

#### Terminologies
Recognition
- parse하기 전에 먼저 해야할 것은 해당 input이 우리 언어의 syntax로 이루어졌는지를 판단해야한다.
- recognizer uses a CFG to check the syntax of the program.
Parsing
1. Recognize the input program
2. Determine the phrase structure of the input program (e.g. by generating AST data structures)

#### Context-Free Grammer Classes
- Top-down LL parser: LL grammar = Left-to-right scanning of input, Left-most derivation
- Bottom-up parsers for LR grammars = Left-to-right scanning of input, Right-most derivation
![[Pasted image 20231012150313.png]]
$$LL \subset LR$$
- 모든 grammar in LL은 LR로 parsing 가능하지만 어떤 grammar in LR은 LL로 parsing되지 않는다. 

## Top-down parsing (LL)
- LL grammar는 linear임. + parser construction과 straight forward하게 맞음

- production이 common prefix를 공유할 수 있음
	- 이때는 2개의 토큰을 봐야할 수도 있음.
	- 혹은 factor out the common prefix

## Recursive descent (LL) parser construction
#### Recursive Descent Parsing
**Key Idea**
- parser tree structure <-> caller-callee relationship of parsing procedures that call each other at runtime

*example*
![[Pasted image 20231012152017.png]]
- 5개의 non-terminal -> 5개의 parsing routine
- 

## LL Grammars


# AST Construction


## Parse trees vs. ASTs


# Chomsky’s Hierarchy
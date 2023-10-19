# 2. Syntax Analysis 
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
```java
public class MicroEnglishParser {
	private Token currentToken;

	private void accept(Token expectedToken) {
		if (currentToken == expectedToken)
			currentToken = scanner.scan();
		else
			report a syntax error
	}

	private void parserSentence() {
		parseSubject();
		parseVerb();
		parseObject();
		accept('.');
	}

	private void parseSubject() {
		if (currentToken == 'I')
			accept('I');
		else if (currentToken == 'a') {
			accept('a');
			parseNoun();
		}
		...	
	}
}
```



### **Systematic Development of a RD parser**
1. EBNF로 grammar 나타내기
2. Grammar Transformations
	- Left factorization and left recursion elimination
3. Parser class 생성
	- private variable `currentToken`
	- methods to call the `scanner::accept` and `acceptIt`
4. EBNF를 RD parser로 바꾸기

private parsing methods 구현:
- 각 nonterminal마다 `private parseN` 만들기
- `public parse` method 구현
	1.  첫 번째 token을 scanner에서 받음
	2. call `parseS`. S는 start symbol
	3. `parseS()`가 return하는 모든 token이 comsume되는 걸 보장해야함. 

Error reporter를 try-catch를 이용해 만들 수 있다.

## LL Grammars
- **L**eft-to-right parsing 
- **L**eftmost derivation
- **1** token look-ahead in the input stream

**LL(1) Properties**
- NOT containing left-recursion
- NOT containing common prefixes
- NOT ambiguous grammar



p.72에서 infinite recursion이 되는 부분이 중요함

p.73

p.74

#### Generality

e.g.
$$\set{a^n0b^n |n\ge1}~\cup~\set{a^n1b^{2n}|n\ge1}$$
$S::=aZb ~|~ aObb$
$Z::=aZb ~|~ 0$
$O::=aObb~|~1$

#### FIRST
- 

e.g.
X::= Y1Y2Y3 ... Yk
First(X) = ... 
	FIrst(Y1) - \epslion  (Rule 3 (a))
	if y1 ->* \epsilon then X::=Y2Y3...Yk, First(X) = {First(y1)-\epsilon}
	if y2 ->* epsilon then  X::=Y3...Yk, First(X) = {first(y1)-epsilon, first(y2)-epsilon}
	...
	if y1,y2,y3,...,yk ->* \epsilon then First(X) = {\epsilon}

# AST Construction


## Parse trees vs. ASTs


# Chomsky’s Hierarchy
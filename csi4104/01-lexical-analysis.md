# Lexical Analysis
#resource #csi4104 #CS #resource/CS #compiler 
#### Taxanomy

* Token: maps stream of characters into words
	* Token types:
	* Identifiers
	* Keywords
	* Operators
	* Separators
	* Literals
* Lexeme
* Pattern: set of lexemes


#### Regular Expression

> A RE $r$ is a **pattern** that describes a set of lexemems $L(r)$. Lexemes are made from characters of a finite set $\Sigma$ called **alphabet**

There are three operations
1. $x | y$ : _alternation_
2. $xy$ : _concatenation_
3. $x^*$ : _repetition_ or _closure_

##### Definition of RegEx (inductive)

**Base case**
* A null character $\epsilon$ is RE

**Inductive case**
* A single character is RE
* A pattern is also RE
* $x|y$ is RE
* $xy$ is RE
* $x^*$ is RE

# Recursive Descent Parsing
There are two basic approaches to parsing programs using context free grammars - top down and bottom up.

The top down approach, which we discuss here, proceeds by scanning the string to be parsed from left to right
and constructing the parse tree using a top-down left-to-right depth first search traveral of the tree. This
corresponds to a leftmost derivation of the string and at each derivation step we need to pick which of the
rules for the left most nonterminal we should use.

The bottom up approach corresponds to rightmost derivation of the string and builds the tree from the bottom up.
This is similar to performing a postfix traversal of the parse tree.  

The algorithm for recursive descent parsing is straightforward. 

We generate a leftmost derivation $S_0,\ldots,S_n$ of the string $\omega$ as follows:
* let $S_0$ be the sentential form consisting of the start symbol $S$
* in the $i$ th step, let $k$ be the position of the first non-terminal $N$ in the sentential form $S_i$
  * the first $k-1$ characters in $S_i$ must match the first $k-1$ characters of $\omega$. Let $c=\omega[k]$ be the next character.
  * **use $c$ to determine which of the grammar rules for $N$ should be used next** (We'll talk about how to do this next!)
  * replace $N$ with the left hand side of the chosen grammar rule and extend the parse tree by adding the LHS of the rule as the children of $N$
* continue until $S_n = \omega$

The key point that makes recursive descent parsing work is if we can determine which production to use next by looking at the next character to be parsed.
The simplest kind of grammar to parse is one where each production for a given non-terminal starts with a different terminal! Let's look at an example from the book.

```
S -> if E then S else S
S -> begin S L
S -> print E
L -> end
L -> ; S L
E -> num
```
Here 
* the terminals are $\\{if, then, else, begin, print, end, num, ';', '-'\\}$ and
* the nonterminals are $\\{S,L,E\\}$

Let's see how to parse a string, say
```
if num then begin print num ; print num end else print num
```
The start symbol is S and the first token is "if" so we know to use the first production
```
S -> if E then S else S
```
next we need to expand parse an E and there is only one rule so we get
```
S -> if num then S else S
```
and now we need to parse an S, and the next token is 'begin' so we use the 2nd rule
```
S -> if num then begin S L else S
```
and so on... at each point we look at the leftmost non-terminal and the next token.
Since each rule for each non-terminal starts with a terminal and those terminals are all different,
we know which rule to use!  We could also build up a parse tree as we go along.
[Here is some code to create the parse tree in Python](./demo1.py)
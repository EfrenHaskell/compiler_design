# Liveness Analysis for Flow Graphs

One of the most important kinds of analysis that a compiler needs to perform is determining, at each point in a program
which variables are live and which are dead.  

Let A = B OP C be an instruction, then we say that A is defined by that instruction and B and C are used.

A variable is (statically) live at a point, if there is a path from that point to a use of that variable, 
which doesn't pass through a definition of that variable, in other words, the value of that variable at that point
might be needed by a later instruction.  

If there are no such paths, then we say the variable is (statically) dead, that is, if every path from that point
eventually reaches the end of the program or reaches an instruction that redefines the variable.

We've already seen how to do liveness analysis for basic blocks. For flowgraphs we need a more sophisticated approach.

The idea is to try to determine at the beginning of each block the set of variables which are live.

Let L(B) be the set of variables which are live at the end of block B. We have seen how to use that information
to find the set T(L(B)) of variables which are live at the start of block B.

We start by assuming that L(B) is empty for each block and we will define a set of equations that propogate the uses of each variable throught the flow graph. When the equations stabilize we have a solution to our liveness analysis.

So for each block B we iterate the following equations and keep doing this until there are no changes

$L(B) = \bigcup_{C\in S(B)} T(L(C))$

where $S(B)$ is the set of all blocks that directly follow block $B$.

We can define T(V) as follows:

$T(V) = V \cup use(B) - def(B)$

where 
* $use(B)$ are the variables in B that are used in B before being defined in D and
* $def(B)$ are the variables in B that are defined before being used

Let's try this with a simple example program, where I have introduced labels for each basic block
whether or not there is a jump to it.
```
B0:
  m=0
  v=0
  if v>n goto L4
B1:
  r=v
  s=0
  if r<n goto B3
B2:
  v=v+1
  goto B1
B3:
  x=M[r]
  s=s+x
  if s<=m goto B5
B4: 
  m=s
B5: 
  r=r+1
  goto B1
```





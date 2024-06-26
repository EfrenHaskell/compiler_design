# Basic Blocks
In order to generate efficient code, it is helpful to break the program into maximally large segments which must always be 
evaluated sequentially, such a segment of three address code is called a basic block. 

A **basic block** is a subsequence of the three address code for a program in which there are no jumps (conditional or absolute) except possibly
the last instruction, and there are no labels except possibly for the first instruction. So any time the processor starts executing
the first instruction it will run all of the instruction in order until the last instruction.

## Example 
Consider the following example from the suggested textbook Aho,Sethi,Ullman, ... Example 8.6.
The following code initializes a 10x10 array to have zeroes everywhere except having ones on the diagonal. We assume that a 2 dimensional array a[i][j] is compiled into a 1 dimensional array where
```
a[i][j] = A[N*i + j]
```
where N is the width of each row of the array.

This code initializes the array to be the identity matrix:
``` java
for (int i=0; i<10; i++)
  for (int j=0; j<10; j++)
    a[i][j] = 0;  // A[10*i+j] = 0
for (int i=0; i<10; i++)
  a[i][i]=1;      // A[10*i+j] = 1
```
We can convert this into the following three address code
which decomposes into 5 basic blocks...

```
  i = 0                # BB0

L1:                    # BB1
  j = 0

L2:                    # BB2
  t1 = 10 * i
  t2 = t1 + j
  a[t2] = 0
  j = j + 1
  if j <= 10 goto L2:

  i = i + 1            # BB3
  if i <= 10 goto L1:

  i = 0                # BB4

L3:                    # BB5
  t5 = 10 * i
  t6 = t5 + i
  a[t6] = 1
  i = i + 1
  if i <= 10 goto L3:  
```
We can represent this program as a **flow graph**, that is directed graph where the nodes are the basic blocks
and there is an edge from one node to another if control can pass directly between those two
blocks. Here is the flow graph for this program

![Flow Graph Example](bb.jpg)

or as a transition table using the [graphviz](https://graphviz.org) dot notation:
```
# bb.dot 
# compile with dot -Tjpg -o bb.jpg bb.dot
# using the graphviz app https://graphviz.org/
digraph dfa {
BB0 -> BB1
BB1 -> BB2
BB2 -> BB2 
BB2 -> BB3
BB3 -> BB1 
BB3 -> BB4
BB4 -> BB5
BB5 -> BB5
}
```


# Loops
Loops are very important in compiler optimizations as the they are often the instructions in a program
that require the most total time during execution. Hence, it is important to be able to identify the loops
of a program. Here is formal definition of the loops of a flow diagram.

A subset L of a flow graph is a loop if 
1. it contains a node e (called an entry node) with the property that every path through the flow graph
   that passes through any node f in L, must first pass through e.
2. every node f in L has a path, entirely within L to e

The loops in our flowgraph above are
* BB2
* BB5
* BB1 BB2 BB3


## Practice
Consider the following simple program
```
u=0;
d=0;
t=0;
while (n > 1) {
  if (n%2==0) {
    n = n/2;
    d = d+1;
    t = t+1
  else {
    n = 3*n+1;
    u = u+1
    t = t+1
  }
}
```


Here is the compilation to three address code:
```
U=0
D=0
T=0
L1:
  if-false n>1 goto(L2)
  T1 = N % 2
  if-false T1==0 goto(L3)
  N = N / 2
  D = D + 1
  T = T + 1
  JUMP L1
L3:
  T2 = 3*N
  N = T2 + 1
  U = U + 1
  T = T + 1
  JUMP L1
L2:
```

1. identify the basic blocks and give them sequential numbers BB0 BB1 BB2 ...
2. draw the flow graph of the program and represented it as a graphviz dot file

What are the loops in this flowgraph?

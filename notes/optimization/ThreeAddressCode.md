# Three Address Code
A very common Intermediate representation for programs is called three address code.
This converts an abstract syntax tree into a sequence of labels and instructions
where each instruction has one of the following forms:
```
# arithmetic expressions
X = Y op Z  op is +,-,*,/,%,//,&&,||, ...
X = uop Z   uop is -,!,...

# conditional and unconditional jumps
if X op Y goto(L)  op is <,<=,==,>,>=,!=
jump L

# array operations
X[Y] = Z
X = Y[Z]
X = int[Y]   # create an array of size Y

# 2d integer arrays are handled as 1 dimension arrays with the rows stacked together
X[A,B] = X[A][B] = X'[N*A+B]  if X in int[N][M] where X' in int[N*M]
```
We can convert arithmetic expressions into 3-address code 
by introducing temporary variables for each non-leaf node in the Abstract Syntax Tree. 
We will use T1,T2,... to represent temporaries.

### Example of compiling arithmetic expressions to 3 address code
For example, 
```
c = (a-b)*(a+b)
```
could be translated into 3 address code as follows (where we indicate registers by upper case letters)
```
T1 = A-B
T2 = A+B
C = T1*T2
```
### Practice
Try converting the following to three address code:
```
x = (-b + sqrt(b*b - 4*a*c))/(2*a)
```
assuming there is a ```SQRT X``` machine instruction

## Example of Compiling control structures to 3 address code
Likewise, we can convert control structures like if, while, switch, into three address code with labels,
where we use L1, L2, L3, ... for labels.

For example,
```
  if (x%2==0){
    x = x/2;
  } else {
    x = 3*x+1;
  }
```
could be converted as follows:
```
  T1 = X % 2
  if T != 0 goto(L1)
  X = X / 2
  jump L2
L1:
  T2 = 3 * X
  X = T2 + 1
L2:
```
### Practice
Convert the following to 3 address code:
```
while(i*i < n){
   s = s + i*i;
   i = i + 1;
}
```
We'll look at optimizing this code a little!

Try converting some simple program code to three address code, e.g.
```
for(int i=1; i<41; i++){
  a[i] = i*i+i+41;  // assume integers take 4 bytes, so a[i]=j would compile to A[4*I]=J
}
```
Also, try this one:
```
for (int i=0; i<10; i++){
  for (int j=0; j<10; j++){
     a[i][j] = i*j;  // a[i][j] represented as A[4*(10*I+J)]
  }
}
```


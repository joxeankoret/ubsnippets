# What is this repository for?

This is a set of Undefined Behaviour C/C++ Snippets. It's, for now, a chaotic and really incomplete set of C/C++ snippets showing undefined behaviour without proper classification or explanations. Feel free to send pull requests making it more understandable or simply adding more.

# Undefined Behaviour Snippets

## Division By Zero

```
int x = 1;
return x / 0; // undefined behavior
```

## Out-of-bounds operations

Simple example 1:

```
int arr[4] = {0, 1, 2, 3};
int *p = arr + 5;  // undefined behavior
```

Computing an out-of-bounds pointer is UB, even without dereferencing it:

```
short a[10];
short *p = &a[15];  // Bleep
```

## Unrelated objects

Comparing pointers from unrelated objects is UB in C:

```
long *p = malloc(size(long));
long *q = malloc(size(long));
if (p > q) ...  // Ouch
```

## Modifying an object between two sequence points

Modifying an object between two sequence points more than once produces undefined behavior. It is worth mentioning that there are considerable changes in what causes undefined behavior in relation to sequence points as of C++11. The following example will however cause undefined behavior in both C++ and C.

Example 1:
```
i = i++ + 1; // undefined behavior
```

Example 2:
```
a[i] = i++; // undefined behavior
printf("%d %d\n", ++n, power(2, n)); // also undefined behavior
```

## Arithmetic overflows

Simple example:
```
#include <limits.h>
#include <stdio.h>

int main (void)
{
  printf ("%d\n", (INT_MAX+1) < 0);
  return 0;
}
```

Real world example:
```
int64_t V = IV->getSExtValue();
if (V >= 0)
  Record.push_back(V << 1);
else
  Record.push_back((-V << 1) | 1);  <<----- bad line

```

The previous code causes this error:
```
UNDEFINED at <BitcodeWriter.cpp, (740:29)> :
Operator: -
Reason: Signed Subtraction Overflow
left (int64): 0
right (int64): -9223372036854775808
```

In all modern C/C++ variants running on two’s complement machines, negating an int whose value is INT_MIN (or in this case, INT64_MIN) is undefined behavior. The fix is to add an explicit check for this case.

## Uninitialized variables

Reading an uninitialized local variable is usually UB:
```
int x;
printf("%d", x);  // UB happens here
```

## Shifting more than the integer width or less than zero

Shifting more than the integer width or less than zero is UB:
```
uint32_t x = 0;
x = x << 33;  // Blamo
x = x >> (-1);  // Kapow
```

## Null pointers

Suppose we have a piece of code like this:

```
void f(int *p) {
    printf("%d", *p);
    if (p != NULL)
        printf("OK");
}
```

Then the compiler is permitted to substitute this equivalent code with the null check removed:

```
void f(int *p) {
    printf("%d", *p);
    printf("OK");
}
```

Why? Suppose p is not NULL. Then the first print will be fine, and the second print will get executed. Now suppose p is 
NULL. Then the first print triggers UB, so everything thereafter is meaningless. For convenience we can make this behave the same as the first case, where both prints get executed.

# References

 * [Wikipedia](https://en.wikipedia.org/wiki/Undefined_behavior)
 * [A Guide to Undefined Behavior in C and C++, Part 1](http://blog.regehr.org/archives/213) by John Regehr.
 * [Undefined behavior in C and C++ programs](https://www.nayuki.io/page/undefined-behavior-in-c-and-cplusplus-programs) from Project Nayuki.
 
 

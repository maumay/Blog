---
title: Fixed Size Integer Arithmetic
---

---

---

### Motivation

Recently I started reading the book 'Computer Systems: A Programmer's Perspective' on the advice of [Teach yourself CS](http://teachyourselfcs.com) as I am somewhat lacking in my knowledge of foundational computer science and I thought it would be a good idea to try and summarize certain topics in the book as I go along in order to cement them into my mind. In this post I will attempt to paraphrase section 2.3 on integer arithmetic.

---

---

### The Constraints

In my mind I currently think of a computer as a processor alongside multiple different 'areas of memory' between which information can be transferred. By areas of memory I mean a linear fixed size array of bits which are partitioned into contiguous groups of 4, i.e. bytes. These bytes each then have their own unique address which is represented by their index in the array. The processor takes some set of bytes as an input which indicates a predefined procedure to follow as well as the input to the procedure and executes it to change the state of the memory. A significant number of procedures that a processor can perform relate to arithmetic of numbers which are represented in binary form and importantly for us the inputs of these procedures are made up of a fixed number of bytes. In the case of \\(n \\in \\mathbb{N}\\) bytes we can only have \\(2^n\\) unique configurations of bits and thus can only represent this many unique numbers in any single perspective on the bits. It is interesting to think about what arithmetic means in this case, how it is implemented and the differences between the 'usual' case where there are no constraints on the quantity of numbers we can consider. In this post I will talk about 2 different perspectives which lets one view the set of fixed length bitstrings as integers.

---

---

### Unsigned Integers

#### Bitstrings as Integers I

The first numerical perspective on set of bits I will look at is that of 'unsigned integers', i.e. non-negative whole numbers. Let us consider the set \\[S_n = \\{ (b_i)_0^{n - 1} : b_i \\in \\{0, 1\\} \\}  \\] where \\(n \\in \\mathbb{N}\\) is a positive integer which is divisible by 4. This is the set of all bit strings of length \\(n\\) and we can see that as described above we have \\(|S_n| = 2^n\\) and thus we can represent at most \\(2^n\\) unique numbers with this set. The most straightforward way of thinking of \\(S_n\\) as a set of numbers is to associate each bit string with the number whose binary representation is that same bit string when padded by 0's so as to have length \\(n\\). I will use the 'little endian' approach here in that \\(b_0\\) is the least significant bit and \\(b_{n - 1}\\) the most significant. Some examples with \\(n = 4\\):

|  Decimal  |  Binary  |  Bitstring  |
| :---: | :---: | :---: |
| 5       | 11    | (1, 1, 0, 0) |
| 12       | 1100    | (0, 0, 1, 1) |

Let us denote 

\\[ \\mathbb{N}_n = \\{ m: m \\in \\mathbb{N}, m \\lt 2^n\\} \\]

so that the bijective mapping defined above can be written succinctly

\\[ f: S_n \\to \\mathbb{N}_n \\]

and we see it is entirely natural to consider these bit strings as numbers in this perspective, nothing clever is going on here. We have some questions unanswered though, it is useless to have a representation of numbers without being able to manipulate and combine them in order to perform computation. We need standard operators like addition and multiplication defined for \\(\\mathbb{N}_n\\) which can then be encoded as processor procedures in order for the computer to work with our first representation of numbers.

---

#### Unsigned Arithmetic

Let us first talk about addition. We would like to define an operator \\(+_n^u: \\mathbb{N}_n \\times \\mathbb{N}_n \\to \\mathbb{N}_n\\) which is "as close as possible" to the standard addition operator on \\(\\mathbb{N}\\). We can see that it is possible to sum two numbers in \\(\\mathbb{N}_n\\) to get a number larger that \\(2^n\\) and so we must modify the normal summation operator in some way. The simplest way is using modular arithmetic to write

\\[  x +_n^u y = x + y\\ (\\mathrm{mod}\\ 2^n) \\quad \\forall  x, y \\in \\mathbb{N}_n\\]

and this is indeed how addition is defined for unsigned integers. It is straightforward to see that the pair \\((\\mathbb{N}_n, +^u_n)\\) satifies the criteria of an Abelian group and so an analogous subtraction operator follows immediately. Similarly we can define multiplication 

\\[ x *_n^u y = xy\\ (\\mathrm{mod}\\ 2^n) \\quad \\forall  x,y \\in \\mathbb{N}_n\\]

Processor procedures representing these operators are implemented to allow the computer to perform calculations using natural numbers.

---

---

### Signed Integers

#### Bitstrings as Integers II

The second numerical perspective on \\(S_n\\) I will look at in this post is that of 'signed integers' i.e. a perspective that lets us handle negative numbers. The cardinality of \\(S_n\\) remains the same, so in order to handle 'extra' numbers we need to sacrifice some in the range \\(0 \\le m \\lt 2^n\\) which is supported by the unsigned perspective. Before we decide how to make the sacrifice there are a couple of things which are desirable in an integer perspective defined on \\(S_n\\)

 1. Any perspective should bijectively map a contiguous block of integers to \\(S_n\\)
 2. If the perspective supports both positive and negative numbers then there should be an equal amount of each.

Well the first condition removes any silly perspectives such as taking every other number or something similar and the second makes the contiguous block 'centred'. Actually with these conditions there are really only two blocks to consider and they are both practically the same. The first is \\(-2^{n - 1} \\le m \\lt 2^{n - 1}\\) and the second is \\(-2^{n - 1} \\lt m \\le 2^{n - 1}\\). From now on we just consider the first range, let us define 

\\[\\mathbb{Z}_n = \\{m: m\\in \\mathbb{Z}, -2^{n - 1} \\le m \\lt 2^{n - 1}\\}\\]

and so we look for a bijection 

\\[g: S_n \\to \\mathbb{Z}_n\\]

This isn't as straightforward as before and the mapping is more involved. The most common method used is called 'twos complement' and is defined as follows 

\\[g((b_i)\_{i = 0}^{n-1}) = -b\_{n - 1}2^{n - 1} + \\sum\_{i = 0}^{n - 2} b_i2^i\\]

I found this really amazing when I first came across it, and I think it's an elegant way to define the perspective. We can see that the most significant bit adds a 'weighting term' to determine the sign. 

So we can think of \\(\\mathbb{Z}_n\\) as \\(S_n\\) under our signed perspective and \\(\\mathbb{N}_n\\) as \\(S_n\\) under our unsigned perspective. We can now define the bijection between the perspectives as 

\\[h = f \\circ g^{-1}: \\mathbb{Z}_n \\to \\mathbb{N}_n \\]

which can be numerically defined by 

\\[
  h(m) = 
  \\begin{cases}
    m, & \\text{for } m \\ge 0  \\\\
    m + 2^n, & \\text{for } m \\lt 0
  \\end{cases}
\\]

and 

\\[
  h^{-1}(m) = 
  \\begin{cases}
    m - 2^n, & \\text{for } m \\ge 2^{n - 1}  \\\\
    m, & \\text{for } m \\lt 2^{n - 1}
  \\end{cases}
\\]

these functions define what happens when we cast between signed and unsigned data types in languages where both are supported such as C. The program defined below demonstrates this for \\(n = 16\\).

```c
#include <stdio.h>

main()
{
    unsigned short x = 65535; // == 2^16 - 1
    printf("%d\n", (short) x); 
    // -1

    x = 25;
    printf("%d\n", (short) x);
    // 25

    short y = -10;
    printf("%d\n", (unsigned short) y);
    // 65526 == -10 + 2^16

    y = 1243;
    printf("%d\n", (unsigned short) y);
    // 1243
}

```

---

#### Twos Complement Arithmetic

We define twos-complement addition on n bits using modular arithmetic in the same way before casting the result (which can be viewed as an unsigned integer) back to a signed integer by way of the function \\(h\\) defined above.

\\[  x +_n^t y = h^{-1}(x + y\\ (\\mathrm{mod}\\ 2^n)) \\quad \\forall  x, y \\in \\mathbb{Z}_n\\]


and we can perform some case analysis to see we have three possibilities

\\[
  x +_n^t y = 
  \\begin{cases}
    x + y - 2^n, & \\text{for } 2^{n - 1} \\le x + y  \\\\
    x + y, & \\text{for } -2^{n - 1} \\le x + y \\lt 2^{n - 1} \\\\
    x + y + 2^n, & \\text{for } x + y \\lt -2^{n - 1} 
  \\end{cases}
\\]

We see that there are now two ways of wrapping. The first case says that when we add two negative numbers to form a negative number which lies outside \\(\\mathbb{Z}_n\\) after taking the modulus and then converting back to a signed integer we end up with a positive number, this is known as negative overflow. The middle case is the 'nice' case, i.e. we can represent the summation correctly with the range available and the result is as expected. The third is the case of positive overflow, where the sum of two positive integers is too large to store and the result gets wrapped round to a negative number. 

We can see that it is possible to use exactly the same processor procedure for signed and unsigned addition by writing \\(x \\in \\mathbb{Z}_n\\) as

\\[ x = -x\_{n - 1}2^{n - 1} + \\sum\_{i = 0}^{n - 2}x_i2^i \\]

and observing 

\\[h(x) = x\_{n - 1}2^n + x\\] 

so that 

\\[ x + y\\ (\\mathrm{mod}\\ 2^n) = h(x) + h(y)\\ (\\mathrm{mod}\\ 2^n)\\]

We can therefore think of twos-complement addition to be casting to unsigned integers before applying unsigned addition and then casting the result back. Remember each unsigned/signed integer pair that \\(h\\) associates together have the same underlying representation in \\(S_n\\).


The negation of a twos-complement number is mostly straightforward as 

\\[x \\in \\mathbb{Z} - \\{-2^{n -1}\\} \\implies -x \\in \\mathbb{Z} - \\{-2^{n -1}\\} \\]

but the case of \\(-2^{n-1}\\) is special as its usual negation lies outside of \\(\\mathbb{Z}_n\\). However we observe that 

\\[-2^{n - 1} +_n^t -2^{n-1} = 0\\]

so it is its own negation.

Finally we define multiplication on signed integers as 

\\[  x *_n^t y = h^{-1}(xy\\ (\\mathrm{mod}\\ 2^n)) \\quad \\forall  x, y \\in \\mathbb{Z}_n\\]

which matches the general pattern we have been following and has the same case analysis as for signed addition in that we have both positive and negative overflow. We see in the same way as we did for addition that the same processor procedure can be used for both signed and unsigned multiplication by writing \\(x \\in \\mathbb{Z}_n\\) as

\\[ x = -x\_{n - 1}2^{n - 1} + \\sum\_{i = 0}^{n - 2}x_i2^i \\]

and observing 

\\[h(x) = x\_{n - 1}2^n + x\\] 

so that 

\\[ xy\\ (\\mathrm{mod}\\ 2^n) = h(x)h(y)\\ (\\mathrm{mod}\\ 2^n)\\]

---

---

#### Summary

We have defined operators representing addition, subtraction and multiplication on integers in the fixed ranges \\(0 \\le m \\lt 2^n\\) and \\(-2^{n - 1} \\le m \\lt 2^{n - 1}\\) which we called unsigned and signed integers respectively. We have seen how these ranges can both be mapped bijectively to a fixed length sequences of bytes (when \\(n\\) is divisible by \\(4\\)) meaning procedures can be implemented for the processor to carry out arithmetic with both. We have seen the numerical definition of the bijective mapping between the two ranges and how this corresponds to casting in languages such as C which support both data types. 

All this is neatly abstracted away from the application programmer so that these numbers and operators can be worked with in computations but one must be mindful about the limitations of this representation of integers. Performing a computation which leads to a result outside of the supported range will lead to overflow and nonsensical results. Carelessness about integer overflow has led to a great many security vulnerabilites and hard to detect bugs in various systems throughout the years.


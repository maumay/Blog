---
title: Fixed Size Integer Arithmetic
---

### Motivation

Recently I started reading the book 'Computer Systems: A Programmer's Perspective' on the advice of [Teach yourself CS](www.teachyourselfcs.com) as I am somewhat lacking in my knowledge of foundational computer science and I thought it would be a good idea to try and summarize certain topics in the book as I go along in order to cement them into my mind. Today it is the joys of integer arithmetic and how it must be handled differently in a computer to how it is handled when one is not constrained by the laws of physics.

### The Constraints

In my mind I currently think of a computer as a processor alongside multiple different 'areas of memory' between which information can be transferred. By areas of memory I mean a linear fixed size array of bits which are partitioned into contiguous groups of 4, i.e. bytes. These bytes each then have their own unique address which is represented by their index in the array. The processor takes some set of bytes as an input which indicates a predefined procedure to follow as well as the input to the procedure and executes it to change the state of the memory. A significant number of procedures that a processor can perform relate to arithmetic of numbers which are represented in binary form and importantly for us the inputs of these procedures are made up of a fixed number of bytes. In the case of \\(n \\in \\mathbb{N}\\) bytes we can only have \\(2^n\\) unique configurations of bits and thus can only represent this many unique numbers in any single perspective on the bits. It is interesting to think about what arithmetic means in this case, how it is implemented and the differences between the 'usual' case where there are no constraints on the quantity of numbers we can consider. In this post I will talk about 2 different perspectives which lets one view the set of fixed length bitstrings as integers.

### Unsigned Integers

#### Bitstrings as Integers I

The first numerical perspective on set of bits I will look at is that of 'unsigned integers', i.e. non-negative whole numbers. Let us consider the set \\[S_n = \\{ (b_i)_0^{n - 1} : b_i \\in \\{0, 1\\} \\}  \\] where \\(n \\in \\mathbb{N}\\) is a positive integer which is divisible by 4. This is the set of all bit strings of length \\(n\\) and we can see that as described above we have \\(|S_n| = 2^n\\) and thus we can represent at most \\(2^n\\) unique numbers with this set. The most straightforward way of thinking of \\(S_n\\) as a set of numbers is to associate each bit string with the number whose binary representation is that same bit string when padded by 0's so as to have length \\(n\\). I will use the 'little endian' approach here in that \\(b_0\\) is the least significant bit and \\(b_{n - 1}\\) the most significant. Some examples with \\(n = 4\\):

|  Decimal  |  Binary  |  Bitstring  |
| :---: | :---: | :---: |
| 5       | 11    | (1, 1, 0, 0) |
| 12       | 1100    | (0, 0, 1, 1) |

Let us denote 

\\[ \\mathbb{N}_n = \\{ m: m \\in \\mathbb{N}, m \\lt 2^n\\} \\]

so that the bijective mapping defined above can be written succintly 

\\[ f: S_n \\to \\mathbb{N}_n \\]

and we see it is entirely natural to consider these bit strings as numbers in this perspective, nothing clever is going on here. We have some questions unanswered though, it is useless to have a representation of numbers without being able to manipulate and combine them in order to perform computation. We need standard operators like addition and multiplication defined for \\(S_n\\) for the processor to work with them.

#### Bitstring operators

Let us first talk about addition. We would like to define an operator \\(+_n: S_n \\times S_n \\to S_n\\) which is "as close as possible" to the standard addition operator on \\(\\mathbb{N}_n\\). As a first attempt we can simply define 

\\[ s_1 +_n s_2 = f^{-1}(f(s_1) + f(s_2)) \\quad \\forall  s_1, s_2 \\in S_n\\]

We can see an immediate problem though. What if \\(f(s_1) + f(s_2) \\ge 2^n\\)? In this case we would have an issue because \\(f(s_1) + f(s_2)\\) falls outside the domain of \\(f^{-1}\\). Ok then we can make a second attempt and use modular arithmetic to define 

\\[  s_1 +_n s_2 = f^{-1}(f(s_1) + f(s_2)\\ (\\mathrm{mod}\\ n)) \\quad \\forall  s_1, s_2 \\in S_n\\]

and this is indeed how addition is defined for  \\(S_n\\) and how it is implemented at the processor level. It is straightforward to see that the pair \\((S_n, +_n)\\) satifies the criteria of an Abelian group and so an analogous subtraction operator follows immediately. Similarly to addition then we can define multiplication 

\\[ s_1 *_n s_2 = f^{-1}(f(s_1) * f(s_2)\\ (\\mathrm{mod}\\ n)) \\quad \\forall  s_1, s_2 \\in S_n\\]

and again this is how multiplication is implemented at the processor level.


### Signed Integers

#### Bitstrings as Integers II

The second numerical perspective on \\(S_n\\) I will look at in this post is that of 'signed integers' i.e. a perspective that lets us handle negative numbers too. The cardinality of \\(S_n\\) remains the same so in order to handle 'extra' numbers we need to sacrifice some in the range \\(0 \\le m \\lt 2^n\\) which is supported by the unsigned perspective. Before we decide how to make the sacrifice there are a couple of things which are desirable in an integer perspective defined on \\(S_n\\)

 1. Any perspective should bijectively map a contiguous block of integers to \\(S_n\\)
 2. If the perspective supports both positive and negative numbers then there should be an equal amount of each.

Well the first condition removes any silly perspectives such as taking every other number or something similar and the second makes the contiguous block 'centred'. Actually with these conditions then there are really only two blocks to consider and they are both practically the same. The first is \\(-2^{n - 1} \\le m \\lt 2^{n - 1}\\) and the second is \\(-2^{n - 1} \\lt m \\le 2^{n - 1}\\). From now on we just consider the first range, let us define 

\\[\\mathbb{Z}_n = \\{m: m\\in \\mathbb{Z}, -2^{n - 1} \\le m \\lt 2^{n - 1}\\}\\]

and so we look for a bijection 

\\[g: S_n \\to \\mathbb{Z}_n\\]

This isn't as straightforward as before and the mapping is more involved. The most common method used is called 'twos complement' and is defined as follows 

\\[g((b_i)\_{i = 0}^{n-1}) = -b\_{n - 1}2^{n - 1} + \\sum\_{i = 0}^{n - 2} b_i2^i\\]

I found this really amazing when I first came across it, and I think its an elegant way to define the perspective. We can see that the most significant bit adds a 'weighting term' to determine the sign. 

So we can think of \\(\\mathbb{Z}_n\\) as \\(S_n\\) under our signed perspective and \\(\\mathbb{N}_n\\) as \\(S_n\\) under our unsigned perspective. We now have the tools to define the bijection between \\(\\mathbb{Z}_n\\) and \\(S_n\\) which associates the numbers in each who have the same bitstring in \\(S^n\\). Let us define 

\\[h: \\mathbb{Z}_n \\to \\mathbb{N}_n\\]

by 

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

#### Twos Complement Arithmetic

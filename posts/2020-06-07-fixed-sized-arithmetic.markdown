---
title: Fixed Size Integer Arithmetic
---

### Motivation

Recently I started reading the book 'Computer Systems: A Programmer's Perspective' on the advice of [Teach yourself CS](www.teachyourselfcs.com) as I am somewhat lacking in my knowledge of foundational computer science and I thought it would be a good idea to try and summarize certain topics in the book as I go along in order to cement them into my mind. Today it is the joys of integer arithmetic and how it must be handled differently in a computer to how it is handled when one is not constrained by the laws of physics.

### The Constraints

In my mind I currently think of a computer as a processor alongside multiple different 'areas of memory' between which information can be transferred. By areas of memory I mean a linear fixed size array of bits which are partitioned into contiguous groups of 4, i.e. bytes. These bytes each then have their own unique address which is represented by their index in the array. The processor takes some set of bytes as an input which indicates a predefined procedure to follow as well as the input to the procedure and executes it to change the state of the memory. A significant number of procedures that a processor can perform relate to arithmetic of numbers which are represented in binary form and importantly for us the inputs of these procedures are made up of a fixed number of bytes. In the case of \\(n \\in \\mathbb{N}\\) bytes we can only have \\(2^n\\) unique configurations of bits and thus can only represent this many unique numbers in any single perspective on the bits. It is interesting to think about what arithmetic means in this case, how it is implemented and the differences between the 'usual' case where there are no constraints on the quantity of numbers we can consider.

### Natural numbers and Unsigned integers

#### Bitstrings as Integers I

The first numerical perspective on set of bits I will look at is that of 'unsigned integers', i.e. non-negative whole numbers. Let us consider the set \\[S_n = \\{ (b_i)_0^{n - 1} : b_i \\in \\{0, 1\\} \\}  \\] where \\(n \\in \\mathbb{N}\\) is a positive integer which is divisible by 4. This is the set of all bit strings of length \\(n\\) and we can see that as described above we have \\(|S_n| = 2^n\\) and thus we can represent at most \\(2^n\\) unique numbers with this set. The most straightforward way of thinking of \\(S_n\\) as a set of numbers is to associate each bit string with the number whose binary representation is that same bit string when padded by 0's so as to have length \\(n\\). I will use the 'little endian' approach here in that \\(b_0\\) is the least significant bit and \\(b_{n - 1}\\) the most significant. Some examples with \\(n = 4\\):

|  Decimal  |  Binary  |  Bitstring  |
| :---: | :---: | :---: |
| 5       | 11    | (1, 1, 0, 0) |
| 12       | 1100    | (0, 0, 1, 1) |

Let us denote \\[ \\mathbb{N}_n = \\{ m: m \\in \\mathbb{N}, m \\lt n\\} \\] so that the bijective mapping defined above can be written succintly \\[ f: S_n \\to \\mathbb{N}_n \\] and we see it is entirely natural to consider these bit strings as numbers in this perspective, nothing clever is going on. We have some big questions unanswered though, it is useless to have a representation of numbers without being able to manipulate and combine them in order to perform computation. We need
standard operaters like addition and multiplication defined too.

#### Bitstring operators I

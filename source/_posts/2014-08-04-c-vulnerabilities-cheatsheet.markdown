---
layout: post
title: "C Vulnerabilities Cheatsheet"
date: 2014-08-04 11:11:46 -0400
comments: true
categories: 
---

###String
In C, there's not native type of strings compares to Java or Python; instead, strings are formed by constructing char array with a termination character.

####Unbounded String Functions
The main problem with these functions is that they are unbounded that is, the destination buffer's size isn't taken into account when performing a data copy. This means that if the string length of the source data supplied to these functions exceeds the destination buffer's size, a buffer overflow condition could be triggered, often resulting in exploitable memory corruption.

+ `scanf()`: When the strings are specified in the format string (using the `%s` formatter), the corresponding buffer needs to be large enough.
+ `sprintf()`: If the destination buffer is not large enough to handle the input data, a buffer overflow could occur.
+ `strcpy()`: If the destination buffer can be smaller than the length of the source string, a buffer overflow could occur.
+ `strcat()`: When calling `strcat()`, the destination buffer (`dst`) must be large enough to hold the string already there, the concatenated string (`src`), plus the NUL terminator.

####Bounded String Functions
The bounded string functions were designed to give programmers a safer alternative to the functions discussed in the previous section. These functions include a parameter to designate the length (or bounds) of the destination buffer. This length parameter makes it easier to use the bounded functions securely, but they are still susceptible to misuse in more subtle ways.

+ `snprintf()`: It works differently on Windows and Unix. On Windows, if there's not enough room to fit all the data into the resulting buffer, a value of -1 is returned and NUL termination is not guaranteed. Conversely, UNIX implementations guarantee NUL termination no matter what and return the number of characters that would have been written had there been enough room.
+ `strncpy()`: The `strncpy()` function does not guarantee NUL-termination of the destination string. If the source string is larger than the destination buffer, `strncpy()` copies as many bytes as indicated by the size parameter, and then ceases copying without `NUL` terminating the buffer.
+ `strncat()`: The size argument represents how many bytes remain in the buffer so it can be confusing it indicates the amount of space left in the buffer.

####Common Manipulation Errors
The four most common errors are *unbounded string copies, off-by-one errors, null termination errors, and string truncation*.

####Unbounded String Copies
Unbounded string copies occur when data is copied from an unbounded source to a fixed length character array (for example, when reading from standard input into a fixed length buffer).

Reading data from unbounded sources creates an interesting problem for a programmer. Because it is not possible to know beforehand how many characters a user will supply, it is not possible to pre-allocate an array of sufficient length.

####Off-by-One Errors
Off-by-one errors are similar to unbounded string copies in that they both involve writing outside the bounds of an array.

####Null-Termination Errors
Static declarations for char arrays fail to storage for the null-terminaion character. As a result, misuse `strcpy()` to an array may write a null character beyond the end of the array.

####String Truncation
String truncation occurs when a destination character array is not large enough to hold the contents of a string. String truncation may occur while reading user input or copying a string and is often the result of a programmer trying to prevent a buffer overflow.

---
layout: post
title: "Reed Solomon"
date: 2019-10-26T10:20:00Z
categories: ["Erasure Coding", "Storage System"]
---
Reed-Solomon codes are a group of error-correcting codes. Now what are error correcting codes and what is their purpose?
Lot of computer systems deal with data and they need to deal with some kind of failures like:
1. **Data Corruption**
2. **Data Loss** 

Let's say you have a data set D and you partition it into n sub-parts {D1...Dn} and you want to generate k extra part {C1...Ck} such that even if k parts are lost from {D1...Dn,C1...Ck} all Data part can be retrieved. {C1...Ck} are called code block that is extra information we need to store to handle at most k failures.

For example if you have a file of size 10MB and you want to divide it into 2 parts X,Y each of size 5MB, you can generate 2 code blocks X+Y and X-Y. If we lose any 2 blocks out of {X,Y,X+Y,X-Y} we can reconstruct original dataset. That is in essence what erasure codes are for.

### Replication Erasure
This is probably the simplest erasure code. In this method given a data algorithm will simply store k more copies of the data. Problem with this approach is as you store k more copies of each piece of data disk usage increase by k fold but benefit of this approach is that calculating code blocks and decoding (recovering data in case some data got corrupted/lost) does not take any time. So this approach would ideally be used for small piece of data that is used frequently.


### Reed Solomon

Main idea of Reed-Solomon is to construct a (k+n) x n matrix such each nxn submatrix of it is invertible. Encoding process involves multiplication of given matrix with data blocks to get \\(O_1 ,..., O_{k+n} \\) (encoded blocks).

$$
  \begin{bmatrix}
    a_{11} & . & . & . & a_{1n} \\. & . & . & . & . \\. & . & . & . & . \\a_{k+n,1} & . & . & . & a_{nn}
  \end{bmatrix}
  \begin{bmatrix}
    D_1\\.\\.\\D_n
  \end{bmatrix} =
  \begin{bmatrix}
    O_1\\.\\.\\O_{k+n}
  \end{bmatrix}
$$

If we later found out that only subset of code blocks is remaining of example \\(O_2,O_3,O_5,O_8,...\\) is remaining we construct a submatrix of M by taking only 2,3,5,8... rows, lets call it N then the original data can be obtained by

$$
  N^{-1} *
  \begin{bmatrix}
    O_2 \\O_3 \\O_5 \\O_8 \\...
  \end{bmatrix}
  =
  \begin{bmatrix} D_1 \\ . \\. \\.\\D_n \end{bmatrix}
$$

#### Vandermonde Reed-Solomon
In this version of Reed Solomon we use Vandermonde matrix for encoding and decoding.

$$
  \begin{bmatrix}
    1 & a_1 & a_1^2 & ... & a_1^n \\ 
    1 & a_2 & a_2^2 & ... & a_2^n \\ 
    . & . & . & ... & . \\ 
    1 & a_n & a_n^2 & ... a_n^n \\ 
  \end{bmatrix}
$$

This matrix has a nice property that any n x n submatrix will be invertible as long as all \\(a_1 ... a_n\\) are different.
You can read more about this at [vander][vander].

**A little divergence in field theory**

Field: In simple terms field is set of number with two operations + and x. Such that if a, b belong to field then a + b and a x b also belong to field. Also for every element a there is an element b such that a + b = 0 and there is a c such that a*c = 1. You can read more about fields at [field][field.]

Galois Field: A galois field is a field of finite order. Most common galois field are \\(F(2^n) = {0, 1, ..., 2^{n-1})\\).
Addition in galois field of type F(2^n) is defined by bitwise xor. Another important thing to note is any element of Galois field F(2^n) can be expressed as a polynomial of order less than n. For example 5 = in binary 101 = 1 + x^2. So multiplication in F(2^n) can be defined by polynomial multiplication.

Another important thing about galois field F(2^n) is each element can be represented by a n x n matrix. Where ith column is element * 2^i so for example representation of 3 in F(2^3) is:

$$
  \begin{bmatrix}
    1 & 0 & 1 \\
    1 & 1 & 1 \\
    0 & 1 & 1 \\
  \end{bmatrix}
$$

You can read more about galois field at [galois][galois].

#### Cauchy Reed-Solomon

This is generally preferred reed solomon method. A cauchy matrix is defined as \\(a_{ij} = 1 / (x_i + y_j)\\). Each \\(x_i\\) and \\(y_j\\) are different from each other. All n x n submatrix of cauchy matrix is invertible.

Another nice property of cauchy matrix is attaching an identity matrix on top of cauchy matrix will also have all the property that all nxn submatrix will be invertible.

So if we define a matrix M such that

$$
  \begin{bmatrix}
    1 & 0 & ... & 0 \\
    0 & 1 & ... & 0 \\
    . & . & ... & . \\
    0 & 0 & ... & 1 \\
    c_{n+1,1} & . & ... & c_{n,n} \\
    . & . & ... & . \\
    c_{n+k,1} & 0 & ... & c_{n+k, n} \\
  \end{bmatrix}
$$

where \\(c_{ij}\\) is cauchy matrix.

by using this method we can see that first n coded blocks are same as data block so we save a lot of computations.

$$
  \begin{bmatrix}
    1 & 0 & ... & 0 \\
    0 & 1 & ... & 0 \\
    . & . & ... & . \\
    0 & 0 & ... & 1 \\
    c_{n+1,1} & . & ... & c_{n,n} \\
    . & . & ... & . \\
    c_{n+k,1} & 0 & ... & c_{n+k, n} \\
  \end{bmatrix}
  *
  \begin{bmatrix}
    D_1\\.\\.\\D_n
  \end{bmatrix} =
  \begin{bmatrix}
    D_1\\.\\D_n\\O_1\\.\\O_{k+n}
  \end{bmatrix}
$$

We can also choose all x and y in cauchy matrix \\(a_{ij} = 1 / (x_i + y_j)\\) to be element of Galois field and convert all \\(a_{ij}\\) in their matrix form as discussed earlier leading to a matrix with all ones and zeros.
As this matrix has all ones and zeros multiplication by this matrix will only involve addition that in galois field ix bitwise xor. Leading to less complexity of multiplication.

Also we can calculate the inverse of cauchy matrix by matrix augmentation in \\(n^2\\).

Another thing to note is that we can carefully choose our cauchy matrix so that expanded matrix we constructed has on average less number of one's per row leading to less addition while matrix multiplication hence faster encoding. You can read more about this [here][opt]. 

[opt]: http://web.eecs.utk.edu/~jplank/plank/papers/CS-05-569.pdf
[vander]: https://en.wikipedia.org/wiki/Vandermonde_matrix
[field]: https://en.wikipedia.org/wiki/Field_(mathematics)
[galois]: http://mathworld.wolfram.com/FiniteField.html

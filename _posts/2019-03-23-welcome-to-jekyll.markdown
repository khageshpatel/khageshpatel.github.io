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

Lets say you have a data set D and you partition it into n sub-parts {D1...Dn} and you want to generate k extra part {C1...Ck} such that even if k parts are lost from {D1...Dn,C1...Ck} all Data part can be retrived. {C1...Ck} are called code block that is extra information we need to store to handle atmost k failures.

For example if you have a file of size 10MB and you want to divide it into 2 parts X,Y each of size 5MB, you can generate 2 code blocks X+Y and X-Y. If we lose any 2 blocks out of {X,Y,X+Y,X-Y} we can reconstruct original dataset. That is in essense what erasure codes are for.

### Replication Erasure
This is probably the simplest erasure code. In this method given a data algorithm will simply store k more copies of the data. Problem with this approach is as you store k more copies of each piece of data disk usage increase by k fold but benifit of this approach is that calculating code blocks and decoding (recovering data in case some data got corrupted/lost) does not take any time. So this approach would ideally be used for small piece of data that is used frequently.


### Reed Solomon

Main idea of Reed-Solomon is to construct a (k+n) x n matrix such each nxn submatrix of it is invertible. Encoding process envolves multiplication of given matrix with data blocks to get \\(O_1 ,..., O_{k+n} \\) (encoded blocks).

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

If we later found out that only subset of code blocks is remaining of example \\(O_2,O_3,O_5,O_8,...\\) is remaining we construct a submatrix of M by taking only 2,3,5,8... rows, lets call it N then original data can be obtained by

$$
  N^{-1} *
  \begin{bmatrix}
    O_2 \\O_3 \\O_5 \\O_8 \\...
  \end{bmatrix}
  =
  \begin{bmatrix} D_1 \\ . \\. \\.\\D_n \end{bmatrix}
$$



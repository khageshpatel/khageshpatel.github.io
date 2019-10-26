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

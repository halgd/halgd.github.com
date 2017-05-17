---
layout: post
title: "longest palindromic substring"
description: "Analysis and algorithm for finding the longest palindromic substring"
keywords: "code"
categories: algorithms
---
In this post I want to guide you to develop intuition and understanding for the longest palindromic substring
*P*<sub>*i*</sub> and ∑<sub>*i*</sub>*P*<sub>*i*</sub>

### Brute force

Using two points i, j we can run down all possible substrings of a string. There would be $N^2$ of them. For each substring we iterate the substring to check if it is a palindrome. This would result in a total worst case complexity of $N^3$

### Slightly better approach

Here, we use the insight that a palindrome is symmetric around its centre (for odd length palindromes) and for even this centre is the empty space between the characters. Consider the examples,

"madam" symmetric about 'd'
"oo" symmetric about 
```python
for k in xrange(0, len(str_s)):
  #for odd length palindromes centred around str_s[k]
  i, j, opl = k - 1, k + 1, 1
  while i > 0 and j < len(str_s):
    if str_s[i] == str_s[j]:
      opl++
    i -= 1
    j += 1

  #for even length palindromes
  i, j, epl = k, k+1, 1
  while i > 0 and j < len(str_s):
    if str_s[i] == str_s[j]:
      epl++
    i -= 1
    j += 1

  max_pl = math.max(epl, opl)
```
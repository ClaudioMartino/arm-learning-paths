---
title: Look-Up Tables
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Look-Up Tables

Look-Up Tables (LUT) can be combined with polynomial approximations. In this approach, the exponential function is reformulated as:
$$e^x=e^{(m \times 2^L + j) \times ln2⁄2^L +r} = 2^m \times (2^{j⁄2^L} + 2^{j⁄2^L} \times p(r)) $$

where

$$r∈[-ln2/2^(L+1), +ln2/2^(L+1)], m \in Z, j \in [0,2^L-1]$$

and p(r) approximates e^r -1.

If the 2^L possible values of 2^(j⁄2^L) are precomputed in table T, the exponential can be approximated as:
$$e^x = 2^m \times (T[j]+T[j] \times p(r))$$

With a table of size 2^L, the evaluation interval for the approximation polynomial is narrowed  by a factor of 2^L. As previously discussed, this reduction leads to improved accuracy for a given polynomial degree due to the narrower approximation range. Alternatively, for a given accuracy target, the degree of the polynomial—and hence its computational complexity—can be reduced.


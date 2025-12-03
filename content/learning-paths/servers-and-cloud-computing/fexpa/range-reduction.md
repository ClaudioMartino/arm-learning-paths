---
title: Range reduction
weight: 3

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Range reduction

Polynomial approximations are among the most widely used methods for software implementations of the exponential function. The accuracy of a Taylor series approximation for exponential function can be improved with the polynomial’s degree but will always deteriorate as the evaluation point moves further from the expansion point.

By applying range reduction techniques, the approximation of the exponential function can however be restricted to a very narrow interval where the function is well-conditioned. This approach consists in reformulating the exponential function in the following way:
$$e^x=e^{k×ln2+r}=2^k \times e^r$$
where
$$x=k×ln2+r, k \in Z, r \in [-ln2/2, +ln2/2]$$
Since *k* is an integer, the evaluation of *2^k* can be efficiently performed using bit manipulation techniques, while *e^r* can be approximated with a polynomial *p(r)*. Thus, the overall approximation becomes:
$$e^x \approx 2^k \times p(r)$$
It is important to note that the polynomial *p(r)* is evaluated exclusively over the interval *[-ln2/2,+ln2/2]*. So, the computational complexity can be optimized by selecting the polynomial degree based on the required precision of *p(r)* within this narrow range.

Rather than relying on a Taylor polynomial, a minimax polynomial approximation can be used to minimize the maximum approximation error over the considered interval, thereby achieving higher accuracy for a given polynomial degree.


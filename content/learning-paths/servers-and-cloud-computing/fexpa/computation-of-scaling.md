---
title: Computation of the scaling factor
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Computation of the scaling factor

By leveraging the structure of floating-point number formats, it becomes relatively straightforward to compute  2^k for k∈Z. In the IEEE-754 standard, normalized floating-point numbers in binary interchange format are represented as:
$$ (-1)^s \times 2^{(exponent-bias)} \times (1.fraction)_2 $$
where s is the sign bit and 1.fraction represents the significand. The value 2^k can be encoded by setting both the sign and fraction bits to zero and assigning the exponent field the value k+bias.  
If k is an 8-bits integer, 2^k can be efficiently computed by adding the bias value and positioning the result into the exponent bits of a 32-bit floating-point number using a logical shift. This method enables fast and accurate evaluation without requiring complex arithmetic operations.

Taking this approach a step further, a fast approximation of exponential function can be achieved using bits manipulation techniques alone. Specifically, adding a bias to an integer k and shifting the result into the exponent field can be accomplished by computing an integer i as follows:  
$$i=2^{23} \times (k+bias) = 2^{23} \times k+2^{23} \times bias$$

This formulation assumes a 23-bit significand, as in the single-precision (fp32) floating-point format, but the method can be generalized to other floating-point precisions. Now, consider the case where k is a real number. The fractional part of k  will propagate into the significand bits of the resulting 2^k approximation. However, this side effect is not detrimental, it effectively acts as a form of linear interpolation, thereby improving the overall accuracy of the approximation. To approximate the exponential function, the following identity can be used:
$$e^x=2^{x⁄ln2}$$

As previously discussed, this value can be approximated by computing a 32-bit integer:
$$i = 2^{23} \times x⁄ln2 + 2^{23} \times bias = a \times x + b $$

where a=2^23⁄ln2 and b=2^23×bias.



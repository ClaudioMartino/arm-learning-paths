---
title: Decomposition of the input
weight: 4

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Decomposition of the input
The decomposition of an input value as x=k×ln2+r with r∈[-ln2/2,+ln2/2], can be done in 2 steps:
- Compute k using the formula: k=round(x⁄ln⁡2 ) where round()   denotes the round-to-nearest function
- Compute r as: r=x-k×ln⁡2

Rounding of k is performed by adding an adequately chosen large number to a floating-point value and subtracting it just afterward (the original value is rounded due to the finite precision of floating-point representation). Although explicit rounding instructions are available in both SVE and SME, this method remains advantageous in this context as the addition of the constant can be fused with the multiplication by the reciprocal of ln⁡2. This approach assumes however that the floating-point rounding mode is set to round-to-nearest, which is the default mode   in Armv9-A. By integrating the bias into the constant, 2^k can also be directly computed by shifting the intermediate value.

Rounding error during the second step will introduce a global error as we will have:
$$x≈k×ln2+r$$

To reduce the rounding errors during the computation of the reduced argument r, the Cody and Waite argument reduction technique is used. 



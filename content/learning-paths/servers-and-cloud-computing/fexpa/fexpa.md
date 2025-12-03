---
title: FEXPA
weight: 8

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Fexpa

Arm introduced in SVE an instruction called FEXPA: the Floating Point Exponential Accelerator. 

Let’s segment the IEEE754 floating-point representation fraction part into several sub-fields (Index, Exp and Remaining bits) with respective length of Idxb, Expb and Remb bits.


| IEEE754 precision | Idxb | Expb | Remb |
|-------------------|------|------|------|
| FP16              | 5    | 5    | 0    |
| FP32              | 6    | 8    | 9    |
| FP64              | 6    | 11   | 35   |

The FEXPA instruction can be described for any real number   x∈[2^(Remb+Expb)+1;2^(Remb+Expb)+2^Expb-1[ as: 
$$FEXPA(x)=2^(x-constant)$$

With the constant depending only on the floating-point format:
$$constant=2^(remBits+expBits)+bias$$

The instruction takes the floating-point value x as an input and provided this value is in the right interval, by copying some fraction bits y into the result’s exponent, which is then interpreted as 2^(y-bias), and by getting the correct fraction value from a hardware lookup table using the lower fraction bits, the result becomes 2^(x-constant).

Let’s consider 
$$e^x=2^(x \times 1/(ln2)) $$
We can see that if we decompose x/ln2 = k × C + r such that r is in the right interval to use FEXPA (using Cody and Waite argument-reduction scheme). With this instruction a degree-2 correction polynomial is sufficient the obtain the desired accuracy for single precision:

```C
svfloat32_t lane_consts = svld1rq(pg, ln2_lo); // Load constants

/* k = round(x/ln2)  */
svfloat32_t z = svmad_x(pg, svdup_f32(inv_ln2), x, shift);
svfloat32_t k = svsub_x(pg, z, shift);

/* Fractional residual r = x - k*ln2 */
svfloat32_t r = svmsb_x(pg, svdup_f32(ln2_hi), k, x);
            r = svmls_lane(r, k, lane_consts, 0);

/* scale = 2^k */
svfloat32_t scale = svexpa(svreinterpret_u32(z));

/* poly(r) = exp(r) - 1 */
svfloat32_t p01 = svmla_x (pg, svdup_f32(c0), r, svdup_f32(c1)); // c0 + r c1
svfloat32_t poly = svmul_x (pg, r, p01); // r c0 + r2 c1

return svmla_f32_x(pg, scale, poly, scale) // scale * (1 + poly(r))
```



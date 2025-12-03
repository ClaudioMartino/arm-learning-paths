---
title: Implementation
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Implementation

Given what we said in the previous chapters, the exponential function can be implemented with SVE intrinsics in the following way:

```C
svfloat32_t lane_consts = svld1rq(pg, constants); // Load constants: ln2_lo, c0, c2, c4

/* k = round(x/ln2) */
svfloat32_t z = svmad_x(pg, svdup_f32(inv_ln2), x, shift);
svfloat32_t k = svsub_x(pg, z, shift);

/* r = x - k*ln2 */
svfloat32_t r = svmsb_x(pg, svdup_f32(ln2_hi), k, x);
            r = svmls_lane(r, k, lane_consts, 0);

/* scale = 2^k */
svfloat32_t scale = svreinterpret_f32_u32(svlsl_n_u32_m(pg, svreinterpret_u32_f32(z), 23));

/* poly(r) = exp(r) - 1 */
svfloat32_t p12  = svmla_lane(svdup_f32(c1), r, lane_consts, 2); // c1 + r * c2
svfloat32_t p34  = svmla_lane(svdup_f32(c3), r, lane_consts, 3); // c3 + r * c4
svfloat32_t r2   = svmul_x(pg, r, r);
svfloat32_t p14  = svmla_x(pg, p12, p34, r2); // c1 + r * c2 + c3 * r^2 + c4 * r^3
svfloat32_t p0   = svmul_lane(r, lane_consts, 1); // r * c0
svfloat32_t poly = svmla_x(pg, p0, r2, p14); // r c0 + r^2 c1 + r^3 c2 + r^4 c3 + r^5 c4 

/* exp(x) = scale * exp(r) */
return svmla_f32_x(pg, scale, poly, scale) // scale * (1 + poly(r))
```

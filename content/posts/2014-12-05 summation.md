Title: Summation Proofs
Date: 2014-12-05
Tags: math
Category: code
Slug: summation_proof
Author: Zunayed Morsalin
Summary: Lessons from a project euler problem

## Proof of sum of consecitive numbers 

## Sigma notation proof
$$S(n) = \sum_{i=1}^{n} i$$

Now we will reverse the above equation

$$S(n) = \sum_{i=1}^{i=n} n + 1 - i$$
> An example of the above line for clarity
>
> $S(3) = (3 + 1 - 1) + (3 + 1 - 2) + (3 + 1 - 3)$
>
> $S(3) = 3 + 2 + 1$

$$S(n) + S(n) = \sum_{i=1}^{n} i + \sum_{i=1}^{i=n} n + 1 - i$$
$$2S(n) = \sum_{i=1}^{i=1} i + (n + 1 - i)$$
$$S(n) = \sum_{i=1}^{i=1} \frac{n + 1}{2}$$

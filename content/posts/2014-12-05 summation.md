Title: Summation Proofs
Date: 2014-12-05
Tags: math
Category: code
Slug: summation_proof
Author: Zunayed Morsalin
Summary: Lessons from a project euler problem


## Original Problem and brute force method
a brute force solution while fine for small numbers but will be an issue as the limit gets higher

## New Solution and benchmarks comparing two

## Proof of sum of consecutive numbers 

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
$$2S(n) = \sum_{i=1}^{i=1} n + 1$$
$$2S(n) = n(n + 1)$$
$$ S(n) = \frac{n(n+1)}{2}$$ 

## Proof of sum of squares



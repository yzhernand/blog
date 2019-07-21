---
title: "Perl Weekly Challenge 17: The Ackermann function"
date: 2019-07-19T07:07:53-04:00
draft: false
---

<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

The [Perl Weekly Challenge](https://perlweeklychallenge.org/blog/perl-weekly-challenge-017/) is back, and our first challenge was to implement a function known as the [**Ackermann function**](https://en.wikipedia.org/wiki/Ackermann_function):

> Create a script to demonstrate Ackermann function. The Ackermann function is defined as below, m and n are positive number:
> 
>   A(m, n) = n + 1                  if m = 0
>   A(m, n) = A(m - 1, 1)            if m > 0 and n = 0
>   A(m, n) = A(m - 1, A(m, n - 1))  if m > 0 and n > 0

Technically, the above formulation is known as the "**Ackermann-Péter function**", but as the Wikipedia page states, many authors refer to it as "the" Ackermann function.

This is looked like a fairly straightforward implementation of a recursive function, but it did have a few hairs. Firstly, as can be seen above, the function is [piecewise](https://en.wikipedia.org/wiki/Piecewise), and is defined only for non-negative integers. In fact, the recursive calls gradually step the values down to 0, with the deepest call possible being reached when \\(m = 0\\).

```perl
sub A ( $m = 3, $n = 3) {
    croak "Error: function only defined for nonnegative integers."
        . "(got: m = $m, n = $n)"
        if ( $m < 0 && $n < 0 );

    # A(m, n) = n + 1                  if m = 0
    return $n + 1 if $m == 0;

    # A(m, n) = A(m - 1, 1)            if m > 0 and n = 0
    # A(m, n) = A(m - 1, A(m, n - 1))  if m > 0 and n > 0
    return A( $m - 1, ($n == 0) ? 1 : A($m, $n-1) );
}
```

One thing I initially missed when implementing this, is that the function not only should subtract 1 from `$n` when both values are greater than 0, but it should actually make a second, embedded call there! It took me forever to notice that was missing 😒.

Once that was implemented, I decided to test it with larger values, because I don't read whole Wikipedia pages. After about a minute of waiting, I decided to scoll down... You should check out the [table of values](https://en.wikipedia.org/wiki/Ackermann_function#Table_of_values) on the wiki page. The short version is that the Ackermann function grows. VERY quickly. I kept my testing down to some smaller values, and those all passed.

Finally, I decided to compare performance with and without memoization. Memoizing really only makes sense with repeated execution, and I don't know of the actual applications of this function so I don't know how true that would be.

With default parameters, using [`Benchmark::Forking`](https://metacpan.org/pod/Benchmark::Forking) gets me the following results;

```
            Rate no_memo    memo
no_memo 269501/s      --    -68%
memo    850801/s    216%      --
```

Its a definite improvement, but again, I'm not sure how useful it would be in a real world application.

See the full solution as a Gist, [here](https://gist.github.com/yzhernand/0e74d23bcd040e3a3106520763d55613).
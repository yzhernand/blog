---
title: "Perl Weekly Challenge 11: Temperature Scales And Identity Matrices"
date: 2019-06-05T11:20:22-04:00
draft: false
---

<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

Another [Perl Weekly Challenge](https://perlweeklychallenge.org). I found this week's challenges easier than last week's, which is not bad as I don't necessarily want to stress out over something that is meant to be a learning experience and to keep my skills up.

# Challenge 1

>Write a script that computes the equal point in the Fahrenheit and Celsius scales, knowing that the freezing point of water is 32 °F and 0 °C, and that the boiling point of water is 212 °F and 100 °C. This challenge was proposed by Laurent Rosenfeld.

Pretty easy. We can approach this a few different ways, but I think the most straightforward is to just use the formula for converting °C to °F: $$°F = °C\left(\frac{9}{5}\right) + 32$$

Following some basic rules in math, if we just set this so that \\(°F = °C\\), this would become:

$$\begin{align}
°F &= \tfrac{9}{5}°F + 32 \newline
0 &= \tfrac{9}{5}°F - °F + 32 \newline
-32 &= \tfrac{4}{5}°F \newline
-40 &= °F
\end{align}$$

From lines 2 to 3, we subtract \\(°F\\) from \\(\tfrac{9}{5}°F\\), which results in \\(\tfrac{4}{5}°F\\) because \\(°F = \tfrac{5}{5}°F\\).

Ok so pretty simple enough. As long as the equation is a linear equation (which means it can be expressed in the form \\(y = mx + b\\)), we can write a generic equation to find the value where \\(y = x\\).

```perl
sub find_x_eq_y {
    my ($slope, $y_intercept) = @_;

    # Solve for x = y
    # $x = $x*$slope + $y_intercept
    # -$y_intercept = $x*$slope - $x
    # $x($slope-1) = -$y_intercept;
    return (-$y_intercept)/($slope - 1);
}
```

Just call that function supplying the values for \\(m\\) (the slope) and \\(b\\) (the y-intercept). In our case, we can get those values either from the conversion equation, or from the challenge statement. If we pretend all we had was the challenge statement, then if °C is the _x_-axis and °F is the _y_-axis we can calculate \\(m\\) using the freezing and boiling points of water in each scale:

$$\begin{align}
m &= \frac{\Delta y}{\Delta x}\newline
 &= \frac{212-32}{100 - 0}\newline
 & = \frac{180}{100}\newline
 &= 1.8
\end{align}$$

Not coincidentally, \\(\tfrac{9}{5} = 1.8\\). The value for \\(b\\) can be inferred from the fact that \\(32 °F = 0 °C\\), so \\(b = 32\\).

# Challenge 2

>Write a script to create an Indentity Matrix for the given size. For example, if the size is 4, then create Identity Matrix 4x4. For more information about Indentity Matrix, please read the wiki page.

There are probably plenty of tricks in Perl for quickly generating the matrix for this challenge. In my case, I went for the obvious implementation. But then again, I also tend to lean towards the more understandable way of writing code rather than going code golfing.

```perl
sub ident_mat {
    my $n = shift or croak "Must supply an value for size of square matrix";

    # Force integer
    $n = int $n;
    my @i_mat = ();

    for my $i ( 0 .. $n - 1 ) {
        my @a = (0) x $n;
        $a[$i] = 1;
        push @i_mat, \@a;
    }

    return \@i_mat;
}
```

If you know Perl, or any C-like actually, this should be fairly self-explanatory. Basically, given a value for `$n`, ensure that value is an integer. Then just generate `$n` arrays of size `$n` with all 0's. For the `$i`th array, flip the `$i`th position to a 1. Since this is Perl, the array is pushed on to another array as an arrayref. That array is then returned as an arrayref as well after loop termination.

UPDATE: If we wanted to scare people with our awesome Perl skills, we might have also written this as:

```perl
sub ident_mat_compact {
    my $n = shift or croak "Must supply an value for size of square matrix";

    my @i_mat;
    return [map { my $i = $_;
            [map { 0+($_ == $i)} ( 0 .. $n - 1)];
        } ( 0 .. $n - 1 )];
}
```

The inner map generates an array of 1's and 0's by mapping over the range `( 0 .. $n - 1)`. A 1 is produced only when `$_` is equal to `$i`. The `0+` (also known as the [Venus operator](https://metacpan.org/pod/perlsecret#Venus)) ensures the value will be interpreted as an integer value. The outer map also maps over the same range, but sets `$i = $_` so that the inner map can work unambiguously. Each map is surrounded by `[]` so that the list becomes a reference.

Maybe I should start learning some Perl 6. I feel like all that stuff about lazy evaluation might come in handy here, particularly for very large values of `$n` in some real-world problems. If I could wrap my head around it.

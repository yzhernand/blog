---
title: "Perl Weekly Challenge 13: Last Friday of the month / Mutually recursive methods"
date: 2019-06-21T10:26:18-04:00
draft: false
---

Note: I've also posted this over on [dev.to](https://dev.to/yzhernand/perl-weekly-challenge-13-last-friday-of-the-month-mutually-recursive-methods-73f) in case anyone wanted to leave any comments there.

This week's [Perl Weekly Challenge](https://perlweeklychallenge.org) had two fairly fun challenges, and a 3rd API challenge that unfortunately needed an additional step I wasn't willing to take to participate in (the website required credit card details to use their API at any level).

# Challenge 1

>Write a script to print the date of last Friday of every month of a given year.

I chose to go the easy route for this challenge and just use the widely recommended [DateTime](https://metacpan.org/pod/DateTime) module. There are some tasks for which it is simply better to use existing libraries where all of the heavy lifting has been done for you. Working with dates and time are such a situation.

```perl
use DateTime;
use DateTime::Duration;

my $year = shift or die "Usage: $0 <year>\n";

my $dt = DateTime->new(
    month => 1,
    day   => 1,
    year  => $year
);
my $add_week = DateTime::Duration->new( weeks => 1 );
my $add_day  = DateTime::Duration->new( days  => 1 );
```

In my solution, I take a fairly simple approach. In the block above, I set up a few variables: the year comes from the user on the command line, the `$dt` variable is a `DateTime` object that I will use as a sort of "cursor" to keep track of where we are in the year, and the last two variables (`$add_week` and `$add_day`) are `DateTime::Duration` objects for my convenience. I will use those two variables to "walk" along the dates in a year. You'll see what I mean below.

```perl
while ( $dt->day_of_week() != 5 ) { $dt->add($add_day); }
```

In the set up block I showed earlier, `$dt` was initialized to be the first date in the given year, which is always January 1. The first while loop in this block adds (using [`DateTime::add`](https://metacpan.org/pod/DateTime#$dt-%3Eadd(-parameters-for-DateTime::Duration-))) one day so long as the current date in `$dt` is not a Friday. So if January 1st was not a Friday, keep adding one day until we arrive at a Friday. If it was, we don't add any days at all.

That's what the `$add_day` variable is used for. The `DateTime` module provides an alternative way of calling that function, allowing us to give the same parameters we needed to create a new `DateTime::Duration` object (eg, `$dt->add(days  => 1)`). This form of the call creates a new `DateTime::Duration` object on each invocation, which is why I decided to avoid it and instead keep an already initialized variable for this purpose. Even if some internal optimization were smart enough to eliminate any potential slowdown for me, I still prefer being explicit about what I am doing in the code.

```perl
my %last_fri;
while ( $dt->year == $year ) {
    $last_fri{ $dt->month } = $dt->ymd("/");
    $dt->add($add_week);
}
```

Now we walk along by weeks (using `$add_week`, with the same motivations for using a variable as above). I use a hash and save the string representation of the date, in "y/m/d" format, of every Friday in the year using the numeric value of the month as the key. This gives the nice side effect of only saving the last Friday for the month, as the previous Fridays get overwritten so long as the month is the same, which is what the challenge asked us to do.

We exit the loop when we move on to the next year.

Finally, we print the results:

```perl
{
    local $, = "\n";
    say @last_fri{ sort { $a <=> $b } keys %last_fri };
}
```

The above could have been a one-liner, but I wanted to make use of a feature that maybe not everyone is aware of. That is, the setting of the `$,` variable (also called `$OFS`) locally. The curly braces are used to start a new block, and we use the `local` keyword to tell perl that we want to use an existing variable name, but we will be making a local copy of it so we can safely change its value within this block. Now, printing with `say` will separate each of its arguments with "\n" (normally, `$,` is set to `undef`). Note that the above could have been achieved with a simple `join` using "\n" as the separator, but the above form would come in handy if we needed to print several lists with the same separator, and wanted to save on the keystrokes.

I numerically sort the keys of my hash so that the dates are printed out as specified in the challenge.

# Challenge 2

>Write a script to demonstrate Mutually Recursive methods. Two methods are
mutually recursive if the first method calls the second and the second calls
first in turn. Using the mutually recursive methods, generate [Hofstadter Female and Male sequences](https://en.wikipedia.org/wiki/Hofstadter_sequence#Hofstadter_Female_and_Male_sequences).

Recursive methods can be fun to write, but I've never done mutually recursive methods. This was a deceptively easy challenge. Once I wrote it, I sort of thought to myself "that can't be all, right?". So of course, I jazzed it up a bit:

```perl
use feature qw(say state);

sub hofstadter_F {
    my $n = shift;
    state %cache = ( 0 => 1 );

    return $cache{$n} if exists $cache{$n};
    return $cache{$n} = $n - hofstadter_M( hofstadter_F( $n - 1 ) );
}

sub hofstadter_M {
    my $n = shift;
    state %cache = ( 0 => 0 );

    return $cache{$n} if exists $cache{$n};
    return $cache{$n} = $n - hofstadter_F( hofstadter_M( $n - 1 ) );
}
```

First thing to notice, is that the bodies of each function have calls to the other. This sort of thing just works. So...let's just leave it at that.

The more interesting thing, is the use of a hash which I uncreatively called `%cache` because it is...a cache! Without this cache, we must do all the work of calculating the intermediate values of the functions on each call. Why is that important?

The Hofstadter Female and Male functions produce sequences, which means that we might need to call them *n* times to get the first *n* terms. If we call them 10 times, you might not notice any need for improvement of performance. But if you wanted a sequence of the first 100 values, you'll notice you'll need to wait a while.

Enter `state`, which we enable using `use feature 'state';`. Similar to my [previous blog post](https://yzhernand.github.io/posts/perl-weekly-challenge-12/) where I talked about closures, `state` is a way of keeping the state of a variable across function calls. Instead of returning an anonymous sub which keeps its own state, however, `state` allows us to keep the state of a variable in a named function, without needing to keep another reference to it.

In each function, I initialize the cache hashes to contain the value for the base call (`F(0)` or `M(0)`). Then, on each call, the hash is checked to see if we have already computed the value of the *n*th term in the sequence. If so, it is returned. Otherwise, we make the appropriate call to calculate it, and save it into the cache while also returning it.

```perl
my $n = shift or die "Usage: $0 <n>\n";

say "F: ", join( ", ", map { hofstadter_F($_) } ( 0 .. $n ) );
say "M: ", join( ", ", map { hofstadter_M($_) } ( 0 .. $n ) );
```

On my machine, without using a cache, calling both functions with `$n = 100` results in a total runtime of nearly 31 seconds! With the cache, the program ran in under 40ms and was done in a literal blink of an eye. Bumping it up to 10000 took 90ms. I did not try that value of *n* without a cache.

A core module called [`Memoize`](https://metacpan.org/pod/Memoize) does this sort of caching for you, under certain conditions. The POD page is well worth a read. I could have done the above by using `Memoize`, but then I might not have had the opportunity to provide an example to write about :).
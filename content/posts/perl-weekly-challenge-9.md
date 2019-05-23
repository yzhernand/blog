---
title: "Perl Weekly Challenge 9"
date: 2019-05-21T09:25:05-04:00
draft: false
---

This will be my first ever blog post ever. Hi there!

I decided to blog a bit so that I can write about the process that goes into solving the Perl Weekly Challenge that I recently heard about. I love Perl, challenging myself with programming tasks, and procrastination. It all works out!

So this post will deal with the 9th challenge so far. Check out this challenge, as well as the Perl Weekly Challenge page, [here](https://perlweeklychallenge.org/blog/perl-weekly-challenge-009/).

# Chellenge #1

> Write a script that finds the first square number that has at least 5 distinct digits. This was proposed by Laurent Rosenfeld.

This was a fun one. In the work I've done in the past, speed was always important. The more we could cut down run time the better. So I brought that mentality here to this challenge, even if the "speedup" was probably not worth it and this "optimization" could hardly be called that.

Anyway, my idea was to just not waste time starting with 1, or 2, or any square root that would not yield at least a 5-digit square. So, for a general case of an n-digit square:

```perl
my $root = int( sqrt( 10**( $n - 1 ) ) );
```

In this case, if `$n = 5`, then `$root = 316`. Cool, we just skipped uselessly squaring `1 .. 315` (and possibly checking the squares to see if we satisfied the challenge condition, depending on how you wrote your code).

Next, we have to loop until we find a perfect square which has at least `$n` unique digits. So I wrote my loop like so:

```perl
while ( 1 ) {

    # Use positive lookahead to get rid of duplicate digits
    ( my $dedup = $root**2 ) =~ s/(.)(?=.*?\1)//g;
    last if ( length($dedup) >= $n );
    $root++;
}
```

I used [Positive Lookahead](https://perldoc.pl/perlretut#Looking-ahead-and-looking-behind) to remove characters if they had already occurred in the string. I could have done something else, like splitting the string and using the characters as keys in a hash, but I figured this could be accomplished using regular expressions. Being Truly Lazy™, I searched online before I brushed up on my lookahead knowledge and confirmed that someone else had already solved the problem for me. So credit to "codaddict" on StackOverflow for [their answer](https://stackoverflow.com/questions/2582940/how-do-i-remove-duplicate-characters-and-keep-the-unique-one-only-in-perl).

We only exit the loop when we find a square with at least `$n` unique digits, so setting `$n` to something like 11 for base 10 will result in an infinite loop.

I'm sure I could have done this in some very clever way, but this seems readable to me while also being compact. I'm curious to see other's solutions.

# Challenge #2

> Write a script to perform different types of ranking as described below:

```
1. Standard Ranking (1224): Items that compare equal receive the same ranking number, and then a gap is left in the ranking numbers.
2. Modified Ranking (1334): It is done by leaving the gaps in the ranking numbers before the sets of equal-ranking items.
3. Dense Ranking    (1223): Items that compare equally receive the same ranking number, and the next item(s) receive the immediately following ranking number.
```

I wasn't sure what the expected output would be here, so I opted to produce a table sorted by the ranked item, with three more columns each giving the different rankings for that item.

There was also no input provided, so I used the [World Bank's estimate](https://data.worldbank.org/indicator/SI.POV.GINI) of the [Gini index](https://en.wikipedia.org/wiki/Gini_coefficient), a statistic used to measure wealth inequality. The item to rank, therefore, was the Gini index value for each country on the list. Countries without any ranking were sorted to the bottom and displayed with a value of NA for the Gini index.

This turned out to be a much simpler problem than I anticipated. Thanks to the descriptions on the [wiki page on ranking](https://en.wikipedia.org/wiki/Ranking) for each ranking strategy, I figured I could do this with simply two counters: one for the total number of items ranked so far, and the other for the total number of unique items.

```perl
my $running_count = 0;
my $dr = 1;
say "Country\tGini (World Bank)\tStandard Ranking\tModified Ranking\tDense Ranking";
for my $g (sort {$b <=> $a} keys %data) {
    my @countries = @{$data{$g}};
    $g = "NA" if $g == -1;
    for my $c (@countries) {
        say "$c\t$g\t" . ($running_count+1) . "\t" . ($running_count+@countries) . "\t" . $dr;
    }
    $running_count += @countries;
    $dr++;
}
```

The data is read into a hash before this code is executed. The keys are the Gini index and the values are an arrayref of the countries with that Gini index value. The items to iterate over are simply a sorted list of the keys in the data. Here that means a numerically descending list of the Gini index values, which could be accomplished with a simple `sort {$b <=> $a} keys %data`.

For the "standard" ranking, items get a ranking equivalent to all the items that came before them, plus one. So I just get my running count total and add 1. Since it is initialized at 0, this gets 1 for the highest ranked item.

For "modified" ranking, there is a "gap" in the rankings *before* the current item, so you must add the number of tied items to the running count to produce the correct ranking.

Finally, the "direct" count is easiest: you just keep a counter that starts at 1 and only increment when you move on to the next Gini index value.

The `$running_count` is also updated each iteration with the number of items at the current index value.

# Challenge #3

I don't think I will tackle the API challenge. Maybe another week. The other two challenges look like puzzles and this one seems a bit more like work.

I look forward to doing more of these as not only do they seem like a fun way to flex my Perl and problem-solving skills, but I find immense value in reading other people's solutions. It's often the case that I find some incredibly creative way of solving the problem, and some solutions are so unique that I learn so much more about this language I *thought* I understood.

That's all for this week, thanks for reading!

---
title: "Perl Weekly Challenge 10: Roman numerals and Jaro-Winkler distance"
date: 2019-06-02T13:49:47-04:00
draft: true
---

Back at it again with another week of the Perl Weekly Challenge. This time for challenge #10

# Challenge #1

> Write a script to encode/decode Roman numerals.
> For example, given Roman numeral CCXLVI, it should return 246.
> Similarly, for decimal number 39, it should return XXXIX.

I made the following assumptions for this challenge:

1. Valid Roman Numerals follow the "Subtractive notation" convention mentioned on the [Wikipedia page](https://en.wikipedia.org/wiki/Roman_numerals).
2. Only one leading subtractive character is allowed (eg, 'VIII' for '8', but not 'IIX').
3. The maximum representable value is 3,999.
4. No unicode characters would be used as input (ASCII only)

I thought about a few ways of doing this, notably using a [PDA](https://en.wikipedia.org/wiki/Pushdown_automaton). It would have been fun to challenge myself with that, but this week I only had a few hours do the challenge, so I went for the quickest thing I could throw together.

## Roman numeral -> Arabic integer

The first step I wanted to take was to cut down the work I might need to do, particularly with respect to validation. We all know how important checking for valid input is. Thanks to StackOverflow again for supplying lazy developers everywhere with a place to ~~steal code from~~ look to for inspiration. Anyway, user 'paxdiablo' [posted](https://stackoverflow.com/a/267405/876844) a nice regex for valid Roman numerals, along with an explanation on how it works.

I used two hashes to store Roman numeral values: one to give me the value of each numeral (numerals as keys and numeric values as values), and the second to translate from integer values to numerals. The second hash had multi-character numerals to make my life easier:

```perl
my %roman_vals = (
    'I' => 1,
    'V' => 5,
    'X' => 10,
    'L' => 50,
    'C' => 100,
    'D' => 500,
    'M' => 1000,
);

my %val_to_roman = (
    1    => 'I',
    4    => 'IV',
    5    => 'V',
    9    => 'IX',
    10   => 'X',
    40   => 'XL',
    50   => 'L',
    90   => 'XC',
    100  => 'C',
    400  => 'CD',
    500  => 'D',
    900  => 'CM',
    1000 => 'M',
);
```

The code to convert a Roman numeral to an integer representation in Arabic numerals is a bit convoluted. But makes sense when broken down.

```perl
    # Traverse string from left to right.
    # Assume subtractive notation and increment while
    # the value is the same or decreasing. Subtract
    # from current if value increases.
    # Assumes regex above allows only valid numerals
    for ( my $r = 0; $r < @roman_arr; ) {
        my $curr = $roman_arr[$r];
        $val = $roman_vals{ $roman_arr[$r] };

        # Consume all of the same value, adding along the way
        while ( ++$r < @roman_arr && $curr eq $roman_arr[$r] ) {
            $val += $roman_vals{ $roman_arr[$r] };
        }

        # If there's still more of the array, and the next
        # value is greater than that of the current symbol,
        # subtract value of current symbol. That is the new
        # current value.
        # This actually ignores the "extra" symbols in something
        # like IIIX, which would be considered invalid anyway.
        if (   $r < @roman_arr
            && $roman_vals{$curr} < $roman_vals{ $roman_arr[$r] } )
        {
            $val = $roman_vals{ $roman_arr[$r] } - $roman_vals{$curr};
            ++$r;
        }

        $decoded += $val;
    }
```

Basically I split the numeral's string into an array and traverse it from left-to-right, consuming characters as long as the value of the current character is the same (or the end of the array is reached).

This means if you have something like this: `XXXVII`, the code will pick up the `X`, and walk along the array until it hits the `V`. All along, it keeps a sum of the value of `X` (10). As soon as it hits the `V`, it starts to keep track of a new sum, adding the old sum to the total. And so on.

Why do it that way? Because suppose the string looked like this: `XXXIVI`. While processing the `X`'s, it runs into an `I`, and `I ne X`. The value accumulated so far is added to the total, and a new sum is started. However, the next character is `V`. Now we make a decision: is this new value greater than the current character's value? If so, subtract from the new character's value and increment the counter so we skip this character next time. Otherwise we just move on and iterate again.

In this case, it takes `V = 5` and subtracts `I = 1` from that, saving a value of 4. Then we jump past the 'V' and loop again. The next character is `I`, its sum is kept, and then the end of the array is reached, so that value (1) is added to the running total.

## Arabic integers -> Roman numerals

Converting an int to a Roman numeral was surprisingly easy.

```perl
sub int_to_roman {
    my $int = shift;
    croak "Error: Integer exceeds maximum representable value."
        if $int > MAX_INT;

    my $str;
    for my $val ( sort ( { $b <=> $a } keys %val_to_roman ) ) {

        # Must find the largest numeral which can be subtracted
        while ( $val <= $int ) {
            $int -= $val;
            $str .= $val_to_roman{$val};
        }
    }

    return $str;
}
```

I set `MAX_INT` to be 3,999 since that is the largest value representable in "subtractive notation". After that, I just find the largest numeral value which can be subtracted from the int, and just keep subtracting it as many times as I can, concatenating the numeral to a string as I go.

Since the `val_to_roman` hash includes compound numerals, like `IX`, I can be sure my output also obeys the convention of subtractive notation.

Check out my full solution [here](https://github.com/manwar/perlweeklychallenge-club/blob/master/challenge-010/yozen-hernandez/perl5/ch-1.pl).

# Challenge 2

> Write a script to find Jaro-Winkler distance between two strings.

During my time in a PhD program, I had to work a lot with alignment algorithms. But I was used to working in C, and never with an algorithm that had to keep track of transpositions, so this threw me off for a bit. After reading the [paper by Winkler](https://files.eric.ed.gov/fulltext/ED325505.pdf) on his modification of Jaro's algorithm, I understood it a bit more (the [Wikipedia page](https://en.wikipedia.org/wiki/Jaro–Winkler_distance) was a little too high level for me to properly implement it).

I won't go over details into the algorithm itself, you can check out the Wikipedia page and the paper I linked above for more on that. Though I may write a post on this which is more in-depth later.

I don't have much to say about my implementation apart from the fact that I think its much more how I might code it in C and could probably be much more "Perl-y". About the "Perliest" thing I did in it was my code to extract the characters with matches from each string:

```perl
    # Get only the matching characters from each string.
    @s1_matches = @s1[ grep { $s1_matches[$_] } 0 .. @s1_matches ];
    @s2_matches = @s2[ grep { $s2_matches[$_] } 0 .. @s2_matches ];
    my $matches = @s1_matches;
```

The `@s*_matches` arrays are arrays of 0/1 flags, as long as each string. So I filter out only the indices in those arrays for which the value is 1, and use those indices to take a slice of the string arrays (`@s*`). I save those again in the `@s*_matches` arrays so I just have the matching positions.

For example, in the strings "DWAYNE" and "DUANE", I get a match array for "DWAYNE" that look as follows: `(1, undef, 1, undef, 1, 1)`. Every 1 indicates a place where the character in that position found a match in "DUANE". Applying the code above gives me the list `(D, A, N, E)`. Since this is the same array you would get from "DUANE" in this comparison, the number of transpositions is 0.

Check out my full solution [here](https://github.com/manwar/perlweeklychallenge-club/blob/master/challenge-010/yozen-hernandez/perl5/ch-2.pl).
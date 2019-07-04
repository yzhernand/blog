---
title: "Perl Weekly Challenge 14: van Eck’s sequence / Adventures in overthinking- making words from US state initials"
date: 2019-06-30T12:01:08-04:00
draft: false
---

<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

**Warning** This post ended up being pretty long. A million thanks if you make it through to the end.

This week's [Perl Weekly Challenge](https://perlweeklychallenge.org/blog/perl-weekly-challenge-014/) offered two very nice challenges that I really had to think about. Later in the week, Neil Bowers, who is credited with suggesting the second challenge, [posted](http://neilb.org/2019/06/24/additional-challenge-14.html) about an additional challenge since his submission was misunderstood.

Now with that out of the way, let me walk you through how I approached these challenges.

# Challenge 1

>Write a script to generate Van Eck’s sequence starts with 0. For more information, please check out wikipedia [page](https://en.wikipedia.org/wiki/Van_Eck%27s_sequence). This challenge was proposed by team member Andrezgz.

Another week, another numeric sequence :). van Eck's sequence is a bit trickier than other sequences I've generated in previous challenges (see my posts on [challenge 9](https://yzhernand.github.io/posts/perl-weekly-challenge-9/) where I simply generate squares, [challenge 12](https://yzhernand.github.io/posts/perl-weekly-challenge-12/) which discusses [Euclid numbers](https://en.wikipedia.org/wiki/Euclid_number), and [challenge 13](https://yzhernand.github.io/posts/perl-weekly-challenge-13/) which discusses [Hofstadter Female and Male sequences](https://en.wikipedia.org/wiki/Hofstadter_sequence#Hofstadter_Female_and_Male_sequences)), as we will see.

```perl
sub van_eck_seq ( $n, $init = 0 ) {

    # Base case. It should always be $init followed
    # by 0, given the definition.
    return ( $init, 0 ) if ( $n == 0 );

    my @seq = van_eck_seq( $n - 1, $init );
    my $m   = first { $seq[$_] == $seq[$n] } reverse( 0 .. ( $n - 1 ) );
    my $val = ( defined $m ) ? ( $n - $m ) : 0;

    return @seq, $val;
}
```

Going by the definition of the sequence on Wikipedia, 

>\\(a\_0 = 0\\). Then, for \\(n ≥ 0\\), if there exists an \\(m < n\\) such that \\(a\_{m} = a\_n\\), take the largest such \\(m\\) and set \\(a\_{n+1} = n − m\\); otherwise \\(a\_{n+1} = 0\\).

I thought of this as "each \\(n\\)th term generates the \\(n+1\\)th term". So, if you call the function I've written above with `van_eck_seq(2)` you would get the first 3 elements of the sequence, not the first 2. This is important because I've written the function to be recursive, and the code following the nested call expects that the list it gets back includes the "current" \\(n
\\)th term.

Also by that definition, it looks like \\(a_1 = 0\\) since there is never an \\(m < n\\) such that \\(a\_{m} = a\_n\\). The base case of `van_eck_seq(0)`, therefore, returns a list with items `(0, 0)`.

Actually, that's not totally true: van Eck's sequence can have a different \\(a\_0\\) term, which changes the sequence. So my function definition allows an optional setting of that term as it's second argument, `$init`.

Continuing, for all \\(n \geq 1\\), we make a nested call to the same function, passing it `$n-1` and the value of `$init` (in case the caller changed it) and use the returned sequence to generate the next value.

This line:

```perl
my $m   = first { $seq[$_] == $seq[$n] } reverse( 0 .. ( $n - 1 ) );
```

lets us walk backward through the sequence we got from the nested call so we can find that \\(m < n\\) mentioned in the definition, if any. The value of `$val` depends on whether or not such an \\(m\\) was found, and then the combined sequence is returned. The function can be called like this:

```perl
local $, = ", ";
say van_eck_seq(10);
```

which gives as output:

```
0, 0, 1, 0, 2, 0, 2, 2, 1, 6, 0, 5
```

For this challenge, I used the `Memoize` module instead of caching myself (see my [post](https://yzhernand.github.io/posts/perl-weekly-challenge-13/) on last week's challenge). I also documented the behavior that the function call returns a list of n+1 elements, as a person who would want to use that function might not expect that.

See the full solution, [here](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-014/yozen-hernandez/perl5/ch-1.pl).

# Challenge 2

>Using only the official postal (2-letter) abbreviations for the 50 U.S. states, write a script to find the longest English word you can spell? Here is the list of U.S. states abbreviations as per wikipedia page. This challenge was proposed by team member Neil Bowers.

Turns out that this challenge wasn't quite what Neil intended, but I think most of us participants are going to solve it as-is. Further below I write about my solution to his actually intended challenge.

```perl
my %longest_word = ( words => [], length => 0 );

#...
open my $wl, "<", "/usr/share/dict/words";
while ( my $w = <$wl> ) {

    # Chomp and drop apostrophes or any other
    # non-alphabetical characters
    chomp $w;
    my $w_alpha = $w;
    $w_alpha =~ s/[^[:alpha:]]//g;

    # Skip unless length is even: all US state codes are bigrams
    next unless ( length($w_alpha) % 2 == 0 );

    # Use unpack to split word into an array of bigrams
    # and use UC to make it all uppercase
    my @w_split = unpack "(a2)*", uc($w_alpha);

    # Skip if the set created from the word is not a
    # proper subset of the US state codes list.
    next unless all { exists $us_states_to_val{$_} } @w_split;

    # By now, we can be sure that the word is only
    # composed of elements in the us_states list.
    if ( length($w_alpha) > $longest_word{length} ) {
        @longest_word{qw(words length)} = ( [$w], length($w) );
    }
    elsif ( length($w_alpha) == $longest_word{length} ) {
        push $longest_word{words}->@*, $w;
    }
}
```

We start with a list of US states (not shown) and use a hash to store the length of the longest word seen and a list of those words, since there may be more than one. The length value is initialized to 0.

We skip all odd-length strings, because US state codes are all 2 letters long (bigrams). I tried using `unpack` for the first time ever because it looked like a nice way to generate a list of bigrams from a string. Then we reject any words which have a bigram which is not in the list of US state codes.

The final bit of code is just an if-elsif branch which checks if the word we found is longer than the longest seen so far. If it is, we basically reset the hash and store the word and new longest length. If it is the same, then we push the word onto the arrayref in the "words" value of the hash. Shorter words are just ignored.

I peeked at a few other solutions to this challenge after I was done. I think I like some of them better because they are more concise, but then I do like to break things up so that I can see what everything is doing a bit more easily. I suspect that performance is similar between the solutions, but I haven't done any benchmarking to check.

With the wordlist dictionary on Arch Linux, this script gives the following output:

```
Longest word(s) constructed using USPS state codes: armorial, calamari, calamine, coalmine, Concorde, Denmark's, Ganymede, landmine, mainland, malarial, Mandarin, mandarin, melamine, memorial, mescalin, moorland
with a length of 8 alphabetical characters
```

and using the alphabetic-only list from https://github.com/dwyl/english-words

```
Longest word(s) constructed using USPS state codes: cacogalactia
with a length of 12 alphabetical characters
```

See the full solution, [here](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-014/yozen-hernandez/perl5/ch-2.pl).

# Additional Challenge 2

>What's the longest word you can spell by traversing US states, taking the initial or initials of the states as you pass through them, without revisiting any states?

Ok so this one made me tear out my hair a little. I am a little jealous of others' solutions which seemed much simpler than mine. But I am also a bit proud of the overengineering since I got to use some concepts in CS that I haven't really used since my undergrad.

When I first read the problem statement, I immediately thought of a [graph](https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)). Basically, a graph is a data structure where things (usually strings, or another object of some sort) are connected to other things in some way. The "things" are called "vertices" (sing, vertex) and the connections are called "edges". Graphs can have edges with, or without, direction, making traversal of the graphs different depending on the type of graph. Luckily there is a module, [`Graph`](https://metacpan.org/pod/Graph), which is included in Perl which can represent these structures.

That takes care of the requirement that the states need to be adjacent. What about searching for words? In the previous challenge, we could just iterate over all words in the dictionary and check them to see if they could be made using the state codes. Here, we would actually need to generate words from the states, given their adjacency, and then check to see if that is a valid word.

Luckily, there is a data structure for that™: the [Trie](https://en.wikipedia.org/wiki/Trie), a search tree which allows for the lookup of words by prefix. It is ideal for applications such as predictive text, where words are suggested to a user based on the first few letters typed so far. We can use this to check if a word we are generating from the graph is in the dictionary. And of course, there is also a Perl module! [`Tree::Trie`](https://metacpan.org/pod/Tree::Trie).

```perl
sub get_paths ( $graph, $trie, $vertex, $data, %seen ) {
    $seen{$vertex} = 1 + ( keys %seen );

    my $string_so_far = fc(
        join( "",
            map { $data->{$_}->{initials} }
                ( sort { $seen{$a} <=> $seen{$b} } keys %seen ) )
    );

    # Filter out successive vertexes which have already been visited
    # and which would not produce a word in the trie
    my @pot_successors = grep {
        !( exists $seen{$_} )
            && $trie->lookup(
            $string_so_far . fc( $data->{$_}->{initials} ) );
    } sort { $a cmp $b } $graph->neighbors($vertex);

    # Base case: no more successors, done with this path
    if ( !@pot_successors ) {
        return ( join( "->", sort { $seen{$a} <=> $seen{$b} } keys %seen ),
            $string_so_far );
    }
    else {
        # Process successors
        return
            map { get_paths( $graph, $trie, $_, $data, %seen ) }
            @pot_successors;
    }

}
```

I'm not showing the code for initializing the graph or the trie here. You can check out the source code for that. The function above, however, lets us make several trips through the graph, given a starting point/vertex. We use a hash, `%seen` to keep track of the vertexes visited along the path so far. Each time a vertex is visited, it goes into the hash and gets a value based on the length of the path so far. I pay a penalty each time this function is called sorting that hash, but I'm willing to do so.

The next chunk of code gives us a list of potential next vertices to visit, both by eliminating already visited vertices, and by checking if visiting that vertex we are still building a valid word. If taking that path would be a "dead end", then we simply won't visit that vertex.

When there are no more potential successive nodes, we have reached the end of a path and build two strings: the path taken and the string generated. The former is just to make our output nicer and more informative. The latter is what we really want. These strings are returned as a two-element list which can be coerced into a hash by the first caller. Key collision should not be a problem since all the paths are different anyway.

If there are more nodes, we make a recursive call, starting at one of the potential vertices to visit next.

**Note:** the fact that the `%seen` hash is not a hash ref makes this all relatively painless: each successive call to this function can make changes all it wants to the hash. Those changes will only live in that call's copy. So if we are iterating over other potential vertices way up in the first vertex, we can go deep down one path, and when we move on to another vertex, the next call to the function will get a copy of the `%seen` hash with only the first vertex in it. And so on for other successive calls.

```perl
# Use a hash to save data on the longest word(s) found.
my %longest_word = ( paths => [], words => [], length => 0 );

# Iterate over all states in the graph.
for my $state ( sort $us_graph->vertices ) {

    # Finds all paths with strings that may be in the dictionary and
    # save them into a hash.
    my %paths = get_paths( $us_graph, $eng_words, $state, \%us_adj_data );

    # Set the trie to do exact string search only
    $eng_words->deepsearch("exact");

    # Iterate over all the path->word elements in the %paths hash
    while ( my ( $path, $search ) = each %paths ) {

        # Drop current path from the hash
        delete $paths{$path};

        # Do an exact string search for the word
        my $match = $eng_words->lookup($search);
        if ( $match && $match eq $search ) {

            # A word was found in the dictionary and its longer
            # than the longest seen so far. Clear the longest_word
            # hash and save the new data.
            if ( length($search) > $longest_word{length} ) {
                @longest_word{qw(paths words length)}
                    = ( [$path], [$match], length($search) );
            }
            elsif ( length($search) == $longest_word{length} ) {

                # A word of the same length as the longest seen
                # was found. Push the data onto the list members
                # of the longest_word hash.
                push $longest_word{paths}->@*, $path;
                push $longest_word{words}->@*, $match;
            }

            # last;
        }
    }

    # Change search setting again so we can do prefix searches on the
    # next iteration again.
    $eng_words->deepsearch("boolean");
}
```

Oh man this is a lot of stuff. The comments should help, however, and I will point out the important parts.

The code above iterates over all the states in the graph. For each state, we use the `get_paths` function to get all of the paths and generated words. The line 

```perl
$eng_words->deepsearch("exact");
```

sets searching the trie to look for exact matches only. It seems that this function doesn't really work as intended, but I need more time to test it and maybe report a bug. Anyway, this means that we can check to see if the word we generated is really a valid word, and not just a valid *prefix* of some word in the dictionary. If we did find an exact match, we save it in a similar way to the original challenge 2, except that now we also save the path.

Before moving on to the next state, we set the search back to "boolean", which simply returns "true" if the prefix is in the trie.

**Note 2:** In Challenge 2, I allowed words with apostrophes to be matched, but for some reason this resulted in some flaky output when using the trie. So for the additional challenge, I drop such words from the dictionary.

I am positive I over-thought and over-engineered this one, but it was fun to do it...once I got over the frustration of trying to use [`Graph::Traversal`](https://metacpan.org/pod/Graph::Traversal). It didn't do what I wanted, and `get_paths` above was my solution. That ended up being better anyway as I had control over which paths not to take.

With the wordlist dictionary on Arch Linux, this script gives the following output:

```
Longest word(s) constructed using initials of US states: 
CO->OK->NM->AZ->NV = conman
with a length of 6 alphabetical characters
```

and using the alphabetic-only list from https://github.com/dwyl/english-words

```
Longest word(s) constructed using initials of US states: 
CA->AZ->NV->UT->CO->KS = canuck
MO->AR->LA->MS->AL->GA = malmag
with a length of 6 alphabetical characters
```

**Cons:** Besides being an overcomplicated solution, building the trie can take a while if it is very large. I am sure that a large enough graph would also be a problem.

See the full solution, [here](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-014/yozen-hernandez/perl5/ch-2a.pl).

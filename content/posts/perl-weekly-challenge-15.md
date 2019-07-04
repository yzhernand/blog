---
title: "Perl Weekly Challenge 15: More Prime Sequences / Encrypt and Decrypt With the Vigenère Cipher"
date: 2019-07-01T14:45:21-04:00
draft: true
---

<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

This week's [Perl Weekly Challenge](https://perlweeklychallenge.org/blog/perl-weekly-challenge-015/) touched on prime number sequences again, and some old-school cryptography (which was pretty exciting).

# Challenge 1

>Write a script to generate first 10 strong and weak prime numbers.
For example, the *n*th prime number is represented by `p(n)`.
> 
>   p(1) = 2
>   p(2) = 3
>   p(3) = 5
>   p(4) = 7
>   p(5) = 11
> 
>   Strong Prime number `p(n)` when `p(n) > [ p(n-1) + p(n+1) ] / 2`
>   Weak   Prime number `p(n)` when `p(n) < [ p(n-1) + p(n+1) ] / 2`

Thanks to a [previous challenge](https://yzhernand.github.io/posts/perl-weekly-challenge-12/), I already had some code to generate prime numbers. However, that code only allowed me to iterate through a sequence, generating the next prime each time the iterator was called. This challenge requires either storing all of the values of the sequence, or quickly regenerating them, so that more than one value in the sequence can be tested.

Strong and weak prime numbers are defined by how close they are to their immediate neighbors in the sequence of prime numbers: strong primes are closer to the next number in the sequence, while weak primes are closer to the preceeding number (see the formulae above).

On my first attempt, I chose to generate the numbers on each strong/weak check. I used the [`Memoize`](https://metacpan.org/pod/Memoize) module so that this wouldn't be too expensive, time-wise:

```perl
use v5.24;
use strict;
use warnings;
use feature qw(say state signatures);
no warnings "experimental::signatures";
use Memoize;
use List::Util qw(first);

memoize('prime_gen');

sub is_prime {
    my $n = shift;

    return 0 if grep { $n % $_ == 0 } ( 2 .. sqrt($n) );

    return 1;
}

sub prime_iterator {
    my $n = 1;
    return sub {
        1 until is_prime ++$n;
        return $n;
        }
}

sub prime_gen ($n) {
    my $prime_iter = prime_iterator();

    # Throw away the first n-1 values
    $prime_iter->() for (1 .. ($n-1));
    return $prime_iter->();
}

sub is_strong_prime ($n) {
    return ($n > 1) && prime_gen($n) > ((prime_gen($n-1)+prime_gen($n+1))/2);
}

sub strong_prime_gen ($n) {
    my @strong_primes;
    my $i = 1;

    while (@strong_primes < $n) {
        my $p = prime_gen($i);
        push @strong_primes, $p if is_strong_prime($i);
        $i++;
    }

    return pop @strong_primes;
}

sub is_weak_prime ($n) {
    return ($n > 1) && prime_gen($n) < ((prime_gen($n-1)+prime_gen($n+1))/2);
}

sub weak_prime_gen ($n) {
    my @weak_primes;
    my $i = 1;

    while (@weak_primes < $n) {
        my $p = prime_gen($i);
        push @weak_primes, $p if is_weak_prime($i);
        $i++;
    }

    return pop @weak_primes;
}

say "Strong primes: ", join(", ", map {strong_prime_gen($_)} (1 .. 10));
say "Weak primes: ", join(", ", map {weak_prime_gen($_)} (1 .. 10));
```

Basically, I would call the `(strong|weak)_prime_gen` functions with the index of the prime I wanted in the sequence. Those functions call things like the prime number generating function `prime_gen`, and another function which checks if the number is in fact strong or weak. Those checking functions ALSO made calls to the `prime_gen` function, so I memoized it. As I was writing it, I thought I was being pretty clever...

However, it's pretty clear there are a bunch of issues here. First, while it is nice to clearly separate things and then put them all together exactly how you might need them, a lot of this code could just be merged together -- it's not going to be reused elsewhere. Also, the `(strong|weak)_prime_gen` functions look *really* similar. The only difference is the direction of the inequality check which was hidden away in that separate checking function. Lastly, this wastes time iterating over the same prime numbers twice, since first I call the `strong_prime_gen` function and then the `weak_prime_gen` function.

I could just iterate over a sequence of prime numbers, all the while making both necessary checks above, and storing the numbers I want to keep in the appropriate lists. I could also have a list of prime numbers since I need those anyway. These are all just ints, and for the challenge I know I only need 10 strong/weak primes anyway, so I don't mind the space trade-off.

So now I just need to consider the cases. As I iterate through the primes, I need to make the inequality checks above. I know I need to have at least 3 elements in my prime numbers sequence to even make such a comparison, so we'll account for that (maybe an `if (@primes > 2)` or something). Next, is the result of the comparison. A prime can either be greater than (strong), less than (weak) or equal to (balanced) the mean of its immediate neighbors. Hm...sounds a lot like comparisons made using the `<=>` or `cmp` operators, which return 1, -1, or 0, respectively, for each of those cases.

```perl
sub is_prime ($n) {
    return 0 if grep { $n % $_ == 0 } ( 2 .. sqrt($n) );

    return 1;
}

sub prime_iterator {
    my $n = 1;
    return sub {
        1 until is_prime ++$n;
        return $n;
    }
}

my ( @primes, @strong_primes, @weak_primes, @bal_primes );
my @which_arr = ( \@bal_primes, \@strong_primes, \@weak_primes );
my $iter      = prime_iterator();
my $n         = 10;

while ( ( @strong_primes < $n ) || ( @weak_primes < $n ) ) {
    push @primes, $iter->();
    if ( @primes > 2 ) {
        push $which_arr[ ( $primes[-2] <=> ( $primes[-3] + $primes[-1] ) / 2 )
        ]->@*, $primes[-2];
    }
}

say "First 10 weak primes: " . join( ", ", @weak_primes );
say "First 10 strong primes: " . join( ", ", @strong_primes );
```

Oh this is much better: far less code, far less repetition, and I don't even need to memoize. I took advantage of the return value of `<=>` by making a list of the references to the lists I needed, which I called `@which_arr`. The order is important, because I'm using the return value of `<=>` as an index into that array, so a `-1` (less than) result means that the value should go into the `@weak_primes` array, which means that the reference to it needs to be the last element of the array. That may be counterintuitive at first, but a `-1` index is the same as the last index since negative indices start from the end of the array.

Also probably counterintuitive is that the value we save is not the last value in the `@primes` sequence, but the second-to-last value, `$primes[-2]` since that is the last value which can have both to an immediately preceding and an immediately succeeding value.

See the full solution, [here](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-015/yozen-hernandez/perl5/ch-1.pl).

# Challenge 2

>Write a script to implement Vigenère cipher. The script should be able encode and decode. Checkout wiki [page](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher) for more information.

This seemed like a cool challenge to tackle. While reading the Wikipedia page, and knowing next to nothing about encryption, I was worried that this would take me all week. I actually spent less time on this challenge than I did on part 1!

Reading the [description](https://en.wikipedia.org/wiki/Vigenère_cipher#Description) section on Wikipedia of the Vigenère cipher, it became immediately obvious what must be done: using the numeric values for each letter in the alphabet, do some simple arithmetic to convert each letter in the message into a different letter determined by the numeric value of the key at a position. Keys can be shorter than the message, so you need to use modulo to wrap around the key when processing long messages.

Let's look at the code to see what I mean:

```perl
use v5.24;
use strict;
use warnings;
use feature qw(say state signatures);
no warnings "experimental::signatures";
use Carp;

my %tabula;
@tabula{ "A" .. "Z" } = ( 0 .. 25 );

sub encode_vigenere {
    my %args = @_;
    croak "Must supply the 'message' and 'key' arguments to this function.\n"
        unless ( exists $args{message} && exists $args{key} );

    my @message = split //, uc($args{message});
    my @key  = split //, uc($args{key});
    return join "", map {
        ( "A" .. "Z" )
            [ ( $tabula{ $message[$_] } + $tabula{ $key[ $_ % @key ] } ) %
            keys %tabula ]
    } ( 0 .. @message - 1 );
}

sub decode_vigenere {
    my %args = @_;
    croak "Must supply the 'message' and 'key' arguments to this function.\n"
        unless ( exists $args{message} && exists $args{key} );

    my @message = split //, uc($args{message});
    my @key  = split //, uc($args{key});
    return join "", map {
        ( "A" .. "Z" )
            [ ( $tabula{ $message[$_] } - $tabula{ $key[ $_ % @key ] } ) %
            keys %tabula ]
    } ( 0 .. @message - 1 );
}

my $message = $ARGV[0] // "ATTACKATDAWN";
my $key  = $ARGV[1] // "LEMON";

# Encrypt
my $encrypted = encode_vigenere( message => $message, key => $key );
say "Encrypted: $encrypted";

# Decrypt
my $decoded = decode_vigenere( message => $encrypted, key => $key );
say "Decrypted: $decoded";
```

I built a hash which mapped uppercase Latin letters to a numeric equivalent, and called that `%tabula` after the term *tabula recta* used by the people actually using this cipher. Now I've got a mapping between letters and numbers which matches the actual mapping used.

Next each function takes it's input, a *message* and a *key*, and splits each of these into arrays so we can iterate over them. A loop iterates over the message, and for each letter in the message, and in the case of encryption we subtract the value of the corresponding letter in the key. Again, since the key is usually shorter, we must use modulo arithmetic dependent on the length of the key to let us wraparound and reuse letters in the key. The new "shifted" value is used as an index into a list of uppercase letters from "A"-"Z", same as when we built the tabula.

Decryption works much the same, except that instead of subtracting the value obtained from the key, we add it. Knowing that, we could make the code even simpler, at the risk of making the interface a bit clunkier:

```perl
sub vigenere (%args) {
    croak "Must supply the 'message' and 'key' arguments to this function.\n"
        unless ( exists $args{message} && exists $args{key} );

    my @message = split //, uc($args{message});
    my @key  = split //, uc($args{key});
    my $decode = ( exists $args{decode} && $args{decode} != 0 ) || -1;
    return join "", map {
        ( "A" .. "Z" )[
            ( $tabula{ $message[$_] }
                    + ( ( -1 * $decode ) * $tabula{ $key[ $_ % @key ] } ) ) %
            keys %tabula
        ]
    } ( 0 .. @message - 1 );
}

my $message = $ARGV[0] // "ATTACKATDAWN";
my $key  = $ARGV[1] // "LEMON";

# Encrypt
my $encrypted = vigenere( message => $message, key => $key );
say "Encrypted: $encrypted";

# Decrypt
my $decoded = vigenere( message => $encrypted, key => $key, decode => 1 );
say "Decrypted: $decoded";
```

Supply any non-0 value to the `decode` option in the `vigenere` function to decode rather than encode. I think I would be happy with that, and we could always add wrapper functions to hide that away from the user and still cut down on code repetition:

```perl
sub encode_vigenere {
    my %args = @_;
    vigenere(%args, decode => 0);
}

sub decode_vigenere {
    my %args = @_;
    vigenere(%args, decode => 1);
}
```

Now, even if the user supplies some other value for `decode`, as long as they use the correct wrapper function, they will get the result they wanted.

See the full solution, [here](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-015/yozen-hernandez/perl5/ch-2.pl).
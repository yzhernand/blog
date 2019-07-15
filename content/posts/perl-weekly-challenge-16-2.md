---
title: "Perl Weekly Challenge 16: Decoding a Bitcoin Address in Perl"
date: 2019-07-12T13:25:49-04:00
draft: false
---

<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

The second challenge of this week's [Perl Weekly Challenge](https://perlweeklychallenge.org/blog/perl-weekly-challenge-016/) deals with Bitcoin, that ubiquitous digital currency we all have heard of, few totally understand, and some wish we had never invested in when we did.

>Write a script to validate a given bitcoin address. Most Bitcoin addresses are 34 characters. They consist of random digits and uppercase and lowercase letters, with the exception that the uppercase letter “O”, uppercase letter “I”, lowercase letter “l”, and the number “0” are never used to prevent visual ambiguity. A bitcoin address encodes 25 bytes. The last four bytes are a checksum check. They are the first four bytes of a double SHA-256 digest of the previous 21 bytes. For more information, please refer [wiki](https://en.bitcoin.it/wiki/Address) page. 

This sort of challenge was new to me. I never dealt with SHA hashes, decoding, or comparing checksums (at least not within Perl). I have done some programming involving byte-wise or bit-wise computation, but definitely not in Perl. It took a couple of hours reading things over in the wiki before I got a handle on what I needed to do.

Each bitcoin address is a number, though a rather large one, encoded in base-58 which just means there are 58 characters which are used to represent values (as opposed to just 10 in base 10, 0-9). The characters are, in increasing value:

```
123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnopqrstuvwxyz
```

As stated in the challenge, some characters are omitted since they can be visually confused for each other in some cases: capital letter "O", number "0", capital letter "I", and lowercase letter "l".

Now, having an idea of what characters represent what value, here are the steps, roughly, on decoding and validating a bitcoin address:

1. Iterate over each symbol in the input address string, convert to a numeric representation multiplied by the "place" of the symbol. Since addresses can be shorter or longer, iterate from right to left.
2. Add the value to a running total.
3. Post iteration, convert the numeric total to byte-representation.
4. If the address is shorter than the longest possible valid bitcoin address, left-pad with '0's.

Seems simple enough, right? Well I haven't read anyone else's blog posts yet, but I ran into a number of issues, which I will detail below.

As mentioned in point 1, we need to iterate over the string in the reverse sense, at least for clarity, since bitcoin addresses are at most 34 characters long, but can be shorter. Starting from the right lets us start a counter at 0 which increases with each leftward move along the string. That counter is used to multiply the value of the current character with the correct power of the base. If that's confusing, this is how you would do the same kind of decoding with base-10 numbers which seems so natural you probably don't realize you are doing it (unless you are a math/CS major I suppose): \\(1295 = 1\times10^3 + 2\times10^2 + 9\times10^1 + 5\times10^0\\).

Now to keep track of the running total of our bitcoin address. In my decoding function:

```perl
    my $val;
    my $base = 58;
    my @btc_arr = reverse split( //, $btc_addr );

    while ( my ( $i, $v ) = each @btc_arr ) {
        croak("Invalid bitcoin address") unless (exists $b58{$v});
        $val += $b58{$v} * ($base**$i);
    }
```

So that doesn't work. Wait, what?

I test the output of my functions as I write them using [`Test::More`](https://metacpan.org/pod/Test::More). Using an online [bitcoin address decoder](http://lenschulwitz.com/base58) and the [pack/unpack](https://perldoc.pl/functions/pack) functions (which I've only learned about thanks to these challenges), I compare what I get to the tool's output:

```
is(unpack("H*", b58_decode('1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2')), lc'0077BFF20C60E522DFAA3350C39B030A5D004E839AF415766B', "b58 decode works");
```

`unpack("H*", $blah)` converts the whatever bytes are in `$blah` according to the template `"H*"`, which means turn into a hex string (in [big-endian](https://en.wikipedia.org/wiki/Endianness) order). The return value of the function is converted into hex and then compared to the hex string I know to be the decoded hex value. I use lowercase to convert it since unpack returns a lowercase string.

The numeric value is turned into bytes and returned in the function more or less like this:

```perl
    my $decoded;

    # Bytes are 8-bit == 256 values
    while ($val >= 256) {
        # Get next lowest byte
        my $mod = $val % 256;

        # Prefix chr value of $mod to byte string
        $decoded = chr($mod) . $decoded;
        $val = int($val / 256);
    }

    $decoded = chr($val) . $decoded;

    #0-padding
    $decoded = (0 x (25-length($decoded))) . $decoded;
    return $decoded;
```

You might notice there are actually two problems here. But the first one is that Perl does not support "big" numbers out of the box. And bitcoin address values are indeed *very* big numbers. Ok, let's use [`Math::BigInt`](https://metacpan.org/pod/Math::BigInt). Problem solved, right?

```perl
sub b58_decode ($btc_addr) {
    my $val = Math::BigInt->new();
    my $base = Math::BigInt->new(58);
    my @btc_arr = reverse split( //, $btc_addr );

    while ( my ( $i, $v ) = each @btc_arr ) {
        croak("Invalid bitcoin address") unless (exists $b58{$v});
        $val = $base->copy()->bpow($i)->bmuladd($b58{$v}, $val);
    }

    # Decode value to bytes
    my $decoded = $val->to_bytes();

    # 0-padding
    $decoded = (0 x (25-length($decoded))) . $decoded;
    return $decoded;
}
```

Thanks to the `to_byte` method in Math::BigInt, I can quickly convert the value to the byte representation. But this still didn't work! Yeah, stupid mistake...I wanted the representation in bytes, and for that you have to use the `chr` function, not just a plain `0`:

```perl
    $decoded = (chr(0) x (25-length($decoded))) . $decoded;
```

My tests finally pass! Now validating doesn't take much at all:

```perl
sub btc_valid ($btc_addr) {
    my $decoded = b58_decode($btc_addr);

  # The last four bytes are a checksum check. They are the first four bytes of
  # a double SHA-256 digest of the previous 21 bytes.
    my $checksum = substr $decoded, -4;
    my $data     = substr $decoded, 0, 21;

    return ( $checksum eq substr(sha256( sha256($data) ), 0, 4) ) ? substr($decoded, 0, 1) : undef;
}
```

The core module [`Digest::SHA`](https://metacpan.org/pod/Digest::SHA) provides the hashing function, `sha256`. We use `substr` on the string returned by my decoding function to extract the bytes we want (this turns out to be faster than split/slice/join'ing), and compare the last 4 bytes to the double hash of the first 21 bytes.

The return value of the function upon success came from me looking at [this thread](https://bitcointalk.org/index.php?topic=1026.0) (suggested by the bitcoin wiki) and the solutions users there had in other languages. Personally, I think that the Perl solution using [`Math::BigInt`](https://metacpan.org/pod/Math::BigInt) looks a bit more compact than the solutions there, but I'm ready to be blown away by the answers from other challenge participants.

This was certainly a challenging problem to solve, but it forced me to use some parts of my brain and Perl I hadn't had to use before, and that might come in handy.

See the full solution, [here](https://github.com/manwar/perlweeklychallenge-club/tree/master/challenge-016/yozen-hernandez/perl5/ch-2.pl).
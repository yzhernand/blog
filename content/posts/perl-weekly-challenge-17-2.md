---
title: "Perl Weekly Challenge 17: Writing Our Own URL Parser in Perl (But Should We?)"
date: 2019-07-19T07:07:57-04:00
draft: false
---

The second challenge in this week's [Perl Weekly Challenge](https://perlweeklychallenge.org/blog/perl-weekly-challenge-017/) asks us to parse a URL:

> Create a script to parse URL and print the components of URL. According to [Wiki page](https://en.wikipedia.org/wiki/URL), the URL syntax is as below:
> 
> **scheme:[//[userinfo@]host[:port]]path[?query][#fragment]**
> 
> For example: **jdbc:mysql://user:password@localhost:3306/pwc?profile=true#h1**
 
```
scheme:   jdbc:mysql
userinfo: user:password
host:     localhost
port:     3306
path:     /pwc
query:    profile=true
fragment: h1
```



Sounds good. Let's try to parse this out. Maybe we'll use a few regex's, and that'll be it.

```perl
sub parse_url_regex($url) {
    my %parsed;

    while ($url) {
        if ( !exists $parsed{scheme} ) {
            $url =~ s!^((?:[[:alnum:]]+:)?[[:alnum:]]+):!!;

            # MUST start with the scheme, format: "scheme:"
            croak "Invalid format: url must start with scheme." unless $1;
            $parsed{scheme} = $1;

            return \%parsed unless $url =~ s!^//!!;
        }
        elsif ( !exists( $parsed{host} )
            && $url
            =~ s!^(?:((?:[\d\w]+:)?(?:[\d\w]+)?)@)?([\d\w\.]+)(?::([\d]+))?!!u
            )
        {
            $parsed{userinfo} = $1 // "";
            $parsed{host} = $2 // "";
            $parsed{port} = $3 // "";
        }
        elsif ( !exists( $parsed{path} )
            && $url =~ s!^/((?:[\d\w\.\/]*)+)!!u )
        {
            $parsed{path} = "/" . ( $1 // "" );
        }
        elsif ( !exists( $parsed{query} )
            && $url =~ s!^\?((?:[\d\w\[\]%\"\']+)=(?:[\d\w\[\]%\"\']+))*!!u )
        {
            $parsed{query} = $1 // "";
        }
        elsif ( !exists( $parsed{fragment} )
            && $url =~ s!^#([\d\w\[\]%\"\']+)!!u )
        {
            $parsed{fragment} = $1 // "";
        }
        else {
            croak "Error: Invalid URL? $url";
        }
    }

    return \%parsed;
}
```

Well that's a lot of `(els)if`s and regex's. I'm not sure I'd want to maintain that. Plus, I'm sure it's full of bugs.

```perl
sub print_parsed ($url_hash_ref) {
    for my $part (qw(scheme userinfo host port path query fragment)) {
        say "$part:\t" . $url_hash_ref->{$part}
            if exists $url_hash_ref->{$part}
            && defined $url_hash_ref->{$part};
    }
    say "";
}

print_parsed(
    parse_url_regex(
        q"jdbc:mysql://user:password@localhost:3306/pwc?profile=true#h1")
);
#scheme: jdbc:mysql                                      
#userinfo:       user:password
#host:   localhost
#port:   3306
#path:   /pwc
#query:  profile=true                         
#fragment:       h1
print_parsed(
    parse_url_regex(q"http://sri:foo@example.com:3000/foo?foo=bar#23") );
#scheme: http
#userinfo:       sri:foo
#host:   example.com
#port:   3000
#path:   /foo
#query:  foo=bar
#fragment:       23
print_parsed( parse_url_regex(q"https://example.com/") );
#scheme: https
#userinfo:
#host:   example.com
#port:
#path:   /
print_parsed(
    parse_url_regex(
        q"http://JP納豆.例.jp:80/dir1/引き割り.html?q=クエリ#メイン"
    )
);
#scheme: http
#userinfo:
#host:   JP納豆.例.jp
#port:   80
#path:   /dir1/引き割り.html
#query:  q=クエリ
#fragment:       メイン
```

These all look correct. But there must be some errors. For one, blank fields should probably just be left as `undef` (like `userinfo`), but for now I've left them as empty strings. More seriously, this doesn't even try to be secure and I wouldn't recommend using it in production.

This is the sort of problem that I would rather reach for a well-established module if I ever needed to do this in production. So let's use a module I know that does that job well, [`Mojo::URL`](https://mojolicious.org/perldoc/Mojo/URL):

```perl
use Mojo::URL;
use Mojo::Util qw(decode url_unescape);

use Test::More tests => 7;
my $parsed_regex
    = parse_url_regex(
    q"http://JP納豆.例.jp:80/dir1/引き割り.html?q=クエリ#メイン"
    );
my $parsed_mojo
    = Mojo::URL->new(
    q"http://JP納豆.例.jp:80/dir1/引き割り.html?q=クエリ#メイン"
    );
is( $parsed_mojo->scheme,
    $parsed_regex->{scheme},
    "Mojo and regex sub agree on 'scheme'"
);
is( $parsed_mojo->userinfo // '',
    $parsed_regex->{userinfo},
    "Mojo and regex sub agree on 'userinfo'"
);
is( $parsed_mojo->host, $parsed_regex->{host},
    "Mojo and regex sub agree on 'host'" );
is( $parsed_mojo->port, $parsed_regex->{port},
    "Mojo and regex sub agree on 'port'" );
is( decode( 'UTF-8', url_unescape( $parsed_mojo->path ) ),
    $parsed_regex->{path}, "Mojo and regex sub agree on 'path'" );
is( decode( 'UTF-8', url_unescape( $parsed_mojo->query ) ),
    $parsed_regex->{query},
    "Mojo and regex sub agree on 'query'"
);
is( $parsed_mojo->fragment,
    $parsed_regex->{fragment},
    "Mojo and regex sub agree on 'fragment'"
);
```

```
ok 1 - Mojo and regex sub agree on 'scheme'
ok 2 - Mojo and regex sub agree on 'userinfo'
ok 3 - Mojo and regex sub agree on 'host'
ok 4 - Mojo and regex sub agree on 'port'
ok 5 - Mojo and regex sub agree on 'path'
ok 6 - Mojo and regex sub agree on 'query'
ok 7 - Mojo and regex sub agree on 'fragment'
```

Looks like we did pretty well! You'll notice that since the URL I chose to use includes non-ASCII characters, and `Mojo::URL` returns strings that are [URL-encoded](https://en.wikipedia.org/wiki/Percent-encoding), I had to unescape the URLs and then decode the unicode strings in order to compare them to what my code returned. Also, since I define missing fields as the empty string instead of setting them to `undef`, I had to use `// ''` for the `userinfo` attribute from `Mojo::URL`.

Ok so we don't seem to run into any errors, and we actually agree (where it counts?) with an established module. Surely there must be some performance cost to doing it this way, right? Let's go back to our trusty [`Benchmark::Forking`](https://metacpan.org/pod/Benchmark::Forking) module:

```
                       Rate               mojo from_scratch_regex
mojo                31204/s                 --               -72%
from_scratch_regex 110892/s               255%                 --
```

Whaaa? We do about 255% better than `Mojo::URL`? On second thought, that probably makes sense. I'm sure a whole lot more going on inside that module to make sure things are way safer than any thing my code does, and it implements a whole class with stringification, etc.

So, it passes all my tests, seems to agree with a well-established module, and even beats it in speed. This is ready to use in your, or my, next project right??

No way! First off, I did NOT run exhaustive tests. Also, I'm basically some random person off the internet who wrote this in a couple of hours.

Don't try to write your own URL parser for an important application unless you really understand what "RFC 3986, RFC 3987 and the URL Living Standard for Uniform Resource Locators with support for IDNA and IRIs" means. Go with something like `Mojo::URL` instead.

Fun side project? Sure.

See the full solution as a Gist, [here](https://gist.github.com/yzhernand/7fccc513bfd06febf946d228c21f9b5d).
How to do a fast, full-text search of man pages?
================================================

It is surprisingly non-obvious!

`man -K foo` takes forever and makes you review each result one after another,
as they are found, while I just want a quick list of pages containing `foo`.

After duckduckgoing it, the closest thing I saw was someone using Elasticsearch
and Ruby to make this happen - a huge, unsatisfying overkill for this humble
task, IMHO...

So, how to make it happen with just some basic system tools? Let's find out!

My conversation with my inner voice went something like this:

- Well, what the heck is an index anyways?
- Well, what do I expect from it?
- I expect to give it a word and get back list of manpage names.
- OK, so it is a dictionary.
- Yeah...
- I have `page -> [word]` and I want to reverse it to `word -> [page]`.
- Yeah... sounds right...
- Dictionaries... Sounds like AWK!
- We'll first need to parse whatever format they're in, but I guess AWK is
  again The Man here...
- Yeah... but... something already parses them...
- `man troff`
- `-a        Generate an ASCII approximation of the typeset output.`
- Sweet! Sounds good-enough for the experiment - let's go!

... a couple of hours later, we have our rough beast ...

It takes 2-3 minutes to build the index file and another 20-30 seconds
to load it into memory:

```sh
$ time ./index_all && ./lookup
./index_all  507.14s user 62.36s system 377% cpu 2:31.02 total
Loading ./index.dat...
Loading completed in 24 seconds.
records: 10274801, words: 161085, pages: 10539.
?
```

The search results, however, are pretty much instantaneous.

Frankly, I now forgot what I originally wanted to search for, so just to
lighten the mood a bit - I decided to look for some lolz, which, to my genuine
surprise, I actually found:

```sh
? poop
==> results: 0
? coprolite
==> results: 0
? feces
==> results: 0
? shit
==> results: 1
      1) common::sense.3pm
? fuck
==> results: 2
      1) EV::libev.3pm
      2) common::sense.3pm
? bitch
==> results: 0
? balls
==> results: 4
      1) fluidballs.6x
      2) attraction.6x
      3) ppmforge.1
      4) discover.1
? dick
==> results: 4
      1) zip.1
      2) mathspic.1
      3) perlmod.1
      4) perlref.1
? penis
==> results: 1
      1) AnyEvent::Impl::POE.3pm
? vagina
==> results: 0
? ass
==> results: 4
      1) youtube-dl.1
      2) twang.6x
      3) mpv.1
      4) mplayer.1
?
```

... you get the idea - the sky is the limit for hidden-gem exploration here...

Potential improvements
----------------------

- make it available as a server, so we can slow-load just once and fast-search
  many times from many shells (this should actually be quite little additional
  work, since `gawk` implements networking...)
- implement a suffix tree, so we can do substring searches

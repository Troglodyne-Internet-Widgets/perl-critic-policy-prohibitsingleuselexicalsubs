# NAME

Perl::Critic::Policy::Subroutines::ProhibitSingleUseLexicalSubs - Inline a lexical sub that is used in only one place.

# VERSION

version 0.001

# Perl::Critic::Policy::Subroutines::ProhibitSingleUseLexicalSubs

A lexical sub that one place uses is that place, moved somewhere else.  The
reader goes to find it, reads it, and comes back.  Put the body where it is
used, and the reader reads it once, in order:

```perl
my sub total { return sum map { $_->{price} } @_ }    # reported
say total(@items);

say sum map { $_->{price} } @items;

my $by_name = sub { $a->{name} cmp $b->{name} };       # reported
my @sorted  = sort $by_name @people;

my @sorted = sort { $a->{name} cmp $b->{name} } @people;
```

A name still earns its place when two or more places use it.

## PROHIBITED

```perl
my sub helper { ... }            helper(@args);
state sub helper { ... }         helper(@args);
my sub helper { ... }            my $ref = \&helper;
my $helper = sub { ... };        $helper->(@args);
my $helper = sub { ... };        $self->$helper(@args);
my $helper = sub { ... };        some_function($helper);
```

## ALLOWED

```perl
my sub helper { ... }            helper(1); helper(2);
my sub walk { ... walk(@kids) }  walk($root);     # it calls itself
my sub helper { ... }                             # unused: see CAVEATS
sub helper { ... }               helper(@args);   # a package sub
our $helper = sub { ... };       $helper->();     # a package variable
```

# CAVEATS

A sub that nothing uses is not reported.  `Subroutines::ProhibitUnusedPrivateSubroutines`
and the policies about unused variables report that, and it wants deleting
rather than inlining.

The count is of the code after the declaration, in the same block, as PPI
parses it.  A use inside a string, such as `"@{[ helper() ]}"`, is not
counted, so a sub used there and in one other place is reported.

If the block declares the same name again, the policy cannot tell which
declaration a use refers to, and it reports nothing for that name.

A sub passed as a callback, such as `find({ wanted => $wanted }, $dir)`,
is inlined as an anonymous sub at that place.

## METHODS

### supported\_parameters

### default\_severity

### default\_themes

### applies\_to

### violates

# AUTHORS

Current Maintainers:

- George S. Baugh <george@troglodyne.net>

# COPYRIGHT AND LICENSE

Copyright (c) 2026 Troglodyne LLC

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

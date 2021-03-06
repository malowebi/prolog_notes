# Some notes on _length/2_

This is a companions page for the SWI-Prolog entry on the predicate [`length/2`](https://eu.swi-prolog.org/pldoc/doc_for?object=length/2)

## Standard behaviour

### Generate a list of unbound variables

**A completely unintuitive trick for the beginner, but very useful**

Generate a "template list" of length 5 (say) holding only unbound variables. Directly 
using `length/2`. This is done by stating the length of the list and leaving 
the list an unbound variable. That unbound variable is "further instantiated" to the 
"template list":

```
?- 
length(List,5).
List = [_1848, _1854, _1860, _1866, _1872].
```

More generally, `length/2` generates longer and longer "template list" and their associated
integer length value when given only unbound variables as arguments:

```
?- 
length(List,Length).
List = [], Length = 0 ;
List = [_4066], Length = 1 ;
List = [_4066, _5194], Length = 2 ;
List = [_4066, _5194, _6322], Length = 3 ;
List = [_4066, _5194, _6322, _7450], Length = 4 ;
List = [_4066, _5194, _6322, _7450, _8578], Length = 5 
...
```

Here's a party trick: Using `length/2` to generate monotonically increasing integers
from 1 onwards. Do not use this, use `between(1,inf,N)` from [`between/3`](https://eu.swi-prolog.org/pldoc/doc_for?object=between/3) instead.

```
?- length([_|_],Length).
Length = 1 ;
Length = 2 ;
Length = 3 ;
Length = 4 ;
Length = 5 
...
```

### See also

[`same_length/2`](https://eu.swi-prolog.org/pldoc/doc_for?object=same_length/2) - useful if you have the length of two lists to compare.

### Unit test code!

This demonstrates functionality of `length/2` in executable format:

[Unit testing length/2](code/test_length.pl)

_Hopefully in the SWI-Prolog distribution soon, as `src/Tests/core/test_length.pl`_ ?

There already is a bunch of test cases for `length/2` in the distribution, in `src/Tests/core/test_bips.pl` in plunit `length`.

## Compared to the ISO Standard

Ulrich Neumerkel has a page on variant behaviour of `length/2` across various implementations:

[ISO/IEC JTC1 SC22 WG17: Comparison of implementations of length/2 and atom_length/2](https://www.complang.tuwien.ac.at/ulrich/iso-prolog/length)

The latest SWI-Prolog's behaviour seem to differ somewhat from the table.

The above can be nicely reformatted into unit test code!

[test_length_against_iso_prolog_wg17.pl](code/test_length_against_iso_prolog_wg17.pl)

**Note this little inconsistency:**

[`atom_length/2`](https://eu.swi-prolog.org/pldoc/doc_for?object=atom_length/2)
**fails** on negative length whereas `length/2` **throws** (at least in SWI-Prolog)

```
?- atom_length(a,-1).
false.
```

```
?- length([a],-1).
ERROR: Domain error: `not_less_than_zero' expected, found `-1'
```

- The expected ISO standard behaviour for `length/2` is to throw.
- Apparently the latest ISO standard says that `atom_length/2` should also throw here (GNU Prolog throws). The 1995 standard still says to fail.

Pragmatically, the decision whether to fail or throw (incl. _what_ to throw) should depend on the situation:

- _throw_ when you are in a section of code where you prefer to think "computationally", but
- _fail_ when you are in a section code where you prefer to think "logically". 

As such, there should be an option to control behaviour. See `probe_length/3` below.

### More reading

See also the description of length/2 in "A Prologue for Prolog (working draft)"

http://www.complang.tuwien.ac.at/ulrich/iso-prolog/prologue#length

## Variants

### A _length/3_ which can behave more leniently

For more flexible behaviour, here is a `length/3` which has a third parameter to select behaviour:

- Behave exactly like the SWI-Prolog length by directly calling it: `length(List,Length,swi)` or `length(List,Length,[swi])` or `length(List,Length,[])` (`swi` wins if it's first in the options list, and is otherwise the default behaviour)
- Behave leniently and never throws, only fails: `length(List,Length,lenient)` or `length(List,Length,[lenient])` (`lenient` wins if it's first in the options list)
- Behave more strictly than SWI-Prolog's `length/2`: `length(List,Length,strict)` or `length(List,Length,[strict])` (`strict` wins if it's first in the options list) -- Actually it behaves exactly the same as the SWI-Prolog `length/2` for now, and this will probably not change.

**Code**

   - [Code](/code/heavycarbon/utils/lenient_length.pl) ([raw code](https://raw.githubusercontent.com/dtonhofer/prolog_notes/master/code/heavycarbon/utils/lenient_length.pl))
   - New to modules? [TL;DR for installation](/code/heavycarbon/utils/TLDR_lenient_length.txt)

**Example**

```
?- length(L,-1,lenient).
false.

?- length(L,-1,strict).
ERROR: Domain error: `not_less_than_zero' expected, found `-1'

?- length(L,-1,swi).
ERROR: Domain error: `not_less_than_zero' expected, found `-1'
```

### Rolling an "extended _length_": _probe_length/3_

So how do we probe the length of a open list, i.e. a list ending in an unbound variable, like `[1,2,3|_]`?

Here is a predicate which can do that `probe_length/3`, `probe_length/4`.

```
probe_length(@MaybeList, ?Length, ?What)
probe_length(@MaybeList, ?Length, ?What, @Options)
```

`probe_length∕3` and `probe_length∕4` probe the length of an potentially open list without modifying it and tell you what the
actual class of this maybe-list object is!

**Code**

   - [Code](/code/heavycarbon/utils/probe_length.pl) ([raw code](https://raw.githubusercontent.com/dtonhofer/prolog_notes/master/code/heavycarbon/utils/probe_length.pl))
   - [Unit tests](/code/heavycarbon/utils/probe_length.plt) ([raw code](https://raw.githubusercontent.com/dtonhofer/prolog_notes/master/code/heavycarbon/utils/probe_length.plt))
   - New to modules? [TL;DR for installation](/code/heavycarbon/utils/TLDR_probe_length.txt)
   
**Examples**

```
?- probe_length([],Length,What). 
Length = 0, What = closed.
```

```
?- probe_length([a,b,c],Length,What).
Length = 3, What = closed. 
```

```
?- probe_length(_, Length, What).
Length = 0, What = var.
```

```
?- probe_length([a,b,c,d|foo], Length, What).
Length = 4, What = nonlist.
```

Propose a length:

```
?- probe_length([a,b,c],3,What).
What = closed.
```

```
?- probe_length([a,b,c],10,What). 
false.
```

Bad length - generally just fail on negative length:

```
?- probe_length([a,b,c],-1,What). 
false.
```

But you can select to throw instead:

```
?- probe_length([a,b,c],-1,What,strict). % You can also use a list: '[strict]'
ERROR: Type error: `nonneg' expected, found `-1' (an integer)
```

The length always must be an integer:

```
?- probe_length([a,b,c],3.0,What). 
ERROR: Type error: `integer' expected, found `3.0' (a float)
```

It can handle a "non-list". Here, a non-list with a "list-like" prefix of length 1

```
?- probe_length([a|x], Length, What). 
Length = 1, What = nonlist.
```

An unbound veriable is special:

```
?- probe_length(_, Length, What).
Length = 0, What = var.
```

Propose a class for `MaybeList`, maybe multiple classes. In the latter case, it's ok if any class matches:

```
?- probe_length([a,b,c,d|foo], Length, nonlist).
Length = 4.
```

```
?- probe_length([a,b], Length, [closed,var]).
Length = 2.
```

```
?- probe_length(_, Length, [closed,var]).
Length = 0.
```

```
?- probe_length([a,b,c|_], Length, [closed,var]).
false.
```

## Efficiency of _length/2_

Implementation-wise, does `length/2` scan the whole "backbone" of the passed list or does it just need to 
read an integer at the tip? Or in other words, is `length/2` _O(1)_ or _O(n)_ with _n_ the length of the list? 

*Answer found by clicking on the `:-` at the top right of the manual page*: At least in this Prolog, it is _O(n)_. 

I suppose the functions '$length'/2, '$length3'/3, '$skip_list/3' are the C API.

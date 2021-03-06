# Notes regarding various elements of Prolog / SWI-Prolog

## Note on how code is shown

In all cases, examples to be entered on the command line are written with a preceding `?-` on a dedicated line.
This avoid having to deploy precise motor skills to capture the part of the text one wants to copy-paste.
The command is separated from the following output by a single line.

```none
?- 
findall(found(X,Y),(member(X,[1,2]),member(Y,[3,4])),Bag).

Bag = [found(1, 3), found(1, 4), found(2, 3), found(2, 4)].
```

Sadly, there is no special markup for code that should be entered in `[user].` mode. Nnor is there a special
way to tell prolog that the it should consider incoming text as a program. A surrounding `[user/] .... [/user]` would be nice. Maybe.

The coloring used for code markup is consistently set to "none". It's the least distracting.

Using "logtalk" coloring:

```logtalk
% Logtalk coloring X=2
/* Logtalk coloring */
findall(found(X,Y),(member(X,[1,2]),member(Y,[3,4])),Bag).
```

Using "prolog" coloring:

```prolog
% Prolog coloring X=2
/* Prolog coloring */
findall(found(X,Y),(member(X,[1,2]),member(Y,[3,4])),Bag).
```

Using "erlang" coloring:

```erlang
% Erlang coloring X=2
/* Erlang coloring */
findall(found(X,Y),(member(X,[1,2]),member(Y,[3,4])),Bag).
```

Using "none" coloring:

```none
% None coloring X=2
/* None coloring */
findall(found(X,Y),(member(X,[1,2]),member(Y,[3,4])),Bag).
```

## Pages reviewed so far

Listed based on the entry in the SWI-Prolog manual:

- [`between/3`](https://eu.swi-prolog.org/pldoc/doc_for?object=between/3)
   - [Notes about `between/3`](about_between/), includes code for `between_with_step/4` and `between_x/3` 
   - [Website text](about_between/webmanualtxt/between.txt)

- [`findall/3`](https://eu.swi-prolog.org/pldoc/doc_for?object=findall/3)
   - [Notes about `findall/3`](about_findall/), also about `bagof/3`.

- [`length/2`](https://eu.swi-prolog.org/pldoc/doc_for?object=length/2)
   - [Notes about `length/2`](about_length/), includes code for `probe_length/3` and (lenient) `length/3`
   - [Website text](about_length/webmanualtxt/length.txt)

- [`maplist/2`](https://eu.swi-prolog.org/pldoc/doc_for?object=maplist/2), [`maplist/3`](https://eu.swi-prolog.org/pldoc/doc_for?object=maplist/3), [`maplist/4`](https://eu.swi-prolog.org/pldoc/doc_for?object=maplist/4)
   - [Notes about `maplist/2`](about_maplist/maplist_2_examples.md) Working off a single list. This includes   
     an explainer regarding the use of [`library(yall)`](https://eu.swi-prolog.org/pldoc/man?section=yall) 
     lambda expressions and Prolog-style closures.
   - [Notes about `maplist/3`](about_maplist/maplist_3_examples.md) Relating the elements of two lists (to be reviewed)
   - [Notes about `maplist/4`](about_maplist/maplist_4_examples.md) Relating the elements of three list (to be reviewed)
   
- [Verify type of a term](https://eu.swi-prolog.org/pldoc/man?section=typetest) and [`must_be/2`](https://eu.swi-prolog.org/pldoc/doc_for?object=must_be/2)
   - [Notes](about_swipl_data_types/), includes image of the decision tree
   - [Website text](about_swipl_data_types/webmanualtxt/type_tree_in_ascii.txt)
   - [Better type tests](about_type_tests/)
   
- The [`->/2`](https://eu.swi-prolog.org/pldoc/doc_for?object=(-%3E)/2) (if-then-else) and [`*->/2`](https://eu.swi-prolog.org/pldoc/doc_for?object=(*-%3E)/2) (soft-cut)
   - [Notes about `->/2` and `*->/2`](about_if_then_else/)

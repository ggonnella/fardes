# Specification

The _fardes_ (feature arrangement description) miniformat consists in a list of
named feature (identified by a feature ID) and, optionally, interval specifiers
(which describe what is in between two named features). The list starts and
ends with a feature ID and interval specifiers always occur between two feature
IDs.

## Named feature

The named features are the features which are explicitely named
using their IDs in the list.

The feature IDs must consist of only letters, numbers and underscores and
start with a letter. It can have a prefix and/or a suffix symbol.

To express the strand constraints, the prefixes ``>``, ``^`` and ``=``
are used (explained below). The first named feature ID
of the list is never prefixed.

### Optional named feature

To express that a feature is optionally present, a ``?`` symbol is used
as a suffix.

## Interval specifiers

Interval specifiers are used to describe the interval between two named
features, in terms of length, number of features, their type and strand.

Interval specifiers generally have the form ``a:b(type)[c:d]``, but can also
have some special values or be shortened, as described later. Thereby the
``a:b`` part expresses the number of features and ``type`` the feature type of
the features in the interval between two identified features and the c:d part
the length of that interval.

In some cases, no interval specifier is used, and two named features just
follow each other in the list. In this case the interval specifier
``0:0[0:*]`` is implied (i.e. there are no other features between the two
named features and there is no constraint regarding the inter-feature interval
length in between).

### Number of features in the interval

The number of features in the interval between two named features are described
by the ``a:b`` part of the interval specifier. Thereby a and b are unsigned
integers, the minimum and maximum number of features in the interval.
If ``a == b`` then ``b`` can be omitted (including the ``:`` separator before
it). If there shall be no restriction on the maximum number of features, ``*``
is used instead of a number for it. It is also possible to give a single number
preceded by ``<``, ``<=``, ``>``, ``>=`` to indicate that the number of
features shall be less than, less equal, larger than, or larger equal that
number.

If any number of features may be present but the interval length shall be
described, the entire ``a:b`` part can be omitted and an interval specifier
consisting of only a length specifier (``[c:d]``) used. Instead if _no_ feature
shall be present, but the interval length shall be described a ``0`` is used,
i.e. the entire interval specifier will be in the form ``0[c:d]``.

### Type of features in the interval

It is possible to specify which type the features may have, which are located
in the interval between two named features. For this, the feature types are
included in round parentheses after the number. If multiple types are possible,
they can be indicated all using a ``;`` as separator, e.g. ``3:4(rRNA;tRNA)``.

It is possible to specify the type of the features also when omitting the
number. I.e. ``A,(tRNA),B`` means that there is none or any number of features
between A and B, and if there are some, they are of the type tRNA.

### Length of the interval

The length of the interval is described in the ``[c:d]`` part. It can be
omitted completely, if the length shall not be constrained (equivalent to
``[0:*]``). Thereby ``c`` and ``d`` are unsigned integers, optionally followed
by a unit symbol and express the minimum and maximum length. The following unit
can be used: "b"/"bp" for base pairs, "kbp" or "kb" for thousands of base pairs
"Mb"/"Mbp" for millions of base pairs, "Gbp" or "Gkb" for billions (``10^9``)
of base pairs. If ``c == d`` then ``d`` can be omitted (including the ``:``
separator before it). If there shall be no restriction on the maximum number of
features, a ``*`` is used instead of ``d``. It is also possible to give a
single value preceded by ``<``, ``<=``, ``>``, ``>=`` to indicate that the
length of the interval shall be less than, less equal, larger than, or larger
equal that value. If a length prefixed by ``~`` is given, then it is an
approximate interval length (no exact minimum and maximum are given in this
case).

### Optional features and interval specifiers

If interval specifiers are present before and/or after optional
features, they still apply if those features are absent. The only
exception are if the optional features are the first and last feature,
in which case they are ignored (for simplicity).
If both before and after the feature an interval is described, then
their sum will be used, in case of absence of the optional feature.
This principle also applies to multiple consequent optional features.

Examples:
- ``A,1,B?,C`` becomes ``A,1,C``, if ``B`` is absent.
- ``A,1,B?,2,C`` becomes ``A,3,C``, if ``B`` is absent.
- ``A,1,B?,1,C?,1,D`` becomes ``A,3,D`` if ``B`` and ``C`` are absent.
- ``A?,1,B,2,C`` becomes ``B,2,C`` if ``A`` is absent.
- ``A,1,B,2,C?`` becomes ``A,1,B`` if ``C`` is absent.

## Special interval specifiers

In some cases, described here, the interval specifier is not given as
a range of number of features and/or lengths, but a special
symbol is used instead.

### Near or distant features

In some cases, it shall only be expressed that two features are close
or far from each other, without any concrete specification in terms
of number of features in between or length of the interval.

In such cases the symbols ``<>`` for distant and ``><`` for near
are used as an interval specifier.

### Same or different molecule

By default, two features are supposed to be on the same replicon
(e.g. chrosomome or plastid).
If the ``<>`` specifier is used, then the features are supposed
to be distant from each other, but it is not described if they
are on the same or different molecules.
To specify this, the symbols ``<|>`` for different molecules
or ``<.>`` for same molecule can be used for distant features,
instead of ``<>``.

### Feature overlap

In some cases, not only there is no interval between two features,
but they even overlap (on the same or, often, on opposite strands).
In such cases the ``&`` symbol is used as interval specifier.

## Relative strand arrangement

The strand selection chars are used in order to specify a relative strand
arrangement. By default the strand arrangement is expressed as
relative to the first feature in the list.
To change the reference point, a feature can be prefixed by the
``>`` character.
Then all following strand specifiers will use that feature
as reference

The strand specification is given by prefixing a feature ID by
the ``=`` character, meaning same strand as the reference feature,
or the ``^`` character, meaning opposite strand to that of the
reference feature.

Since the first unit of the list have no reference feature (and is instead
the default reference feature until ``>`` is found), then its ID cannot
be prefixed by any symbol.


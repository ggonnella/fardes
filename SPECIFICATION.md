# Specification

The _fardes_ (feature arrangement description) miniformat
consists in a list of feature IDs and, optionally,
interval specifiers (which describe what is in between two features).
The list starts and ends with a feature ID and interval specifiers
always occur between two feature IDs.

## Feature IDs

The feature IDs must consist of only letters, numbers and underscores and
start with a letter. To express the strand constraints, they can sometimes
be prefixed by a strand selection character (which is explained later),
i.e. one of ">", "^" and "=".
The first feature ID of the list is never prefixed by a strand selection
character.

## Interval specifiers

Interval specifiers generally have the form "a:b(type)[c:d]", but can also have some
special values or be shortened, as described later. Thereby the a:b part
expresses the number of features and type the feature type of the features
in the interval between two identified features and the c:d part the length of that interval.

In some cases, no interval specifier is used, and two identified features just
follow each other in the list. In this case the interval specifier
``0:0[0:*]`` is implied (i.e. there are no other features between the two
features with the given IDs and there is no constraint regarding the
inter-feature interval length in between).

### Number of features in the interval

The number of features in the interval are described by the a:b part of
the interval specifier.
Thereby a and b are unsigned integers, the minimum
and maximum number of features in the interval. If ``a == b`` then ``b``
can be omitted (including the ``:`` separator before it). If there shall
be no restriction on the maximum number of features, ``*`` is used instead
of a number for it. It is also possible to give a single number preceded
by ``<``, ``<=``, ``>``, ``>=`` to indicate that the number of features
shall be less than, less equal, larger than, or larger equal that number.

If any number of features may be present but the interval
length shall be described, the entire ``a:b``
part can be omitted and an interval specifier consisting of only a length
specifier (``[c:d]``) used. Instead if _no_ feature shall be present, but the interval
length shall be described a ``0`` is used, i.e. the entire
interval specifier will be in the form ``0[c:d]``.

### Type of features in the interval

It is possible to specify which type the features in the interval may have.
For this, the feature types are included in round parentheses after
the number. If multiple types are possible, they can be indicated all
using a ``;`` as separator, e.g. ``3:4(rRNA;tRNA)``.

### Length of the interval

The length of the interval is described in the ``[c:d]`` part.
It can be omitted completely, if the length shall not be constrained
(equivalent to ``[0:*]``).
Thereby ``c`` and ``d`` are unsigned integers, optionally followed by a unit symbol
and express the minimum and maximum length. The following unit can be
used: "b"/"bp" for base pairs, "kbp" or "kb" for thousands of base pairs
"Mb"/"Mbp" for millions of base pairs, "Gbp" or "Gkb" for billions (``10^9``)
of base pairs.
If ``c == d`` then ``d``
can be omitted (including the ``:`` separator before it). If there shall
be no restriction on the maximum number of features, a ``*`` is used instead
of ``d``. It is also possible to give a single value preceded
by ``<``, ``<=``, ``>``, ``>=`` to indicate that the length of the interval
shall be less than, less equal, larger than, or larger equal that value.
If a length prefixed
by ``~`` is given, then it is an approximate interval length
(no exact minimum and maximum are given in this case).

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


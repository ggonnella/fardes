# Fardes: Features Arrangement Description Miniformat

The mini-format described here allows to describe the relative
arrangement of named sequence features on one or multiple molecules,
in terms of their order, length of the interval between them,
possible presence of further features between them, strand,
position on the same or different molecule.

It was developed for an application in which the expected genome contents
of prokaryotic genomes is expressed as a set of rules, which
in some cases concern the relative arrangement of features.

The miniformat is described in the Markdown document ``SPECIFICATION.md`` in
this repository.

## Implementation

The miniformat has been implemented as a TextFormats specification
(``fardes.tf.yaml``).

This has been included in a Python module ``fardes``, which additionally include
cross-checking not expressable in TextFormats and normalizes the elements
while parsing a string
(e.g. by including implicit values and applying multipliers).
The module can be installed using ``pip``.

Here is an example of usage of the module:
```
import fardes
elements = fardes.parse("A,1:10[1kb:3kb],>B,1,>C,1[2],>D,=E,[3:*],F,1:*[>2Mb],G,<>,H,>0,I,<4,J,[~3kb],K,<|>,L,><,M,&,N")
```

will result in the following:

```
[{'prefix': '', 'type': 'unit', 'unit': 'A'},
 {'length': {'max': 3000, 'min': 1000}, 'n_features': {'max': 10, 'min': 1}, 'type': 'interval'},
 {'prefix': '>', 'type': 'unit', 'unit': 'B'},
 {'length': {'max': None, 'min': 0}, 'n_features': {'max': 1, 'min': 1}, 'type': 'interval'},
 {'prefix': '>', 'type': 'unit', 'unit': 'C'},
 {'length': {'max': 2, 'min': 2}, 'n_features': {'max': 1, 'min': 1}, 'type': 'interval'},
 {'prefix': '>', 'type': 'unit', 'unit': 'D'},
 {'length': {'max': None, 'min': 0}, 'n_features': {'max': 0, 'min': 0}, 'type': 'interval'},
 {'prefix': '=', 'type': 'unit', 'unit': 'E'},
 {'length': {'max': None, 'min': 3}, 'n_features': {'max': None, 'min': 0}, 'type': 'interval'},
 {'prefix': '', 'type': 'unit', 'unit': 'F'},
 {'length': {'max': None, 'min': 2000001}, 'n_features': {'max': None, 'min': 1}, 'type': 'interval'},
 {'prefix': '', 'type': 'unit', 'unit': 'G'},
 {'special': 'distant', 'type': 'interval'},
 {'prefix': '', 'type': 'unit', 'unit': 'H'},
 {'length': {'max': None, 'min': 0}, 'n_features': {'max': None, 'min': 1}, 'type': 'interval'},
 {'prefix': '', 'type': 'unit', 'unit': 'I'},
 {'length': {'max': None, 'min': 0}, 'n_features': {'max': 3, 'min': 0}, 'type': 'interval'},
 {'prefix': '', 'type': 'unit', 'unit': 'J'},
 {'length': {'approx': 3000}, 'n_features': {'max': None, 'min': 0}, 'type': 'interval'},
 {'prefix': '', 'type': 'unit', 'unit': 'K'},
 {'special': 'other_molecule', 'type': 'interval'},
 {'prefix': '', 'type': 'unit', 'unit': 'L'},
 {'special': 'near', 'type': 'interval'},
 {'prefix': '', 'type': 'unit', 'unit': 'M'},
 {'special': 'overlap', 'type': 'interval'},
 {'prefix': '', 'type': 'unit', 'unit': 'N'}]
```

## Examples

Here are examples on how the format can be used to express different
arrangements:

``A,B,C,D``: this is a list without any interval specifications, thus the
features (whose IDs are given) will just follow each other without any
other relevant feature in between.

``A,1,B,C``: in this case, between A and B, there is a further feature.

``A,8:10,B``: in this case, between A and B, there are 8 to 10 other features.

``A,1:*,B`` or ``A,>=1,B``: these are two equivalent ways to express the fact
that between A and B there is at least one other feature.

``A,<10,B``: between A and B there are less than 10 features (max 9).

``A,0[1000:3000],B``: there are no features between A and B,
but there are between 1000 and 3000 bases.

``A,0:1[1kb:3kb],B`` or ``A,<1[1kb:3kb],B``: there are between
1000 and 3000 bases and eventually a feature in this interval

``A,[>30kbp],B`` or ``A,>=0[>30kbp],B``: there are at least 30000 bases between
A and B, including any number of features.

``A,><,B,<>,C``: A and B are close to each other (and thus also on the same
molecule) and distant from C (which can be on the same molecule or another)

``A,><,B,<.>,C``: A and B are close to each other and distant from C,
but all three are on the same molecule

``A,><,B,<|>,C``: A and B are close to each other on the same molecule,
while C is on another molecule

``A,&,^B``: A and B overlap each other, on different strands

``A,B,>C,^D``: the order of the features is A, B, C and D with no other
feature in between them; thereby C and D are on opposite strands, while
A and B can be on any strand)

``A,B,=C``: the feature C is on the same strand as A, but B can be on the
same or on the oppposite strand.

``A,><,=B,><,=C``: all three features are on the same strand and close
to each other, with no features in between.

## Acknowledgements

This specification has been created in context of the DFG project GO 3192/1-1
“Automated characterization of microbial genomes and metagenomes by collection
and verification of association rules”. The funders had no role in study
design, data collection and analysis.


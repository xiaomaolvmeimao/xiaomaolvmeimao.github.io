---
layout:     post
title:      Byte Pair Encoding
author:     Jexus
tags: 		NLP deep_learning
subtitle:   使用 Subword Units 解決 OOV 的方法 -- Byte Pair Encoding
category:  note
---

## Algorithm:
![Alg](https://i.imgur.com/ZsSXYWk.png)

## Detail in Algorithm:

### Input vocab dict
```
orginal vocab: {'l o w </w>': 5, 
                'l o w e r </w>': 2, 
                'n e w e s t </w>': 6, 
                'w i d e s t </w>': 3}
```

### Performing BPE

```
==== Stage:0 ====
# count frequency of all successive character pairs
pairs_freq: [(('e', 's'), 9), (('s', 't'), 9), (('t', '</w>'), 9), (('w', 'e'), 8), (('l', 'o'), 7), (('o', 'w'), 7), (('n', 'e'), 6), (('e', 'w'), 6), (('w', '</w>'), 5), (('w', 'i'), 3), (('i', 'd'), 3), (('d', 'e'), 3), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('e', 's')
## after merging
new_vocab: {'l o w </w>': 5, 'l o w e r </w>': 2, 'n e w es t </w>': 6, 'w i d es t </w>': 3}
```

```
==== Stage:1 ====
# count frequency of all pairs from the vocab dict of last stage
pairs_freq: [(('es', 't'), 9), (('t', '</w>'), 9), (('l', 'o'), 7), (('o', 'w'), 7), (('n', 'e'), 6), (('e', 'w'), 6), (('w', 'es'), 6), (('w', '</w>'), 5), (('w', 'i'), 3), (('i', 'd'), 3), (('d', 'es'), 3), (('w', 'e'), 2), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('es', 't')
## after merging
new_vocab: {'l o w </w>': 5, 'l o w e r </w>': 2, 'n e w est </w>': 6, 'w i d est </w>': 3}
```

```
==== Stage:2 ====
pairs_freq: [(('est', '</w>'), 9), (('l', 'o'), 7), (('o', 'w'), 7), (('n', 'e'), 6), (('e', 'w'), 6), (('w', 'est'), 6), (('w', '</w>'), 5), (('w', 'i'), 3), (('i', 'd'), 3), (('d', 'est'), 3), (('w', 'e'), 2), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('est', '</w>')
new_vocab: {'l o w </w>': 5, 'l o w e r </w>': 2, 'n e w est</w>': 6, 'w i d est</w>': 3}
```

```
==== Stage:3 ====
pairs_freq: [(('l', 'o'), 7), (('o', 'w'), 7), (('n', 'e'), 6), (('e', 'w'), 6), (('w', 'est</w>'), 6), (('w', '</w>'), 5), (('w', 'i'), 3), (('i', 'd'), 3), (('d', 'est</w>'), 3), (('w', 'e'), 2), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('l', 'o')
new_vocab: {'lo w </w>': 5, 'lo w e r </w>': 2, 'n e w est</w>': 6, 'w i d est</w>': 3}
```

```
==== Stage:4 ====
pairs_freq: [(('lo', 'w'), 7), (('n', 'e'), 6), (('e', 'w'), 6), (('w', 'est</w>'), 6), (('w', '</w>'), 5), (('w', 'i'), 3), (('i', 'd'), 3), (('d', 'est</w>'), 3), (('w', 'e'), 2), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('lo', 'w')
new_vocab: {'low </w>': 5, 'low e r </w>': 2, 'n e w est</w>': 6, 'w i d est</w>': 3}
```

```
==== Stage:5 ====
pairs_freq: [(('n', 'e'), 6), (('e', 'w'), 6), (('w', 'est</w>'), 6), (('low', '</w>'), 5), (('w', 'i'), 3), (('i', 'd'), 3), (('d', 'est</w>'), 3), (('low', 'e'), 2), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('n', 'e')
new_vocab: {'low </w>': 5, 'low e r </w>': 2, 'ne w est</w>': 6, 'w i d est</w>': 3}
```

```
==== Stage:6 ====
pairs_freq: [(('ne', 'w'), 6), (('w', 'est</w>'), 6), (('low', '</w>'), 5), (('w', 'i'), 3), (('i', 'd'), 3), (('d', 'est</w>'), 3), (('low', 'e'), 2), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('ne', 'w')
new_vocab: {'low </w>': 5, 'low e r </w>': 2, 'new est</w>': 6, 'w i d est</w>': 3}
```

```
==== Stage:7 ====
pairs_freq: [(('new', 'est</w>'), 6), (('low', '</w>'), 5), (('w', 'i'), 3), (('i', 'd'), 3), (('d', 'est</w>'), 3), (('low', 'e'), 2), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('new', 'est</w>')
new_vocab: {'low </w>': 5, 'low e r </w>': 2, 'newest</w>': 6, 'w i d est</w>': 3}
```

```
==== Stage:8 ====
pairs_freq: [(('low', '</w>'), 5), (('w', 'i'), 3), (('i', 'd'), 3), (('d', 'est</w>'), 3), (('low', 'e'), 2), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('low', '</w>')
new_vocab: {'low</w>': 5, 'low e r </w>': 2, 'newest</w>': 6, 'w i d est</w>': 3}
```

```
==== Stage:9 ====
pairs_freq: [(('w', 'i'), 3), (('i', 'd'), 3), (('d', 'est</w>'), 3), (('low', 'e'), 2), (('e', 'r'), 2), (('r', '</w>'), 2)]
highest to merge: ('w', 'i')
new_vocab: {'low</w>': 5, 'low e r </w>': 2, 'newest</w>': 6, 'wi d est</w>': 3}
```
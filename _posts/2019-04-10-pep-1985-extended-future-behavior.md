---
layout: post
title: 'PEP 1985: Extended __future__ Behavior'
date: 2019-04-10 21:58 -0700
---

**PEP:** | 1985
**Title:** | Extended __future__ Behavior
**Author:** | The Late BDFL, Aearnus, [@TristanBomb](https://twitter.com/TristanBomb "TristanBomb")
**Status:** | Paradoxical
**Type:** | Standards Track
**Created:** | 1-Apr-2025
**Python-Version:** | 7.8++136-Qubit
**Post-History:** | 1-Apr-1985, 1-Apr-2019, 30-Mar-2025, 31-Mar-2025, 1-Apr-2025

Abstract
---
This PEP documents the semantics of cross-temporal module inclusion, contained within `__future__`.

Rationale
---
With the rapidly changing pace of the programming world, we need to introduce a PEP which allows the mortal Python programmer to ensure backwards, forwards, and time independent compatability of all the code that he/she writes.

Ignore this Section
---
This section of the proposal exists because versions of this PEP from the ~~past~~ future also included this section.

Specification
---
To be able to import across the lowly boundaries of time, a developer must import this functionality from `__future__`, as it is not and has not ever been implemented in any withstanding Python version. Since we are importing modules from time periods other than when we are importing this PEP, one must write

```python
from __future__ import __future__.__future__ as __future__
```

to access this functionality.

Once imported, a code sorcerer can specify the module they wish to import as follows:

```python
from __future__.2025 import SupercooledQuantumErrataJS
```

This imported module can then be used as normal:

```python
qe = QuantumErrata(temperature = 0.00004)
qe.coolProcessingUnitToMatch() # => True
print(qe.observeHyperSpin())   # prints "-2|-1" on the author's unit
qe.newSingularityNode()        # => Ṭ͔̞͍̟͔̤̳̱ͪ̉ͥ̐͘͝ṛ̸̯̠͕̲̲̳͓̪̝̥̟ͯ̐ͪ̍ͮ̂̚͘͠͝u͆̈́͑͐͜҉̵̯̠̮̼̠̰̹̞͍̕͝ͅͅeͮͫ͐ͬ̊̈̇̉͗ͪͤ̋̈́̐͢͡͏̵̘͓̞̪̻̼̝̭̠̮͙̼̰̣̤͢
```

Edge Cases and Errata
---
To avoid time paradoxes, attempting to use code imported from the `__future__` in a module that is being summoned to another era will raise a `TypeError` (an unfortunate consequence of having an intern misspell the phrase `TimeError`).

Attempts to correct this misspelling have thus far failed, as the draft document is spontaneously modified to revert the change with a last-modified date sometime in the future.

Importing a module from any year past `2025` seems to terminate the Python instance with a random exit code.

Practical Considerations
---
The authors leave the hardware implementation of these features as an excercise for future developers. Doubts about this strategy may be set to rest, as the successful implementation of importing from the future has proven this strategy to clearly be successful. It is suspected that this implementation involves quantum computing, because time travel and quantum computing are both confusing, and therefore related.

Footnotes
---
The authors of this proposal recommend rejecting this proposal. While this is certainly an unconventional move, use of this proposal has indicated that the future version of this proposal was rejected; in order to maintain timeline consistently, the present version must also be rejected.

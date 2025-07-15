Py-SkipList
===============

Pure Python implementation of a skip list data structure.

[![Build Status](https://travis-ci.org/ZhukovAlexander/py-skiplist.svg?branch=master)](https://travis-ci.org/ZhukovAlexander/py-skiplist)
[![Coverage Status](https://coveralls.io/repos/ZhukovAlexander/py-skiplist/badge.svg?branch=master&service=github)](https://coveralls.io/github/ZhukovAlexander/py-skiplist?branch=master)

Intro
-----
Skip lists are a data structure that can be used in place
of balanced trees. Skip lists use probabilistic balancing
rather than strictly enforced balancing and as a result
the algorithms for insertion and deletion in skip lists
are much simpler and significantly faster than equivalent
algorithms for balanced trees.

Skip lists are balanced by consulting a random number 
generator. Although skip lists have bad worst-case
performance, no input sequence consistently produces the
worst-case performance (much like quicksort when the pivot
element is chosen randomly).

Example usage
-------------

```python
>>>sl = Skiplist(foo='bar', 'spam'='eggs')
>>>sl
'skiplist({"foo": "bar", "spam": "eggs"})'
>>>
>>>sl['foo']
'bar'
>>>
>>>sl['foo'] = 'baz'
>>>sl['foo']
'baz'
>>>
>>>'spam' in sl
True
>>>
>>>del sl['spam']
>>>sl
'skiplist({"foo": "bar"})'
```

Skip List Structure
--------------------
Each element is represented by a node, the level of 
which is chosen randomly when the node is inserted
without regard for the number of elements in the
data structure. A `level i` node has `i` forward
pointers, indexed 1 through `i`. There is no need
to store the level of a node in the node. Levels
are capped at some appropriate constant `MaxLevel`.
The *level of a list* is the maximum level currently
in the list (or 1 if the list if empty). The `header`
of a list has forward pointers at levels one through
`MaxLevel`. The forward pointers of the header at
levels higher than the current maximum level of the
list point to `NIL`.

Skip List Algorithms
===================
Skip list operations are analogous to that of a binary
tree. They include: **search**, **insert**,
and **delete**. Note that skip lists are easily
extendable to support operations like _find the minimum key_ or _find the next key_.

Initialization
--------------
An element `NIL` is allocated and given a key
greater than any legal key. All levels of all
skip lists are terminated with `NIL`. A new list
has level 1 and all forward pointers of the list's
header point to `NIL`.

Search Algorithm
-----------------
Search works by traversing forward pointers
that do not overshoot the node containing the element
being searched for. When no more progress can be
made at the current level of forward pointers, the
search moves down to the next level. When we can make
no more progress at level 1, we must be in front
of the node that contains the desired element (if 
it is in the list).

At what level should the search be started? William's
analysis suggests that ideally we should start
at level `L` where we expect `log_{1/p}n` where
`n` is the number of elements in the list and
`p` is the fraction of nodes in level `i` that
also have level `i+1` pointers. Starting a search
at the maximum level in the list does not add more
than a small constant to the expected search time.

Insertion and Deletion Algorithm
--------------------------------
To insert or delete a node, we simply search and
splice. A vector `update` is maintained so that when
the search is complete, `update[i]` contains a pointer
to the rightmost node of level `i`. The new node
is of a random level.
If the insertion generates a node with a greater level
than the previous maximum, both `Maxlevel` 
and the appropriate portions of the update vector
are updated. After each deletion, we check if we have
deleted the maximum element of the list and if so,
decrease the maximum level of the list.

**References**

* [Skip list wikipedea article](http://en.wikipedia.org/wiki/Skip_list)
* [A Skip List Cookbook](http://cg.scs.carleton.ca/~morin/teaching/5408/refs/p90b.pdf) by William Pugh
* [Skip List vs. Binary Search Tree on Stack Overflow
](http://stackoverflow.com/questions/256511/skip-list-vs-binary-tree)
* [A Provably Correct Scalable Concurrent Skip
List Whitepaper](http://cs.tau.ac.il/~shanir/nir-pubs-web/Papers/OPODIS2006-BA.pdf)
* [Purely functional concurrent skip list
 on Stack Overflow](https://stackoverflow.com/questions/3489560/purely-functional-concurrent-skip-list)
* [http://eternallyconfuzzled.com/tuts/datastructures/jsw_tut_skip.aspx](http://eternallyconfuzzled.com/tuts/datastructures/jsw_tut_skip.aspx)

---
layout: post
title:  "Balanced binary trees on easy mode"
date:   2020-04-13 14:00 +0200
categories: tutorial, binary tree
---


Almost every programmer has heard of binary trees - they are the basis of ordered set implementations
in almost every programming language. Those with unversity education may have even implemented some
basic search trees in an intro class, but most have never implemented balancing. And of those that did,
few have done so often.

And why should they? After all, every language has an ordered set type now. And if you need something
more powerful, there are plenty of existing implementations they can use and/or adapt to their needs.
Some implementations are pluggable, meaning they can accelerate arbitary operations based on
user-provided functions.

However, there are times when such solutions cannot be used. Finding binary trees implementations online
is fraught with difficulty, as there are plenty slightly wrong implementations that *look* fine, but then
lose balance under some operations. In competitive programming, some competitions forbid outside sources -
meaning all structures must be coded before use. Although ordered sets can be pushed pretty far, some
problems require the use of balanced trees and do not readily accept other solutions.


What problems can binary trees solve?
====

While standard binary search trees implement sorted sets, binary trees can also implement sequences,
allowing insertion, deletion and lookup at arbitary indices in O(log(n)) time. Such a tree is generally
a variant of an *order-statistic tree* (OST), unlike the common *binary search tree* (BST). For comparison, vectors
have O(n) insertions except at the end, and circular buffer lists take O(sqrt(n)) for most operations.
However, this analysis hides the most powerful ability of binary trees: the ability to calculate any
associative statistic on a subinterval in log(n) time. This allows surprisingly complex operations to
be solved, like for example finding the matching paranthesis in a text editor [2]. If you want something
like the fifth element larger than X, you can do it by with a help of a max-statistic [3]. The possibilities
are not quite endless, but it's close.


Why are binary trees not used more often?
=====

The biggest obstacle to use of binary trees is the poor state of online resources relating to the tree
structure. Most only cover the search tree (BST) variant, which only implements an ordered set,
and not a sequence. Even among those, the implementations of balancing algorithms are very complicated
and hard to understand. As an example, here[4]'s a random AVL tree implementation. Note that it contains
`if`s nested *three levels deep*. Wikipedia is not much better, but at least it has a textual description
of all operations. The red-black tree description is not much better; implementers are advised to follow
the Sedgwick Left-Leaning Red-Black Tree paper [5] and ignoring the Wikipedia text. But even then, all
desriptions are of the BST variant, with no hints as to the true capabilities of balancing tree data 
structures.

This has resulted in balancing trees not being used where they could, even commercially. An example being
the Discord blog [], where they started with an Erlang Ordset and ended up implementing a skiplist. A sequence OST
could have done the job just as well, but there's not much data on them, and they all hinge on implementing
a balancing tree. Since the implementations are so complicated, implementing the balancing tree (usually
either red-black or AVL) and then augmenting it into a OST is a project in itself.

But there is no reason for this complexity. To prove it, in this post we shall design a balancing binary tree that
implements a sequence, and we shall make it simple enough to be written from scratch in 15 minutes,
without looking up any references or details.


Easy mode restrictions
=====

In order to make such an implementation tractable, we shall make a simplified variant of a 
sequence tree. We will store all data in the leaf nodes, and leave the inner nodes for statistics. This mantains
the log(n) complexity guarantees, but it does mean we look at two nodes more than we would if we used
a full tree. However, this makes it very easy to further augment the tree with additional capabilities.

Since we store all data in the leaves, we can introduce another restriction: every inner node has
exactly two children. This means we can skip several NULL checks when rebalancing a tree, but we will still
need to check whether a node is the root node.

We shall implement the tree *with* parent pointers, in order to have a freestanding rebalance function. Without
them, we need to inline the search and rebalancing operation into the insert and delete, which induces repetition
and a place for errors. Having an additional pointer to juggle does make the code longer, but the complexity is
well balanced.

We shall implement an AVL balanced tree, simply because it is simpler than the Red-black tree. The left-leaning red-black
tree is quite short and logical, as long as you understand why red-black trees work. Other algorithms
with comparable complexity are the Splay tree and the Weight-balanced tree. The Splay tree was not used because its
amortized complexity makes it very easy to suddenly hit a linear-time operation. The Weight-balanced tree
was discarded because it has two numeric parameters, which must be chosen within a narrow range or the tree will
not mantain its balance. More information is available in [5]. A treap was also considered, but storing values in leaves
completely breaks its balancing guarantees; in treaps, values really *must* be stored in its inner nodes, otherwise 
arbitarily unbalanced trees may be created.





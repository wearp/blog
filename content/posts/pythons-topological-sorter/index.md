+++
date = "2020-08-24T00:00:00Z"
title = "Python 3.9's TopologicalSorter"
+++

## Introduction 

New to Python 3.9 is the `graphlib` module and its `TopologicalSorter`. Appearing somewhat out of place in the standard library at the moment, I'll introduce topological sorting generally. I'll also demonstrate how Python's new `TopologicalSorter` works.

---

## What's topological sorting?

To understand topological sorting, knowing the fundamentals of a **directed acyclic graph (DAG)** helps.

Starting off, it's a **graph type data structure**. It's made up of _vertices_ (or nodes) and _edges_ (or lines or arcs) connecting pairs of vertices. In _Figure 1_, the graph consists of five vertices (A, B, C, D, E) and five edges (AB, AC, BD, CD, DE):

```
+---+      +---+      +---+      +---+
| A | ---> | B | ---> | D | ---> | E |
+---+      +---+      +---+      +---+
  |                     ^
  |        +---+        | 
  +------> | C | -------+
           +---+
```
Figure 1: Basic DAG


It's **directed**. Edges connecting vertices have a direction associated with them. In a DAG, edges are sometimes called _arrows_ or _directed edges_.

Finally, it's **acyclic**, which means it has no directed cycles. In other words, the graph has no trail that when followed loops back on itself. So, if you start at one vertex, and follow the graph, you can't return to the same vertex. 

Due to this acyclic property, a DAG must contain at least one **topological ordering** of its vertices. In simplest terms, it's a sequence of the vertices such that every edge is directed from earlier to later in the sequence. In _Figure 1_, two topological orderings are possible: A, B, C, D, E and A, C, B, D, E.

So, **Topological sorting** is the algorithmic problem of finding a topological ordering given a DAG. 

_Figure 2_ shows a very basic **cyclic** graph. You can follow from vertex A to B to C and back to A. This is a directed cycle. For these types of graphs, no topological ordering exists and so they can't be topologically sorted.
```
+---+      +---+ 
| A | <--- | C |
+---+      +---+ 
  |          ^           
  |          |
  |        +---+
  +------> | B |
           +---+
```
Figure 2: Basic _cyclic_ graph.

---

## Python's new TopologicalSorter

Python 3.9 contains its own implementation of topological sort. Called `TopologicalSorter`, it sits in the `graphlib` module (also new) in the standard library. Of course, other third-party implementations are available such as [NetworkX's](https://networkx.github.io/documentation/stable/reference/algorithms/dag.html) which, as well as including a topological sort, also has a bunch of other algorithms for working with DAGs.

`TopologicalSorter`, when given a valid DAG, returns an iterable of nodes in topological order. It can be instantiated with an optional `dict` representation of a DAG and/or nodes can be passed with their predecessors using `add()`.

Here's a quick demonstration of the `TopologicalSorter` at work on the DAG from _Figure 1_:
```
>>> import graphlib
>>> graph = {"D": {"B", "C"}, "C": {"A"}, "B": {"A"}}
>>> ts = graphlib.TopologicalSorter(graph)
>>> ts.add('E', 'D')
>>> tuple(ts.static_order())
('A', 'C', 'B', 'D', 'E')
```

Should the graph contain directed cycles, it raises a `CycleError` exception instead. Here's a quick example of `TopologicalSorter` raising such an error when attempting to sort the cyclic graph from _Figure 2_:
```
>>> import graphlib
>>> cyclic_graph = {"A": {"C"}, "B": {"A"}, "C": {"B"}}
>>> ts = graphlib.TopologicalSorter(graph)
>>> tuple(ts.static_order())
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/willearp/.pyenv/versions/3.9.0rc1/lib/python3.9/graphlib.py", line 241, in static_order
    self.prepare()
  File "/home/willearp/.pyenv/versions/3.9.0rc1/lib/python3.9/graphlib.py", line 103, in prepare
    raise CycleError(f"nodes are in a cycle", cycle)
graphlib.CycleError: ('nodes are in a cycle', ['A', 'B', 'C', 'A'])
```
---

## Final thoughts

`graphlib` is Python's first foray into graph data structures. Up until now, such tools have stayed out the standard library with a number of established third-party packages serving the community instead. I'm interested to see how this module evolves or if it evolves at all.

Anyway, thanks for taking time to read my post on Python 3.9's new `TopologicalSorter`. I hope it's been worth your while. If you want to understand any of the content here in greater depth, I suggest taking a look at the links below.

Also, if you have any feedback for me or just want to get in touch, please email me at <will.earp@icloud.com>.

---

## References
- What's new in Python 3.9 - [docs.python.org](https://docs.python.org/3.9/whatsnew/3.9.html)
- graphlib - [docs.python.org](https://docs.python.org/3.9/library/graphlib.html)
- Topological sorting - [wikipedia.com](https://en.wikipedia.org/wiki/Topological_sorting)
- Directed acyclic graph - [wikipedia.com](https://en.wikipedia.org/wiki/Directed_acyclic_graph)
- What is a directed acyclic graph (DAG) in cryptocurrency? - [academy.binance.com](https://academy.binance.com/blockchain/what-is-a-directed-acyclic-graph-dag-in-cryptocurrency)
- NetworkX - [networkx.github.io](https://networkx.github.io/)

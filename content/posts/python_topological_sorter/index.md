+++
date = "2020-08-21T00:00:00Z"
title = "Python 3.9's TopologicalSorter"
draft = true
+++

## Introduction 

New to Python 3.9 is the `graphlib` module and its `TopologicalSorter`. Appearing somewhat out of place in the standard library at the moment, I'll introduce topological sorting generally. I'll also demonstrate how Python's new `TopologicalSorter` works.

---

## What's topological sorting?

To understand topological sorting, knowing the fundamentals of a **directed acyclic graph (DAG)** helps.

Starting off, it's a **graph type data structure**. It's made up of _vertices_ (or nodes) and _edges_ (or lines or arcs) connecting pairs of vertices. 

It's **directed**. Edges connecting vertices have a direction associated with them. In a DAG, edges are sometimes called _arrows_ or _directed edges_.

Finally, it's **acyclic**, which means it has no directed cycles. In other words, the graph has no trail that when followed loops back on itself. So, if you start at one vertex, and follow the graph, you can't return to the same vertex.

Due to this acyclic property, a DAG must contain at least one **topological ordering** of its vertices. In simplest terms, it's a sequence of the vertices such that every edge is directed from earlier to later in the sequence. 

So, **Topological sorting** is the algorithmic problem of finding a topological ordering given a DAG. 

---

## Python's new TopologicalSorter

With Python 3.9, the standard library contains its own `TopologicalSorter`, which is included in the also new `graphlib` module. 

Given a valid DAG, `TopologicalSorter` returns an iterable of nodes in topological order. Optionally, it can be instantiated with a `dict` representation of a DAG.
```
>>> import graphlib
>>> graph = {"D": {"B", "C"}, "C": {"A"}, "B": {"A"}}
>>> ts = graphlib.TopologicalSorter(graph)
>>> ts.add('E', 'D')
>>> tuple(ts.static_order())
('A', 'C', 'B', 'D', 'E')
```

Should the graph contain directed cycles, it raises a `CycleError` exception instead.
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

## References
- What's new in Python 3.9 - [docs.python.org](https://docs.python.org/3.9/whatsnew/3.9.html)
- graphlib - [docs.python.org](https://docs.python.org/3.9/library/graphlib.html)
- Topological sorting - [wikipedia.com](https://en.wikipedia.org/wiki/Topological_sorting)
- Directed acyclic graph - [wikipedia.com](https://en.wikipedia.org/wiki/Directed_acyclic_graph)
- What is a directed acyclic graph (DAG) in cryptocurrency? - [academy.binance.com](https://academy.binance.com/blockchain/what-is-a-directed-acyclic-graph-dag-in-cryptocurrency)

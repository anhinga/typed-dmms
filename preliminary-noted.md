# Preliminary notes for the next round of refactoring

The first thing which requires revision is memory management.

In our Processing implementations all memory is mutable, a fixed memory buffer is used for the current
element of each input stream and each output stream, and that buffer is rewritten at every recomputation.
This is straightforward, GPU-friendly, and otherwise nice, but is inconvenient if we are dealing with 
large unchanging or sparsele changing structures (since a lot of memory which does not change is rewritten).
It is also inconvenient, if we want to keep the trace of the stream evolution in memory (the potential for sharing
is lost).

In our Clojure implementation all stream-related memory is immutable, it's great from the viewpoint
of sharing and of keeping the trace of the stream evolution in memory, but this is not GPU-friendly, and also
this require a nice implementation of persistent objects with sharing (this is a strong side of Clojure, but
we were trying to write a Python implementation, where these niceties don't seem to be available out of the box).

The first Python version in https://github.com/anhinga/population-of-directions/tree/master/drafts is rather ad hoc.
We attempted to cross these two versions, but we created a difficult-to-understand mess in terms of memory
management. Here is what we really want to accomplish.

In addition to the default case that a stream is zero (and any nonzero linear combination is a variation of that),
we want to enable a second special case, that a stream is identical to some other stream (a network matrix row has 
exactly a single 1, and the rest of it is zero, cf. Section of https://arxiv.org/abs/1712.07447)



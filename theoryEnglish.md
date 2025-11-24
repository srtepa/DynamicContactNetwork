---
title: DSU Data Structure (Union-Find)
weight: 2
authors:
    - Sergey Slotin
created: 2019
---

In graph algorithms, a subtask of tracking connectivity often arises. If we are solving a problem statically, a Breadth-First Search (BFS) or Depth-First Search (DFS) is sufficient. However, if edges are added dynamically, recalculating components each time in $O(V+E)$ is too slow. We need a structure that can do this almost instantly.

This structure is called the **Disjoint Set Union** (DSU). Formally, we have $n$ elements, each initially in its own set. We want to quickly perform two operations:

1.  **Union**: Merge two sets containing elements $a$ and $b$.
2.  **Find**: Determine which set an element belongs to (return its representative).

We want to perform both operations in (almost) $O(1)$. DSU is the *de facto* standard for Kruskal's algorithm (finding the Minimum Spanning Tree) and for finding connected components.

## Internal Structure

We will store sets as **trees**. One tree = one set.
The root of the tree is the **leader** (representative) of the entire set.
The main rule: *for the root of the tree, the parent is the root itself*.

We will store this in an array `p`, where `p[i]` is the parent of element `i`.

```
int p[maxn];

// Initially, everyone is their own leader
for (int i = 0; i < n; i++)
    p[i] = i;
```

To find the leader of element $v$, we simply move up the references until we find a vertex where `p[root] == root`.

To merge two sets (`unite(a, b)`), we find the leaders of both elements and attach one root to the other: `p[leader_a] = leader_b`.

**The problem with the naive implementation:**
In the worst case, such a structure degenerates into a "bamboo" (a long chain). Imagine that we always attach a deeper tree to a leaf. Then the leader search operation will take $O(n)$. If we have $M$ queries, we get $O(M \cdot n)$ and a TL (Time Limit Exceeded).

We need to guarantee that the trees remain short.

## Optimizations

To make DSU work fast, two heuristics are applied.

**1. Path Compression**
The idea is simple: while we are looking for the root of vertex $v$, we traverse a chain of ancestors. Why not immediately re-attach all of them directly to the root? Next time, finding the leader for them will be instantaneous.

```
int leader(int v) {
    // If v is the root, return it.
    // Otherwise, find the leader for the parent and immediately save the result in p[v].
    return (p[v] == v) ? v : p[v] = leader(p[v]);
}
```

**2. Union Heuristic (Union by Rank / Size)**
This heuristic controls the height of the tree during merging. We always want to attach the **smaller** tree to the **larger** one.

*   *Rank Heuristic:* We store the approximate height of the tree ($rank$). We attach the tree with the smaller rank to the tree with the larger rank. If the ranks are equal, the new root increases its rank by 1.
*   *Size Heuristic:* We store the size of the subtree ($size$). We attach the tree with fewer vertices to the one with more.

Example of union by size (most frequently used, as set sizes are often needed in the problem itself):

```
int s[maxn]; // s[i] = 1 initially

void unite(int a, int b) {
    a = leader(a);
    b = leader(b);
    if (a == b) return; // Already in the same set

    // We want 'a' to be smaller to attach it to 'b'
    if (s[a] > s[b]) swap(a, b);
    
    s[b] += s[a]; // Update the size of the larger one
    p[a] = b;     // Attach the smaller one
}
```

You need to use **both** optimizations simultaneously. Path compression speeds up the search, and proper merging prevents the tree from growing in height.

## Asymptotic Complexity

> When using both heuristics (path compression + rank/size), the average time for one operation is $O(\alpha(n))$.

Here, $\alpha(n)$ is the inverse Ackermann function.

**What you need to know about $\alpha(n)$:**
The Ackermann function grows unimaginably fast. Consequently, the inverse function grows unimaginably slowly.
For any numbers you might encounter in the Universe (say, $n \le 10^{600}$), it holds that:
$$
\alpha(n) \le 4
$$

Therefore, in practice, you can safely assume that DSU works in **constant time** $O(1)$.

**Proof** (for theory lovers):
In the general case without path compression, but with the rank heuristic, the tree height is logarithmic $O(\log n)$. Path compression makes the structure even flatter. A rigorous proof of the estimate via the Ackermann function takes several pages and requires complex amortized analysis.
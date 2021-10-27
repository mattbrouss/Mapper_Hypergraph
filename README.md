# Mapper_Hypergraph

Construct a simplex tree recording the simplicial complex generated from the Mapper algorithm and visualize and analyze it as a hypergraph using hypernetx.

Background
--------------

The traditional 2-dimensional Mapper (the Mapper graph) is a simplicial complex formed by performing clustering on
portions of the data space, resulting in a collection of nodes each with some of the data points assigned to them
(the contents of the node). Unlike a traditional clustering algorithm, however, the partial clustering method can
cluster a single data point into multiple nodes. To reflect this, a 1-simplex (an edge) is added to the graph
connecting any pair of nodes which have a non-empty intersection of assigned data points. We assign the intersection
of those data points to the edge as its contents. This results in a graph which can be used to visualize (and conduct
a more sophisticated examination of) the data's structure.

This process can be extended to higher-dimensional simplices: a trio of nodes with a non-empty intersection is
assigned a 2-simplex (a filled-in triangle) connecting the three nodes, with the three pairwise intersections between
the three nodes forming the edges of the triangle, a quartet of nodes with non-empty intersection produces a
tetrahedron, and so on. This process, however, produces a structure that isn't a graph and is not easilly visualizable.
In addition, the calculations can be taxing. In practice, we often restrict ourselves to the Mapper graph.

A simplicial complex is, however, an instance of a hypergraph with the additional restriction that if a set of n nodes
are connected by an edge, all subsets of those nodes are also connected by an edge. The hypernetx package gives tools
for the analysis and visualization of hypergraphs, which allows us to use the same tools to analyze simplicial
complexes.


Purpose
-------

These functions take a dictionary with node names as keys and sets of data points as values and calculate the
resulting hypergraph. They then give methods to visualize the hypergraph through the hypernetx package.

We calculate the hypergraph by use of a simplex tree. The simplex tree has a root node, then has a node for each 0-
simplex in the hypergraph as a decendent of the root node. Each 0-simplex has children for each edge connecting that
simplex to a higher-indexed simplex. Thus if there is an edge (node_0,node_1), the 0-simplex "node_0" will have a
child named "node_1" representing that edge. Likewise, each edge has a child for each 2-simplex for which that edge
connects the two lowest-indexed nodes forming the triangle (that is, triangle (node_0,node_1,node_2) would be
represented by giving the node representing the edge (node_0,node_1) a child named "node_2"). In general, an n-
simplex is represented by giving the (n-1)-simplex connecting all but its highest-indexed node a child named for the
highest-indexed node.

We build our simplex tree from the root up. We add all the Mapper's nodes immediately, then sweep through all pairs
of a node with another, higher-indexed nodes looking for edges. For higher-dimensional simplices, we note that an n-
simplex exists if and only if the (n-2)-simplex connecting its (n-2) lowest-indexed nodes exists, that simplex has
children named after the two highest-indexed nodes in the proposed n-simplex, and those two children have a non-empty
intersection. That is, the simplex (1,2,...,n-2,n-1,n) exists if and only if the simplex (1,2,...,n-2) exists and 
has children (1,2,...,n-2,n-1) and (1,2,...,n-2,n), and those two simplices have a non-empty intersection. Thus once
we have all (n-1) simplices constructed, we can construct all n-simplices by searching each (n-2)-simplex's children
for interesections between a child and one of its higher-indexed siblings. Since we can generate the root node and
all 0-simplices directly, iterating through this process until no new simplices are generated will form the full
simplicial complex.

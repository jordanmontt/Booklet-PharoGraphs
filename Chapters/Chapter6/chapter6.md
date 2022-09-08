## Minimum spanning trees

	| root1 root2 |
	root1 := aDSNode find.
	root2 := self find.

	root1 = root2 ifTrue: [
		"The nodes already belong to the same component"
		^ self ].

	root1 parent: root2
	"Return the root of the component but modifying the parent/child structure during the process of finding a root."

	| root next node |
	node := self.
	root := node.
	[ root = root parent ] whileFalse: [ root := root parent ].

	"Compress the path leading back to the root.
	This is the path compression operation that gives the linear amortized time complexity"
	[ node = root ] whileFalse: [
		next := node parent.
		node parent: root.
		node := next ].

	^ root
2. Pick the smallest edge.
    Check if its two nodes are already unified.
      If they are not, unified them and include the edge to the spanning tree.
      Else, discard it.
3. Repeat step 2 until there are all nodes are connected.

	| treeEdges sortedEdges |
  sortBlock := [ :e1 :e2 | e1 weight < e2 weight ].
	treeEdges := OrderedCollection new.
	nodes do: [ :node | node makeSet].
	sortedEdges := edges asSortedCollection: sortBlock.
	sortedEdges
		reject: [ :edge |
			"Only join the two nodes if they don't belong to the same component"
			edge from find = edge to find ]
		thenDo: [ :edge |
			edge from union: edge to.
			treeEdges add: edge ].
	^ treeEdges
2. Pick the biggest edge...
edges := #( #( $A $B 25 ) #( $A $D 8 ) #( $A $F 11 ) #( $B $A 25 )
            #( $B $E 1 ) #( $B $C 12 ) #( $C $B 12 ) #( $C $D 16 )
            #( $C $F 6 ) #( $C $G 9 ) #( $D $A 8 ) #( $D $C 16 )
            #( $E $B 1 ) #( $E $G 14 ) #( $F $A 11 ) #( $F $C 6 )
            #( $F $G 5 ) #( $F $J 4 ) #( $G $F 5 ) #( $G $C 9 )
            #( $G $E 14 ) #( $G $H 7 ) #( $H $G 7 ) #( $I $J 7 )
            #( $J $F 4 ) #( $J $I 7 ) ).
kruskal := AIKruskal new.
kruskal nodes: nodes.
kruskal
	edges: edges
	from: #first
	to: #second
	weight: #third.
minimumSpanningTree := kruskal run
DSN $J -> DSN $F weight: 4
DSN $F -> DSN $G weight: 5
DSN $F -> DSN $C weight: 6
DSN $I -> DSN $J weight: 7
DSN $H -> DSN $G weight: 7
DSN $A -> DSN $D weight: 8
DSN $A -> DSN $F weight: 11
DSN $C -> DSN $B weight: 12
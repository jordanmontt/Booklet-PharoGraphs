## Shortest path problem
mark start node as visited
Q.addLast(start)
while Q is not empty do:
  node := Q.removeFist()
  if node is the end then:
    return node
  for adjacent nodes of node do:
    if adjacentNode is not visited then:
      mark adjacentNode as visited
      Q.addLast(adjacentNode)

	| node neighbours |
	queue := LinkedList with: start.
	start visited: true.

	[ queue isNotEmpty ] whileTrue: [
		node := queue removeFirst.
		neighbours := node adjacentNodes.

		neighbours do: [ :next |
			next visited ifFalse: [
				queue addLast: next.
				next visited: true.
				next previousNode: node ] ] ]

	| path previous |
	"If no path exists between the start and the end node"
	end previousNode ifNil: [ ^ #( ) ].

	path := LinkedList empty.
	previous := end.
	path addFirst: end model.
	[ previous = start ] whileFalse: [
		previous := previous previousNode.
		path addFirst: previous model ].
	^ path
edges := #( #( $a $b ) #( $b $c ) #( $c $d ) #( $d $e ) #( $e $a )
            #( $b $e ) #( $e $b ) #( $e $f ) #( $f $g ) #( $g $h )
            #( $h $f ) #( $g $i ) #( $i $g ) ).
bfs := AIBFS new.
bfs
	nodes: nodes;
	edges: edges from: #first to: #second.

path := bfs runFrom: $a to: $g
pathToD := bfs reconstructPath

	| pq |
	pq := self newPriorityQueue.
	pq add: start -> 0.

	[ pq isNotEmpty ] whileTrue: [
		| assoc node minWeight |
		assoc := self removeMostPromisingPair: pq.
		node := assoc key.
		minWeight := assoc value.
		node visited: true.

		"Skip if the path weight is less than the one obtained from the pq.
		This is an optimization for not processing unnecessary nodes."
		node pathDistance < minWeight ifFalse: [
			node outgoingEdges do: [ :edge |
				edge to visited ifFalse: [
					| newDistance |
					newDistance := node pathDistance + edge weight.

					newDistance < edge to pathDistance ifTrue: [
						self updateDistance: newDistance of: edge to previousNode: node.
						pq add: edge to -> newDistance ] ] ] ] ]

	aNode previousNode: previousNode.
	aNode pathDistance: newDistance
	"This is the naive implementation of the data structure."

	^ OrderedCollection new
	"This is the naive implementation of the data structure."

	| minValue |
	minValue := aPriorityQueue detectMin: [ :assoc | assoc value ].
	^ aPriorityQueue remove: minValue
edges := #( #( $A $B 5 ) #( $A $C 1 ) #( $B $C 2 ) #( $B $E 20 )
            #( $B $D 3 ) #( $C $B 3 ) #( $C $E 12 ) #( $D $C 3 )
            #( $D $E 2 ) #( $D $F 6 ) #( $E $F 1 ) ).

dijkstra := AIDijkstra new.
dijkstra nodes: nodes.
dijkstra
	edges: edges
	from: #first
	to: #second
	weight: #third.

shortestPathAToB := dijkstra runFrom: $A to: $B.
pathDistanceAToB := (dijkstra findNode: $B) pathDistance.

dijkstra end: $F.
shortestPathAToF := dijkstra reconstructPath.
pathDistanceAToF := (dijkstra findNode: $F) pathDistance.

dijkstra reset.
shortestPathBToE := dijkstra runFrom: $B to: $E.

	nodes do: [ :node | node pathWeight: Float infinity ].
	start pathWeight: 0

	| topSorter stack sortedNode |
	self initializePathWeights.
	topSorter := AITopologicalSorting new
    addNodesFromDifferentGraph: nodes;
    yourself.
	topSorter run.
	stack := topSorter topologicalSortedElements.

	[ stack isNotEmpty ] whileTrue: [
		sortedNode := self findNode: stack removeFirst.
		sortedNode outgoingEdges do: [ :nextEdge |
			nextEdge to pathWeight >
					(sortedNode pathWeight + nextEdge weight)
				ifTrue: [
					nextEdge to pathWeight: sortedNode pathWeight +
            nextEdge weight.
					nextEdge to previousNode: sortedNode ] ] ]

	| stack sortedNode |
	self initializePathWeights.
	stack := self topologicalSortedNodes.

	[ stack isNotEmpty ] whileTrue: [
		sortedNode := self findNode: stack removeFirst.

		sortedNode outgoingEdges do: [ :nextEdge |
			nextEdge to pathDistance >
      (sortedNode pathDistance + nextEdge weight)
				ifTrue: [
          self updatePathDistance: nextEdge previousNode: sortedNode ] ] ]

	| topSorter |
	topSorter := AITopologicalSorting new
		addNodesFromDifferentGraph: nodes;
    yourself.
	 topSorter run.
	 ^ topSorter topologicalSortedElements.

	edge to pathDistance: previousNode pathDistance + edge weight.
	edge to previousNode: previousNode
edges := #( #( $A $B 1 ) #( $B $C 5 ) #( $B $E 11 ) #( $B $D 8 )
            #( $D $E 6 ) #( $E $F 7 ) #( $G $D 4 ) ).
shortestPathInDAG nodes: nodes.
shortestPathInDAG
	edges: edges
	from: #first
	to: #second
	weight: #third

pathAtoF := shortestPathInDAG runFrom: $A to: $F.

	start pathDistance: 0.
	self relaxEdges.
	"Run the algorithm one more time to detect if there is any negative cycles.
	The variation is if we can relax one more time an edge,
	means that the edge is part of a negative cycle.
	So, we put negative infinity as the path distance"
	self relaxEdgesToNegativeInfinity

	| anEdgeHasBeenRelaxed |
	"Relax the edges V-1 times at worst case"
	nodes size - 1 timesRepeat: [
		anEdgeHasBeenRelaxed := false.

		edges do: [ :edge |
			edge from pathDistance + edge weight < edge to pathDistance ifTrue: [
				edge to pathDistance: edge from pathDistance + edge weight.
				edge to previousNode: edge from.
				anEdgeHasBeenRelaxed := true ] ].

		"If no edge has been relaxed means that we can stop the iteration before V-1 times"
		anEdgeHasBeenRelaxed ifFalse: [ ^ self ] ]
	"This method is called after a first relaxation has occurred already.
	The algorithm is the same as the previous one but with the only difference that now if an edge can be relaxed we set the path distance
	as negative infinity because means that the edge is part of a negative cycle."

	| anEdgeHasBeenRelaxed |
	"Relax the edges V-1 times at worst case"
	nodes size - 1 timesRepeat: [
		anEdgeHasBeenRelaxed := false.

		edges do: [ :edge |
			edge from pathDistance + edge weight < edge to pathDistance ifTrue: [
				edge to pathDistance: Float negativeInfinity.
				anEdgeHasBeenRelaxed := true ] ].

		"If no edge has been relaxed means that we can stop the iteration before V-1 times"
		anEdgeHasBeenRelaxed ifFalse: [ ^ self ] ]
nodes := $A to: $F.
edges := #( #($A $B -1) #($B $C -5) #($B $E -11)
            #($B $D -8) #($E $F -7) #($D $E -6)
            #($G $D -4) ).
bellmanFord nodes: nodes.
bellmanFord
	edges: edges
	from: #first
	to: #second
	weight: #third.

pathFromAtoF := bellmanFord runFrom: $A to: $F.
pathDistanceFromAToF := (bellmanFord findNode: $F) pathDistance
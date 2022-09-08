## Topological sorting
S ← Set of all nodes with no incoming edge
while S is not empty do
    remove a node n from S
    add n to L
    for each node m with an edge e from n to m do
        remove edge e from the graph
        if m has no other incoming edges then
            insert m into S
if graph has edges then
    return error (graph is not a DAG)
else
    return L (a topologically sorted order)

  topologicalSortedElements := OrderedCollection empty.
  nodesWithNoIncomingEdges := LinkedList empty.

  "Obtain all the nodes without incoming nodes"
  nodesWithNoIncomingEdges addAll:
  	(nodes select: [ :node | node incomingNodes isEmpty ]).

  [ nodesWithNoIncomingEdges isNotEmpty ] whileTrue: [
  	| node |
  	node := nodesWithNoIncomingEdges removeFirst.
  	topologicalSortedElements addLast: node model.

  	"Remove all the edges of node from the graph"
  	node adjacentNodes do: [ :adjacentNode |
  		adjacentNode incomingNodes remove: node.
  		adjacentNode incomingNodes ifEmpty: [
  			nodesWithNoIncomingEdges add: adjacentNode ] ].
  	node adjacentNodes: #(  ) ].

  "If the graph still has edges"
  (nodes anySatisfy: [ :node | node adjacentNodes isNotEmpty ])
    ifTrue: [ Error signal: 'Not a DAG (Directed Acyclic Graph)' ].

  "Return the topological order the first element being the node without any dependencies"
  ^ topologicalSortedElements

	topologicalSortedElements := OrderedCollection new.
	nodesWithNoIncomingEdges := LinkedList new
	"Obtain all the nodes without incoming nodes"

	nodesWithNoIncomingEdges addAll:
	    (nodes select: [ :node | node isLeaf ]).

	self initializeElements.
	self gatherNoIncomingNodes.
	[ nodesWithNoIncomingEdges isNotEmpty ] whileTrue: [
		| node |
		node := nodesWithNoIncomingEdges removeFirst.
		...

	node adjacentNodes do: [ :adjacentNode |
		adjacentNode incomingNodes remove: node.
		adjacentNode incomingNodes ifEmpty: [
			nodesWithNoIncomingEdges add: adjacentNode ] ].
	node adjacentNodes: #( )].

	^ nodes anySatisfy: [ :node | node adjacentNodes isNotEmpty ].

	self initializeElements.
	self gatherNoIncomingNodes.
	[ nodesWithNoIncomingEdges isNotEmpty ] whileTrue: [
		| node |
		node := nodesWithNoIncomingEdges removeFirst.
		topologicalSortedElements addLast: node model.
		self removeEdgesOf: node ].

	self graphHasEdges ifTrue: [
		Error signal: 'Not a DAG (Directed Acyclic Graph)' ].
	^ topologicalSortedElements
nodes := #( $A $B $C $D $E $F $G ).
edges := #( #( $A $B ) #( $A $C ) #( $B $E ) #( $C $E ) #( $C $D )
            #( $D $E ) #( $D $F ) #( $E $G ) #( $F $G ) ).
"Instantiate the graph algorithm"
topSortingAlgo := AITopologicalSorting new.
"Set the nodes and edges"
topSortingAlgo
	nodes: nodes;
	edges: edges from: #first to: #second.
"Run to obtain the result"
topologicalSortedElements := topSortingAlgo run.
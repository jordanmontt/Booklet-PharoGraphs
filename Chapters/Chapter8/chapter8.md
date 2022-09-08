## Link analysis

	self initializeNodes.
	k timesRepeat: [
		nodes do: [ :node | self computeAuthoritiesFor: node ].
		nodes do: [ :node | self computeHubsFor: node ].
		self normalizeScores ].
	^ nodes

	"Here we are using float instead of int because of the normalization."
	nodes do: [ :n |
		n auth: 1.0.
		n hub: 1.0 ]

	aNode auth:
		(aNode incomingNodes
			inject: 0
			into: [ :sum :node | sum + node hub ])

	aNode hub:
		(aNode adjacentNodes
			inject: 0
			into: [ :sum :node | sum + node auth ])

	| authNorm hubNorm |
	authNorm := 0.
	hubNorm := 0.

	nodes do: [ :node |
		authNorm := authNorm + node auth squared.
		hubNorm := hubNorm + node hub squared ].

	authNorm := authNorm sqrt.
	hubNorm := hubNorm sqrt.

	"To avoid dividing by 0"
	authNorm = 0 ifTrue: [ authNorm := 1.0 ].
	hubNorm = 0 ifTrue: [ hubNorm := 1.0 ].

	nodes do: [ :n |
		n auth: n auth / authNorm.
		n hub: n hub / hubNorm ]
edges := #( #( 'A' 'B' ) #( 'A' 'C' ) #( 'A' 'D' ) #( 'B' 'C' )
            #( 'B' 'D' ) #( 'C' 'A' ) #( 'C' 'D' ) #( 'D' 'D' ) ).
hits := AIHits new.
hits
	nodes: nodes;
	edges: edges from: #first to: #second;
  k: 3.
nodes := hits run
('B' auth: 0.27 hub: 0.54)
('C' auth: 0.49 hub: 0.41)
('D' auth: 0.81 hub: 0.34)

	aNode auth: (aNode incomingEdges
			 inject: 0
			 into: [ :sum :edge | sum + (edge weight * edge from hub) ])

	aNode hub: (aNode outgoingEdges
			 inject: 0
			 into: [ :sum :edge | sum + (edge weight * edge to auth) ])
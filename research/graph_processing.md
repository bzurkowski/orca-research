# Graph processing

* http://cs.brown.edu/courses/cs195w/slides/graphprocess.pdf

* https://www.cs.princeton.edu/courses/archive/fall16/cos418/docs/L21-graph-processing.pdf

MapReduce doesn’t efficiently express data dependencies
Ghost vertices maintain adjacency structure and replicate remote data

Distributed consistency
Solution 1: Chromatic Engine - Edge Consistency via Graph Coloring
Solution 2: Distributed Locking

**GraphLab / PowerGraph**

* https://www.usenix.org/conference/osdi12/technical-sessions/presentation/gonzalez
* https://www.usenix.org/system/files/conference/osdi12/osdi12-final-167.pdf

To achieve serializability, GraphLab prevents adjacent vertex-programs from running concurrently using a fine-grained locking protocol which requires sequentially grabbing locks on all neighboring vertices. Furthermore, the locking scheme used by GraphLab is unfair to high degree vertices.

PowerGraph retains the strong serializability guarantees of GraphLab while addressing its limitations. We address the problem of sequential locking by introducing a new parallel locking protocol which is fair to high degree vertices.

* http://graphstream-project.org/
* https://github.com/graphstream/gs-core
* https://pdfs.semanticscholar.org/9bb9/74fde129a4f88d8675ee1546f627d4dc2cef.pdf
* http://www.eecs.qmul.ac.uk/~fcuadrado/papers/debs17-raphtory.pdf
* https://www.sciencedirect.com/science/article/pii/S0167739X19301621

* https://2017.debs.org/wp-content/uploads/sites/5/2017/07/Chronograph.pdf

Chronograph [18] combines components of all these systems, providing a dynamic graph model which allows concurrent modifications via an unbound stream of updates. This is enabled by instantiating vertices as actors [19] and performing local event sourcing, where each vertex logs its changes and that of its outgoing edges. A global log is then only required to maintain vertex creation and deletion order, minimising bottlenecks. Various computation models may then execute on top of this, performing online approximations on the live dynamic graph and offline batch processing on consistent snapshots. However, whilst this permits temporal analysis via snapshot comparison, the history is not maintained in memory and snapshots must be recomputed from the stored logs.

As established distributed graph processing systems focus primarily on snapshot comparison for temporal analysis, it appeared appropriate to investigate how temporal graphs have been proposed and formalised, as well as understand the manner in which temporal information is traditionally stored. Having many multi-disciplinary applications, a range of models are available in the literature under various pseudonyms such as temporal networks [1] and evolving graphs [20]. These are, therefore, explored to discuss all desirable characteristics, noting possible expansions.

* https://www.cl.cam.ac.uk/~ey204/teaching/ACS/R212_2013_2014/papers/cheng_eurosys_2012.pdf
* https://www.waitingforcode.com/graphs/streaming-graph-processing/read

* https://www.semanticscholar.org/paper/Time-based-sampling-of-social-network-activity-Ahmed-Berchmans/a8516e063df27f6e29263bc86820237cc10c0a9c
* https://www.semanticscholar.org/paper/Bundled-Visualization-of-DynamicGraph-and-Trail-Hurter-Ersoy/7c888d1b9ab165308dd9ce46c43492c6b91b6064
* https://www.semanticscholar.org/paper/Modeling%2C-analysis%2C-and-experimental-comparison-of-Guo-Hong/5dee3ae337fa3cd2e30731fb61b512e06613cc2d

* https://github.com/miratepuffin/raphtory
* https://www.freecodecamp.org/news/how-to-embrace-event-driven-graph-analytics-using-neo4j-and-apache-kafka-474c9f405e06/
* http://www.lsi.us.es/~jtroya/publications/MoDELS18_accepted.pdf
* https://github.com/dbs-leipzig/gradoop

* https://softwareengineering.stackexchange.com/questions/252779/why-would-i-use-elasticsearch-if-i-already-use-a-graph-database
* https://medium.com/@imriqwe/elasticsearch-as-a-graph-database-bc0eee7f7622

* https://neo4j.com/blog/graphs-in-time-and-space/
* https://www.youtube.com/watch?time_continue=54&v=1hgdoS8ICVc&feature=emb_logo
* https://www.youtube.com/watch?v=jiE3wsrVUQs
* https://www.youtube.com/watch?v=tvSgRX9_vP4

* https://www.youtube.com/watch?v=5wluUfomasg
* https://cambridge-intelligence.com/keylines/
* https://grandstack.io/
* https://neo4j.com/blog/streaming-graphs-combining-kafka-neo4j/
* https://neo4j.com/blog/visualize-time-based-graphs-neo4j/

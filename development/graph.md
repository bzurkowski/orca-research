# Graph

```python
from orca.graph.drivers import neo4j
from orca.graph import graph

host = "localhost"
port = 7687
user = "neo4j"
password = "admin"

driver = neo4j.Neo4jDriver(
    host=host, port=port, user=user, password=password)

g = graph.Graph(driver)

g.get_nodes(kind='falco_alert')
```


[[SQL-Create-Edge]]
[discrete]
### SQL - `CREATE EDGE` 
image:../images/edit.png[link="https://github.com/ArcadeData/arcadedb-docs/blob/main/src/main/asciidoc/sql/SQL-Create-Edge.md" float=right]

Creates a new edge in the database.

**Syntax**

```sql
CREATE EDGE <type> [BUCKET <bucket>] [UPSERT] FROM <rid>|(<query>)|[<rid>]* TO <rid>|(<query>)|[<rid>]*
                    [SET <field> = <expression>[,]*]|CONTENT {<JSON>}
                    [RETRY <retry> [WAIT <pauseBetweenRetriesInMs]] [BATCH <batch-size>]
```

- **`<type>`** Defines the type name for the edge.  Use the default edge type `E` in the event that you don't want to use sub-types.
- **`<bucket>`** Defines the bucket in which you want to store the edge.
- **`UPSERT`** allows to skip the creation of edges that already exist between two vertices (ie. a unique edge for a couple of vertices). This works only if the edge type has a UNIQUE index on `out, in` fields, otherwise the statement fails.
- **`JSON`** Provides JSON content to set as the record.  Use this instead of entering data field by field.
- **`RETRY`** Define the number of retries to attempt in the event of conflict, (optimistic approach).
- **`WAIT`** Defines the time to delay between retries in milliseconds.
- **`BATCH`** Defines whether it breaks the command down into smaller blocks and the size of the batches.  This helps to avoid memory issues when the number of vertices is too high.  By default, it is set to `100`.

Edges and Vertices form the main components of a Graph database.  ArcadeDB supports polymorphism on edges.  The base type for an edge is `E`. 

When no edges are created ArcadeDB throws a `OCommandExecutionException` error.  This makes it easier to integrate edge creation in transactions.  In such cases, if the source or target vertices don't exist, it rolls back the transaction. 


**Examples**

- Create an edge of the type `E` between two vertices:

```
ArcadeDB> CREATE EDGE FROM #10:3 TO #11:4
```

- Create a new edge type and an edge of the new type:

```
ArcadeDB> CREATE TYPE E1 EXTENDS E
ArcadeDB> CREATE EDGE E1 FROM #10:3 TO #11:4
```

- Create an edge in a specific bucket:

```
ArcadeDB> CREATE EDGE E1 BUCKET EuropeEdges FROM #10:3 TO #11:4
```

- Create an edge and define its properties:

```
ArcadeDB> CREATE EDGE FROM #10:3 TO #11:4 SET brand = 'fiat'
```

- Create an edge of the type `E1` and define its properties:
 
```
ArcadeDB> CREATE EDGE E1 FROM #10:3 TO #11:4 SET brand = 'fiat', name = 'wow'
```

- Create edges of the type `Watched` between all action movies in the database and the user Luca, using sub-queries:

```
ArcadeDB> CREATE EDGE Watched FROM (SELECT FROM account WHERE name = 'Luca') TO 
            (SELECT FROM movies WHERE type.name = 'action')
```

- Create an edge using JSON content:

```
ArcadeDB> CREATE EDGE E FROM #22:33 TO #22:55 CONTENT <code type='lang-json userinput'>{ "name": "Jay", 
            "surname": "Miner" }
```


>For more information, see:

- <<SQL-Create-Vertex,`CREATE VERTEX`>>



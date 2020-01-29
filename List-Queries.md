# List Computers with Total Administrator Counts Decending
```
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:AdminTo]->(c)
OPTIONAL MATCH (u2:User)-[]->(:Group)-[:AdminTo]->(c)
WITH COLLECT(u1) + COLLECT(u2) as tempVar,c
UNWIND tempVar as users
RETURN c.name as Computer_Name,COUNT(DISTINCT(users)) as User_Count
ORDER BY User_Count DESC
```
# List Computers with Total RDP Counts Descending
```
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:CanRDP]->(c)
OPTIONAL MATCH (u2:User)-[]->(:Group)-[:CanRDP]->(c)
WITH COLLECT(u1) + COLLECT(u2) as tempVar,c
UNWIND tempVar as users
RETURN c.name as Computer_Name,COUNT(DISTINCT(users)) as User_Count
ORDER BY User_Count DESC
```

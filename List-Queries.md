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
# List Computer, User, and Group Permitting Administrator Access
```
MATCH (c:Computer)
MATCH (u:User)-[r:MemberOf*1..]->(g:Group)-[:AdminTo]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
RETURN DISTINCT c.name, u.name, g.name
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
# List Computer, User, and Group Permitting RDP Access
```
MATCH (c:Computer)
MATCH (u:User)-[r:MemberOf*1..]->(g:Group)-[:CanRDP]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
RETURN DISTINCT c.name, u.name, g.name
```

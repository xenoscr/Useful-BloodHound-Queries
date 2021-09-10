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
# List Groups Permitting Administrator Access
```
MATCH (g:Group)-[:AdminTo]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
RETURN DISTINCT c.name, g.name
```
# List Computer, User, and Group Permitting Administrator Access
```
MATCH (c:Computer)
MATCH (u:User)-[r:MemberOf*1..]->(g:Group)-[:AdminTo]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
RETURN DISTINCT c.name, u.name, g.name
```
# List Groups with Administrator Access and Unrolled User Counts Descending (Faster)
```
MATCH (g:Group)-[:AdminTo]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
WITH c, g
MATCH (u1:User)-[:MemberOf*0..]->(g)
RETURN DISTINCT c.name, g.name, COUNT(u1) AS userCount
ORDER BY userCount DESC
```
# List Groups with Administrator Access and Unrolled User Counts Descending (Works on small datasets)
```
MATCH (g:Group)-[:AdminTo]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
WITH c, g
OPTIONAL MATCH (u1:User)-[:MemberOf]->(g)
OPTIONAL MATCH (u2:User)-[]->(:Group)-[:MemberOf]->(g)
WITH COLLECT(u1) + COLLECT(u2) as tempVar, c, g
UNWIND tempVar as users
RETURN DISTINCT c.name, g.name, COUNT(users) AS userCount
ORDER BY userCount DESC
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
# List Groups Permitting RDP Access
```
MATCH (g:Group)-[:CanRDP]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
RETURN DISTINCT c.name, g.name
```
# List Computer, User, and Group Permitting RDP Access
```
MATCH (c:Computer)
MATCH (u:User)-[r:MemberOf*1..]->(g:Group)-[:CanRDP]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
RETURN DISTINCT c.name, u.name, g.name
```
# List Groups with RDP Access and Unrolled User Counts Descending (Faster)
```
MATCH (g:Group)-[:CanRDP]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
WITH c, g
MATCH (u1:User)-[:MemberOf*0..]->(g)
RETURN DISTINCT c.name, g.name, COUNT(u1) AS userCount
ORDER BY userCount DESC6
```
# List Groups with RDP Access and Unrolled User Counts Descending (Works on small datasets)
```
MATCH (g:Group)-[:CanRDP]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
WITH c, g
OPTIONAL MATCH (u1:User)-[:MemberOf]->(g)
OPTIONAL MATCH (u2:User)-[]->(:Group)-[:MemberOf]->(g)
WITH COLLECT(u1) + COLLECT(u2) as tempVar, c, g
UNWIND tempVar as users
RETURN DISTINCT c.name, g.name, COUNT(users) AS userCount
ORDER BY userCount DESC
```
# List of Objects with Interesting Rights to Domain Controllers
```
MATCH (c:Computer)-[r1:MemberOf]-(g1:Group) WHERE g1.name =~ "(?i)DOMAIN CONTROLLERS@.*"
WITH c AS DCs
MATCH (o)-[r2:Owns|:CanRDP|:AdminTo|:AddMember|:WriteDacl|:GenericAll|:GetChanges|:HasSession|:WriteOwner|:ExecuteDCOM|:AllowedToAct|:GenericWrite|:GetChangesAll|:AllExtendedRights|:AllExtendedRights|:AllowedToDelegate|:ForceChangePassword]->(DCs)
RETURN o.name AS Name, o.objectid AS SID, type(r2) AS RelationshipType, DCs.name AS DomainController
```
# List of Users with Cross-Domain Computer Sessions
```
MATCH p=(c1:Computer)-[r:HasSession]->(u:User)<-[r1:HasSession]-(c2:Computer)
WHERE NOT c1.domain = c2.domain 
RETURN u.name, c1.name, c2.name
```
# List of Groups with Total Number of Machines That they Are AdminTo
```
MATCH (g:Group)
MATCH (g)-[r:AdminTo]->(c:Computer)
RETURN g.name as Group_Name, COUNT(DISTINCT(c)) as Total_Machines
ORDER BY Total_Machines DESC
```
# List of Groups with Total Number of Machines that they Can RDP to
```
MATCH (g:Group)
MATCH (g)-[r:CanRDP]->(c:Computer)
RETURN g.name as Group_Name, COUNT(DISTINCT(c)) as Total_Machines
ORDER BY Total_Machines DESC
```
# List Groups with DCOM Access and Unrolled User Counts Descending
```
MATCH (g:Group)-[:ExecuteDCOM]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
WITH c, g
MATCH (u1:User)-[:MemberOf*0..]->(g)
RETURN DISTINCT c.name, g.name, COUNT(u1) AS userCount
ORDER BY userCount DESC
```
# List Groups with PowerShell Remoting Access and Unrolled User Counts Descending
```
MATCH (g:Group)-[:CanPSRemote]->(c)
WHERE NOT g.name =~ "DOMAIN ADMINS@.+"
WITH c, g
MATCH (u1:User)-[:MemberOf*0..]->(g)
RETURN DISTINCT c.name, g.name, COUNT(u1) AS userCount
ORDER BY userCount DESC
```

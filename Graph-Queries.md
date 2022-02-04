# Graph Users wtih Dangerous Rights to Domain Controllers
```
MATCH (c:Computer)-[b:MemberOf]->(g:Group)
WHERE g.name =~ "DOMAIN CONTROLLERS@.+"
WITH c
MATCH p=(u:User)-[r:Owns|WriteDacl|GenericAll|WriteOwner|ExecuteDCOM|GenericWrite|AllowedToDelegate|ForceChangePassword|AllowedToDelegate|ForceChangePassword|ReadGMSAPassword|AllExtendedRights|CanPSRemote|SQLAdmin]->(c:Computer)
RETURN p
```
# Graph Groups with Dangrous Rights to Domain Controllers
```
MATCH (c:Computer)-[b:MemberOf]->(g:Group)
WHERE g.name =~ "DOMAIN CONTROLLERS@.+"
WITH c
MATCH p=(g:Group)-[r:Owns|WriteDacl|GenericAll|WriteOwner|ExecuteDCOM|GenericWrite|AllowedToDelegate|ForceChangePassword|AllowedToDelegate|ForceChangePassword|ReadGMSAPassword|AllExtendedRights|CanPSRemote|SQLAdmin]->(c:Computer)
RETURN p
```
# Graph Shortest Path from Domain Users to Target User
```
MATCH (n:User {name: "<ACCOUNT_NAME>"}), (m), p=shortestPath((m)-[r*1..]->(n)) WHERE m.name =~ "DOMAIN USERS@.+" AND NOT m=n RETURN p
```
# ShortestPath from Domain Users Groups to Objects with DCSync Rights (GetChanges & GetChangesAll)
```
MATCH (a)-[:GetChanges]->(b)
WITH a, b
MATCH (a)-[:GetChangesAll]->(b)
WITH a AS targetGroup
MATCH p=shortestPath((m)-[r*1..]->(targetGroup)) 
WHERE m.name =~ "DOMAIN USERS@.+" AND NOT m=targetGroup
RETURN p
```

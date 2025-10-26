```mermaid
graph TD
subgraph Clients T1-Global
C1\[Client Mòbil]
C2\[Client Web]
C3\[...]
end

subgraph Edge\_Services T2-Global Gateway
G1\[Gateway/Balancer Regió A]
G2\[Gateway/Balancer Regió B]
end

subgraph Infrastructure\_Middleware Capa de Comunicació i Coordinació
PSB Servei Publicació-Subscripció / Multicast
COORD Servidor de Coordinació Distribuïda: ex. Chubby/etcd
end

subgraph Logic\_Cluster T3-Lògica de Joc Particionada
direction LR
LGA Microservei Lògica de Joc - Zona A
LGB Microservei Lògica de Joc - Zona B
end

subgraph Data\_Layer T4-Persistència Distribuïda
DCACHE\[Cache Distribuïda]
DDB NoSQL Distribuïda Sharded - Estat Joc/Missatges
RDB DB Relacional - Perfils/Transaccions Atòmiques
end

C1 --\&gt; G1
C2 --\&gt; G2
G1 --\&gt; LGA
G2 --\&gt; LGB

LGA --\&gt; PSB
LGB --\&gt; PSB
PSB --\&gt; LGA
PSB --\&gt; LGB
PSB --\&gt; DDB(Escriptura de logs/missatges)

LGA \&lt;--\&gt; DCACHE
LGB \&lt;--\&gt; DCACHE

DCACHE --\&gt; DDB
LGA \&lt;--\&gt; RDB
LGB \&lt;--\&gt; RDB
LGA \&lt;--\&gt; COORD
LGB \&lt;--\&gt; COORD
```






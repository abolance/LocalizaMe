```mermaid

graph TD

&nbsp;   subgraph Clients (T1 - Global)

&nbsp;       C1\[Client Mòbil]

&nbsp;       C2\[Client Web]

&nbsp;       C3\[...]

&nbsp;   end



&nbsp;   subgraph Edge\_Services (T2 - Global Gateway)

&nbsp;       G1\[Gateway/Balancer (Regió A)]

&nbsp;       G2\[Gateway/Balancer (Regió B)]

&nbsp;   end



&nbsp;   subgraph Infrastructure\_Middleware (Capa de Comunicació i Coordinació)

&nbsp;       PSB(Servei Publicació-Subscripció / Multicast)

&nbsp;       COORD(Servidor de Coordinació Distribuïda: ex. Chubby/etcd)

&nbsp;   end



&nbsp;   subgraph Logic\_Cluster (T3 - Lògica de Joc Particionada)

&nbsp;       direction LR

&nbsp;       LGA(Microservei Lògica de Joc - Zona A)

&nbsp;       LGB(Microservei Lògica de Joc - Zona B)

&nbsp;   end



&nbsp;   subgraph Data\_Layer (T4 - Persistència Distribuïda)

&nbsp;       DCACHE\[Cache Distribuïda (Redis/Memcached)]

&nbsp;       DDB(NoSQL Distribuïda Sharded - Estat Joc/Missatges)

&nbsp;       RDB(DB Relacional - Perfils/Transaccions Atòmiques)

&nbsp;   end



&nbsp;   C1 --\&gt; G1

&nbsp;   C2 --\&gt; G2

&nbsp;   G1 --\&gt; LGA

&nbsp;   G2 --\&gt; LGB



&nbsp;   LGA --\&gt; PSB

&nbsp;   LGB --\&gt; PSB

&nbsp;   PSB --\&gt; LGA

&nbsp;   PSB --\&gt; LGB

&nbsp;   PSB --\&gt; DDB(Escriptura de logs/missatges)



&nbsp;   LGA \&lt;--\&gt; DCACHE

&nbsp;   LGB \&lt;--\&gt; DCACHE



&nbsp;   DCACHE --\&gt; DDB

&nbsp;   LGA \&lt;--\&gt; RDB

&nbsp;   LGB \&lt;--\&gt; RDB

&nbsp;   LGA \&lt;--\&gt; COORD

&nbsp;   LGB \&lt;--\&gt; COORD

```


```mermaid
graph LR
    subgraph Clients T1
        C1["Client Mòbil"]
        C2["Client Web"]
        C3["..."]
    end

    subgraph API Gateway T2
        G1["Gateway/Balancer<br>Regió A"]
        G2["Gateway/Balancer<br>Regió B"]
    end

    subgraph Cluster Lògica del Joc T3
        LGA["Microservei Lògica de Joc<br>- Zona A"]
        LGB["Microservei Lògica de Joc<br>- Zona B"]
    end
    
    subgraph Infrastructure
        style Infrastructure fill:#f2f2f2,stroke:#333,stroke-width:2px
        PSB["Servei Publicació-<br>Subscripció / Multicast"]
        COORD["Servidor de Coordinació<br>Distribuïda"]
    end

    subgraph Persistència DistribuÏda T4
        DCACHE["Cache Distribuïda"]
        DDB["DB Distribuïda <br>- Estat Joc/Missatges"]
        RDB["DB Relacional -<br>Perfils/Transaccions"]
    end

    %% --- Conexiones del Flujo Principal ---
    C1 & C2 & C3 --> G1
    C1 & C2 & C3 --> G2
    
    G1 --> LGA
    G2 --> LGB
    
    LGA --> DCACHE & DDB & RDB
    LGB --> DCACHE & DDB & RDB

    %% --- Conexiones con la Infraestructura ---
    LGA <--> PSB
    LGB <--> PSB
    
    G1 & G2 <--> COORD
    LGA & LGB <--> COORD
```



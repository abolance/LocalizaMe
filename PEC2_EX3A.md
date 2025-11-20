```mermaid
    graph LR
    subgraph "Flux d'Interacció"
        U[Resource Owner (Usuari)]
        C[Client (Aplicació)]
        AS[Authorization Server]
        RS[Resource Server (API)]
    end

    U -- "1. L'usuari inicia l'acció" --> C
    C -- "2. Redirecciona l'usuari per a l'autorització" --> AS
    U -- "3. S'autentica i dona consentiment" --> AS
    AS -- "4. Envia Codi d'Autorització<br/>(via navegador de l'usuari)" --> C
    
    subgraph "Canal Segur (Back-Channel)"
        C_backend[Client (Backend)]
        AS_backend[Authorization Server]
    end
    
    C_backend -- "5. Intercanvia el Codi d'Autorització<br/>per un Access Token" --> AS_backend
    AS_backend -- "6. Retorna l'Access Token" --> C_backend
    
    C -- "7. Utilitza l'Access Token per<br/>accedir al recurs" --> RS
    RS -- "8. Valida el Token i retorna<br/>les dades protegides" --> C
```

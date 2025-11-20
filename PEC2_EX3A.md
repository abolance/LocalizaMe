sequenceDiagram
    actor User as "Resource Owner (Usuari)"
    participant Client as "Client (Aplicació de Tercers)"
    participant AuthServer as "Authorization Server"
    participant ResourceServer as "Resource Server (API)"

    %% 1. Inici de la sol·licitud
    User->>Client: 1. Sol·licita accés (ex: "Connectar amb Google")
    
    %% 2. Redirecció per a l'autorització
    Client->>User: 2. Redirecciona a l'Authorization Server per obtenir permís
    User->>AuthServer: 3. S'autentica i dóna el seu consentiment explícit
    
    %% 4. Lliurament del Codi d'Autorització
    AuthServer->>User: 4. Redirecciona al Client amb un Codi d'Autorització
    User->>Client: 5. El navegador lliura el Codi d'Autorització al Client

    %% 6. Intercanvi del Codi per un Access Token (Comunicació Back-Channel)
    Note over Client,AuthServer: Aquesta comunicació és segura (servidor a servidor)
    Client->>AuthServer: 6. Intercanvia el Codi d'Autorització (+ Client ID/Secret) per un Access Token
    AuthServer->>Client: 7. Valida el codi i lliura l'Access Token
    
    %% 8. Accés al recurs protegit
    Client->>ResourceServer: 8. Sol·licita el recurs protegit adjuntant l'Access Token
    ResourceServer->>Client: 9. Valida el Token i retorna el recurs sol·licitat
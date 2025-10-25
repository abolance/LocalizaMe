```mermaid
graph TD
subgraph Tier 1: User view and controls
A\[Personal computers or mobile devices]
end

subgraph " "
B\[Message Broker (PubSub System)]
end

subgraph Tier 2: Application server
C\[Application logic]
end

subgraph Tier 3: Database server
D\[Database manager]
end

&nbsp;   A -- Publica nou event --> B
&nbsp;   B -- Entrega nou event --> C
&nbsp;   C -- Publica resultat nou event --> B
&nbsp;   B -- Entrega resultat nou event --> D
```



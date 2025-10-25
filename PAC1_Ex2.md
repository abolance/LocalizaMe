```mermaid
graph TD
&nbsp;   subgraph Tier 1: User view and controls
&nbsp;       A\[Personal computers or mobile devices]
&nbsp;   end

&nbsp;   subgraph " "
&nbsp;       B\[Message Broker (Pub/Sub System)]
&nbsp;   end

&nbsp;   subgraph Tier 2: Application server
&nbsp;       C\[Application logic]
&nbsp;   end

&nbsp;   subgraph Tier 3: Database server
&nbsp;       D\[Database manager]
&nbsp;   end

&nbsp;   A -- Publica nou event --> B
&nbsp;   B -- Entrega nou event --> C
&nbsp;   C -- Publica resultat nou event --> B
&nbsp;   B -- Entrega resultat nou event --> D
```

# Sistemes Distribuïts - PAC 2

Abel Bolance Olivares

---

## Pregunta 1

Proposeu el disseny d’una plataforma distribuïda, escalable i d’alt rendiment que permeti publicar, emmagatzemar i processar fluxos de dades en temps real. La plataforma ha de seguir el model de publicació/subscripció, és a dir, les dades són publicades en temps real pels productors dels temes, i posteriorment consumides pels usuaris.

La meva proposta es una plataforma distribuïda de publicació/subscripció dissenyada per l’alta disposició i consistència. Utilitza un sistema de replicació passiva (mestre-esclau) amb un node líder elegit dinàmicament amb l’algoritme “Bully”.

El líder garanteix l’ordre en el processament dels missatges amb una comunicació “atomic multicast”. Per gestionar l’accés i garantir la integritat el sistema utilitza un control de concurrència pessimista (pessimistic method).

Per establir la seqüència d’esdeveniments, el sistema utilitza rellotges lògics ordenats.

El disseny del sistema ha de considerar les característiques següents:

### a) Coordinació i consens entre els diferents nodes del sistema
Analitzi com dissenyaria la coordinació i l’acord entre els diferents nodes del sistema. Discuteixi com implementaria l’exclusió mútua, l’elecció de líder, per què és necessari un líder, i la comunicació entre grups. En cas d’utilitzar algun algorisme diferent dels vistos a la teoria del mòdul, especifiqui el pseudocodi corresponent.

L’algorisme d’exclusió mútua distribuïda es important per coordinar l’accés a recursos compartits evitant condicions de carrera i inconsistència en les dades.

L’algoritme “Bully” es adequat per un sistema síncron, permet elegir de forma fiable un nou líder quan l’actual falla i garanteix que el procés amb l’identificador més alt assumeix el nou rol. Un líder simplifica la coordinació, actua com seqüenciador global i garanteix un ordre total dels missatges garantint que tots el nodes els processin en la mateixa seqüència.

Fer servir multicast atòmic assegura que tots els nodes correctes rebin els mateixos missatges. L’ordenació total garanteix que es processin en el mateix ordre.

### b) Replicació i tolerància a fallades
Argumenti per què és important la tolerància a fallades en un sistema d’aquest tipus, quin tipus de replicació utilitzaria (activa/passiva), com realitzaria la comunicació en grup entre les diferents rèpliques del sistema, quin paradigma de replicació aplicaria (mestre-mestre / mestre-esclau), i quin algorisme de replicació considera més adequat per a aquest sistema (Gossip, Bayou o Coda)?

La tolerància a fallades es important per aconseguir una alta disponibilitat i alt rendiment. La replicació de les dades permet un sistema sense interrupcions per als usuaris quan un o més nodes fallen.

La comunicació entre rèpliques síncrona, garanteix que tots els nodes tinguin una visió consistent dels nodes actius en cada moment. Assegura que el canvis (nous nodes o fallades) es gestionin de manera ordenada.

Un paradigma mestre-esclau amb replicació passiva ja que simplifica el sistema, els clients només interactuen amb el mestre. El mestre executa les operacions i les replica l’estat actualitzat als esclaus.

Un algorisme de replicació passiva es més adient per a un sistema que requereix una alta consistència en temps real. Gossip, Bayou o Coda estan dissenyats per a sistemes desconnectats.

### c) Gestió de la concurrència i ordenació d’esdeveniments
Discuteix quin tipus de concurrència implementaria en el seu sistema (optimista o pessimista) i quin mètode utilitzaria per dur a terme l’ordenació dels esdeveniments.

Un sistema de concurrència pessimista. Es ideal per a sistemes on les operacions son majoritàriament actualitzacions, com el cas de la publicació de missatges. Detecta els conflictes en el moment de l’accés evitant inconsistències.

L’ordenació dels esdeveniments es gestiona amb rellotges lògics ordenats. El temps físic no és fiable per ordenar esdeveniments en un sistema distribuït.

### d) Comparació amb Apache Kafka
Compari la plataforma distribuïda proposada en els apartats anteriors amb Apache Kafka, i discuteixi els avantatges i/o inconvenients de la seva plataforma respecte d’aquesta.

**Característiques**

| | Sistema proposat | Apache Kafka |
|---|---:|---|
| Coordinació i consens entre nodes | Utilitza “Bully”. El líder és responsable de la coordinació total. | Depèn d’un sistema de consens robust amb el seu propi protocol (KRaft). Un node “controller” gestiona l’estat del líder de cada partició. |
| Replicació i tolerància a fallades | Replicació passiva (mestre-esclau). | Model de log replicat i particionat. |
| Gestió de concurrència i ordenació | Ordre total mitjançant un seqüenciador central. | Garanteix ordre dins de cada partició. |

**Avantatges i inconvenients**

- Sistema proposat: la garantia d’orde total simplifica el disseny; el líder pot convertir-se en un coll d’ampolla.
- Apache Kafka: l’arquitectura per particions és més escalable; la càrrega es divideix en múltiples líders; no garanteix ordre total global.

---

## Pregunta 2

Compari els conceptes i casos d’ús següents, tenint en compte els seus avantatges, limitacions i escenaris d’aplicació:

### a) Autenticació: Needham-Schroeder vs Kerberos
Analitzi la seguretat davant d’atacs, l’eficiència en la comunicació i la facilitat d’implementació en sistemes distribuïts.

**Anàlisis**

| Concepte | Needham-Schroeder | Kerberos |
|---|---:|---|
| Seguretat davant d’atacs | Vulnerable a atacs de repetició. | Segur davant atacs de repetició; soluciona amb timestamps. |
| Eficiència en la comunicació | Menys eficient per a sessions múltiples. | Molt eficient per a sessions múltiples. |
| Facilitat d’implementació | Conceptualment més simple. | Més complex d’implementar i gestionar. |

### b) Serveis altament disponibles: Gossip vs Bayou vs Coda
Compari la tolerància a fallades, la consistència eventual, la disponibilitat i els tipus d’aplicacions per als quals cada protocol és més adequat.

**Anàlisis**

| Protocol | Tolerància i disponibilitat | Consistència | Tipus d’aplicacions |
|---|---:|---|---|
| Gossip | Molt alta; client pot connectar-se amb qualsevol rèplica disponible. | Utilitza segells de temps vectorial. | Aplicacions amb alta disponibilitat i baixa latència de lectura (news, social). |
| Bayou | Dissenyat per entorns mòbils; alta disponibilitat en mode desconnectat. | Transformació operacional i procediments de fusió. | Aplicacions compartides (editores, calendaris). |
| Coda | Molt alta; utilitza AVSG. | Vectors de versió de servidor (CVV). | Sistemes d’arxius distribuïts accessibles des de diferents dispositius. |

### c) Control de concurrència: Google Docs vs Wikipedia
Descrigui com gestionen la concurrència, la resolució de conflictes, la consistència de les dades i la latència en la col·laboració en temps real.

**Anàlisis**

| Sistema | Gestió de concurrència | Resolució de conflictes | Consistència | Latència |
|---|---:|---:|---:|---:|
| Google Docs | Optimista i en temps real. | Automàtica a nivell de servidor. | Consistència eventual en una capa, però percepció forta per l’usuari. | Molt baixa. |
| Wikipedia | Optimista i basat en sessions. | Manual a nivell d’usuari. | Consistència forta per versions publicades. | Molt alta. |

---

## Pregunta 3

### a) OAuth 2.0 — funcionament
OAuth 2.0 es un protocol per a la delegació d’autorització. Permet que una aplicació (Client) accedeixi a recursos d’un Usuari (Owner) allotjats en un altre servei (Resource Server). El procés es basa en l’emissió de tokens d’accés validats per un Authorization Server.

(Diagrama en mermaid inclòs més endavant.)

### b) Needham-Schroeder — explicació de la figura
La figura representa el protocol de clau secreta de Needham-Schroeder dissenyat per establir una clau de sessió (KA,B) segura entre dues parts (Alice i Bob) utilitzant un servidor de confiança (KDC).

**Identificació dels elements del protocol:**

- Alice (A) i Bob (B): usuaris o processos que volen comunicar-se de forma segura.  
- KDC: servidor d’autenticació que coneix les claus secretes de A i B.  
- RA1, RA2, RB: nonces (valors aleatoris) per evitar atacs de repetició.  
- Claus secretes: KA,KDC i KB,KDC són claus compartides entre el KDC i cada usuari.  
- KA,B: clau de sessió generada pel KDC.

**Identificació del Ticket:**

El ticket és un missatge xifrat amb la clau secreta de Bob (KB,KDC) que conté la identitat d’A i la clau de sessió (KA,B). Alice obté aquest ticket del KDC i l’envia a Bob perquè Bob pugui verificar la sessió.

**Interacció del protocol (resum de passos):**

1. Alice sol·licita al KDC la connexió amb Bob (envia identitat, identitat de Bob i nonce RA1).  
2. El KDC responde xifrant amb la clau de Alice: el nonce RA1, la identitat de Bob, la clau de sessió KA,B i el ticket xifrat per Bob.  
3. Alice envia a Bob el ticket i un missatge xifrat amb KA,B(RA2).  
4. Bob desxifra el ticket i respon amb RA2-1 i envia un nonce RB.  
5. Alice retorna RB-1 xifrat amb KA,B.  
6. Finalment, Alice i Bob comparteixen KA,B i poden comunicar-se de forma segura.

---

## Pregunta 4 — Transaccions

Analitzi el rendiment, l’escalabilitat i les fallades en els protocols Two-Phase Commit i Three-Phase Commit. Proposi exemples de sistemes reals on s’utilitzi cadascun d’ells.

**Two-Phase Commit (2PC)**  
- Més utilitzat en transaccions distribuïdes.  
- Rendiment lligat a la latència de xarxa; requereix dues rondes (votació i finalització).  
- El coordinador pot ser un coll d’ampolla i fallades del coordinador deixen participants en estat incert bloquejant recursos.  
- Exemple: middleware de transaccions en CORBA / sistemes SQL distribuïts tradicionals.

**Three-Phase Commit (3PC)**  
- Dissenyat per reduir la probabilitat d’estats blocats quan falla el coordinador.  
- Afegeix una fase intermedi que permet que els participants acordin completar o abortar sense esperar el coordinador si certs precondicions es compleixen.  
- Exemples pràctics: poc estès en entorns comercials; més teòric o en sistemes amb requisits específics de tolerància a fallades.

### Mesura del rendiment de 2PC
- El rendiment es mesura per latència per completar la transacció (temps de commit), nombre de rondes de missatges, i temps de bloqueig en cas de fallada.
- Escenaris amb 4 transaccions:
  - Totes fan commit: latència total = suma del temps de commit per cada transacció (fases completes).
  - Dues fan abort: el cost d’abort és menor en termes de bloqueig però implica comunicació per informar l’abort; rendiment depèn de quines finalitzen amb commit.

---

## Pregunta 5 — Rellotges

### a) Lamport clocks
Tots els rellotges comencen a zero. Etiquetes finals (resultats atorgats en l’exercici):

**Escenari 1 — Resultat Final**
- P1: 11  
- P2: 9  
- P3: 10  
- P4: 7

**Escenari 2 — Resultat Final**
- P1: 10  
- P2: 6  
- P3: 9  
- P4: 9

### b) Rellotges vectorials
Tots els rellotges comencen en (0,0,0,0).

**Escenari 1 — Resultat Final**
- P1: (10,2,3,6)  
- P2: (7,5,3,3)  
- P3: (8,2,6,4)  
- P4: (1,0,0,6)

**Escenari 2 — Resultat Final**
- P1: (10,1,1,8)  
- P2: (1,4,2,3)  
- P3: (4,1,7,6)  
- P4: (7,1,1,8)

### c) Relació “va ocórrer abans”
Amb rellotges vectorials es pot determinar si dos esdeveniments són concurrents o tenen relació causal només observant els seus segells. Això no és possible amb només rellotges de Lamport.

### d) Rellotges vectorials a Amazon Dynamo
Dynamo utilitza rellotges vectorials per detectar conflictes de versions d’una mateixa dada actualitzada de forma concurrent a diferents rèpliques. Quan es detecta un conflicte, Dynamo delega la reconciliació semàntica al client (no resol automàticament).

**Referències**  
- Distributed Systems Concepts and Design (Fifth Edition)  
- Kafka 4.1 Documentation  
- Protocol Kerberos (RFC 4120)  
- Needham-Schroeder Public key protocol (NSPK)  
- Amazon Dynamo (paper)  
- OAuth.net

---

## Generació de gràfics (Mermaid)

**Graf exercici 3.A**

```mermaid
graph LR
    subgraph "Flux d'Interacció"
        U[Resource Owner - Usuari]
        C[Client - Aplicació]
        AS[Authorization Server]
        RS[Resource Server - API]
    end
    U -- "1. L'usuari inicia l'acció" --> C
    C -- "2. Redirecciona l'usuari per a l'autorització" --> AS
    U -- "3. S'autentica i dona consentiment" --> AS
    AS -- "4. Envia Codi d'Autorització<br/>via navegador de l'usuari" --> C
    subgraph "Canal Segur - Back-Channel"
        C_backend[Client - Backend]
        AS_backend[Authorization Server]
    end
    C_backend -- "5. Intercanvia el Codi d'Autorització<br/>per un Access Token" --> AS_backend
    AS_backend -- "6. Retorna l'Access Token" --> C_backend
    C -- "7. Utilitza l'Access Token per<br/>accedir al recurs" --> RS
    RS -- "8. Valida el Token i retorna<br/>les dades protegides" --> C

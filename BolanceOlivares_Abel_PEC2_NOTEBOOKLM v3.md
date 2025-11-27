### Sistemes Distribuïts - PAC 2
#### Pregunta 1
##### Proposeu el disseny d’una plataforma distribuïda, escalable i d’alt rendiment que permeti publicar, emmagatzemar i processar fluxos de dades en temps real. La plataforma ha de seguir el model de publicació/subscripció, és a dir, les dades són publicades en temps real pels productors dels temes, i posteriorment consumides pels usuaris.
##### La meva proposta es una plataforma distribuïda de publicació/subscripció dissenyada per l’alta disposició i consistència. Utilitza un sistema de replicació passiva (mestre-esclau) amb un node líder elegit dinàmicament amb l’algoritme “Bully”.

##### El líder garanteix l’ordre en el processament dels missatges amb una comunicació “atomic multicast”. Per gestionar l’accés i garantir la integritat el sistema utilitza un control de concurrència pessimista (*pessimistic method*). Per establir la seqüència d’esdeveniments, el sistema utilitza rellotges lògics ordenats.
##### El disseny del sistema ha de considerar les característiques següents:
##### a) Coordinació i consens entre els diferents nodes del sistema. Analitzi com dissenyaria la coordinació i l’acord entre els diferents nodes del sistema. Discuteixi com implementaria l’exclusió mútua, l’elecció de líder, per què és necessari un líder, i la comunicació entre grups. En cas d’utilitzar algun algorisme diferent dels vistos a la teoria del mòdul, especifiqui el pseudocodi corresponent.

##### L’algorisme d’exclusió mútua distribuïda es important per coordinar l’accés a recursos compartits evitant condicions de carrera i inconsistència en les dades.
##### L’algoritme “Bully” es adequat per un sistema síncron, permet elegir de forma fiable un nou líder quan l’actual falla i garanteix que el procés amb l’identificador mes alt assumeix el nou rol. Un líder simplifica la coordinació, actua com seqüenciador global i garanteix un ordre total dels missatges garantint que tots el nodes els processin en la mateixa seqüència.

##### Fer servir *multicast atòmic* assegura que tots els nodes correctes rebin els mateixos missatges. L’ordenació total garanteix que es processin en el mateix ordre.
##### b) Replicació i tolerància a fallades. Argumenti per què és important la tolerància a fallades en un sistema d’aquest tipus, quin tipus de replicació utilitzaria (activa/passiva), com realitzaria la comunicació en grup entre les diferents rèpliques del sistema, quin paradigma de replicació aplicaria (mestre-mestre / mestre-esclau), i quin algorisme de replicació considera més adequat per a aquest sistema (Gossip, Bayou o Coda)?

##### La tolerància a fallades es important per aconseguir una alta disponibilitat i alt rendiment. La replicació de les dades permet un sistema sense interrupcions per als usuaris quan un o mes nodes fallen.
##### La comunicació entre rèpliques síncrona, garanteix que tots els nodes tinguin una visió consistent dels nodes actius en cada moment. Assegura que el canvis (nous nodes o fallades) es gestionin de manera ordenada.
##### Un paradigma mestre-esclau amb replicació passiva ja que simplifica el sistema, els clients només interactuen amb el mestre. El mestre executa les operacions i les replica l’estat actualitzat als esclaus.

##### Un algorisme de replicació passiva es mes adient per a un sistema que requereix una alta consistència en temps real. Gossip, Bayou o Coda estan dissenyats per a sistemes desconnectats.
##### c) Gestió de la concurrència i ordenació d’esdeveniments. Discuteix quin tipus de concurrència implementaria en el seu sistema (optimista o pessimista) i quin mètode utilitzaria per dur a terme l’ordenació dels esdeveniments.
##### Un sistema de concurrència pessimista. Es ideal per a sistemes on les operacions son majoritàriament actualitzacions, com el cas de la publicació de missatges. Detecta els conflictes en el moment de l’accés evitant inconsistències.

##### L’ordenació dels esdeveniments es gestiona amb rellotges lògics ordenats. El temps físic no és fiable per ordenar esdeveniments en un sistema distribuït.

##### d) Compari la plataforma distribuïda proposada en els apartats anteriors amb Apache Kafka, i discuteixi els avantatges i/o inconvenients de la seva plataforma respecte d’aquesta.

| Característiques | Sistema proposat | Apache Kafka |
| :--- | :--- | :--- |
| **Coordinació i consens entre els diferents nodes del sistema** | Utilitza un algorisme “Bully”. El líder es responsable de la coordinació total. | Depèn d’un sistema de consens robust amb el seu propi protocol Kraft. Un node “controller” gestiona l’estat del líder de cada partició. |
| **Replicació i tolerància a fallades** | Replicació passiva (mestre-esclau). | Un model de log replicat i en particions. |
| **Gestió de la concurrència i ordenació d’esdeveniments** | Ordre total mitjançant un seqüenciador central. | Garanteix l’ordre total només dins de cada partició. |

| Sistema | Avantatges | Inconvenients |
| :--- | :--- | :--- |
| **Sistema proposat** | La garantia d’ordre total simplifica el disseny. | El líder pot convertir-se en un coll d’ampolla. |
| **Apache Kafka** | L’arquitectura basada en particions es més escalable. La càrrega es divideix en múltiples líders de partició que poden treballar en paraŀlel. | La falta d’ordre total global. |

#### Pregunta 2
##### Compari els conceptes i casos d’ús següents, tenint en compte els seus avantatges, limitacions i escenaris d’aplicació:
##### a) Autenticació: Needham-Schroeder vs Kerberos. Analitzi la seguretat davant d’atacs, l’eficiència en la comunicació i la facilitat d’implementació en sistemes distribuïts.

| Anàlisis | Needham-Schroeder | Kerberos |
| :--- | :--- | :--- |
| **Seguretat davant d’atacs** | Vulnerable a atacs de repetició. | Segur d’avant de atacs de repetició. Soluciona aquesta vulnerabilitat mitjançant “timestamps”. |
| **Eficiència en la comunicació** | Menys eficient per a sessions múltiples. | Molt eficient per a sessions múltiples. |
| **Facilitat d’implementació** | Conceptualment mes simple. | Molt mes complex d’implementar i gestionar. |

##### b) Serveis altament disponibles: Gossip vs Bayou vs Coda. Compari la tolerància a fallades, la consistència eventual, la disponibilitat i els tipus d’aplicacions per als quals cada protocol és més adequat.

| Anàlisis | Gossip | Bayou | Coda |
| :--- | :--- | :--- | :--- |
| **Tolerància a fallades i disponibilitat** | Molt alta, un client pot comunicar amb qualsevol gestor de rèplica disponible. | Dissenyat per entorns mòbils. Alta disponibilitat fins i tot en mode desconnectat. | Molt alta. Utilitza grups d’emmagatzematge de volum disponible (AVSG). |
| **Consistència** | Utilitza segells de temps vectorial (*timestap vector*). | Utilitza transformació operacional, cada actualització inclou una comprovació de dependències i un procediment de fusió per resoldre conflictes. | Utilitza vectors de versió de Servidor (CVV) per detectar conflictes. |
| **Tipus d’aplicacions** | Aplicacions amb alta disponibilitat i baixa latència de lectura com sistemes de noticies o xarxes socials. | Aplicacions compartides com editors de text o calendaris compartits. | Sistemes d’arxius distribuïts que poden ser accessibles per l’usuari des de diferents dispositius. |

##### c) Control de concurrència: Google Docs vs Wikipedia. Descrigui com gestionen la concurrència, la resolució de conflictes, la consistència de les dades i la latència en la coŀlaboració en temps real.

| Anàlisis | Google Docs | Wikipedia |
| :--- | :--- | :--- |
| **Gestió de la concurrència** | Optimista i en temps real. | Optimista i basat en sessions. |
| **Resolució de conflictes** | Automàtica a nivell de servidor. | Manual a nivell d’usuari. |
| **Consistència de les dades** | Consistència eventual. | Consistència forta. |
| **Latència en la coŀlaboració en temps real** | Molt baixa. | Molt Alta. |

#### Pregunta 3
##### Expliqui breument:
##### a) El funcionament del protocol OAuth 2.0, utilitzant un diagrama on es mostri clarament el flux i els rols.

##### OAuth 2.0 es un protocol per a la delegació d’autorització.
##### Permet que una aplicació (Client) accedeixi a recursos d’un Usuari (Owner) allotjats en un altre servei (Resource Server). El procés es basa en l’emissió de tokens d’accés validats per un Authorization Server.

<!-- IMAGEN EXTRAÍDA: Image001 -->
### Figura Image001: Flux d'Interacció del Protocol OAuth 2.0 (Diagrama Mermaid)

**Descripción Técnica Detallada:**
La imatge, representada pel codi *mermaid*, és un diagrama de flux que descriu l'intercanvi de missatges en el protocol OAuth 2.0 (Grant Type: Authorization Code Flow). Inclou els quatre rols principals: Resource Owner (Usuari), Client (Aplicació), Authorization Server (AS) i Resource Server (RS). El flux consta de 8 passos: 1. L'usuari inicia l'acció. 2. Redirecció a l'AS. 3. Autenticació i consentiment de l'usuari. 4. Retorn del Codi d'Autorització al Client. 5. Intercanvi del Codi per l'Access Token mitjançant un **Canal Segur (Back-Channel)**. 6. Retorn de l'Access Token. 7. Ús de l'Access Token per accedir al recurs del RS. 8. Validació del Token pel RS i retorn de les dades protegides al Client.

##### b) Expliqui la figura següent, que mostra la interacció del protocol Needham-Schroeder, identificant cada element del protocol i el ticket en particular.
##### La figura representa el protocol de clau secreta de Needman-Schoeder dissenyat per establir una clau de sessió ($K_{A,B}$) segura entre dues parts (Alice i Bob) utilitzant un servidor de confiança (KDC).

<!-- IMAGEN EXTRAÍDA: Image002 -->
### Figura Image002: Diagrama de Interacció del Protocol Needham-Schroeder (Conceptual)

**Descripción Técnica Detallada:**
El diagrama representa els cinc passos de la interacció del protocol de clau secreta de Needham-Schroeder, dissenyat per autenticar Alice (A) i Bob (B) i establir una clau de sessió temporal ($K_{A,B}$), amb la mediació d’un Servidor de Distribució de Claus (KDC). La figura il·lustra l'ús de *nonces* ($R_{A1}, R_{A2}, R_B$) per prevenir atacs de repetició i les claus secretes compartides ($K_{A,KDC}, K_{B,KDC}$). El component crucial és el **Ticket**, un missatge xifrat pel KDC amb la clau secreta de Bob ($K_{B,KDC}$), que conté la identitat d'A i $K_{A,B}$. Alice (A) rep el ticket en el missatge 2, el reenvia a B en el missatge 3 sense poder-lo desxifrar, provant així a B que la clau de sessió prové del KDC.

##### Identificació del elements del protocol:
##### Alice (A) i Bob (B), son els usuaris o processos principals que volem comunicar-se de forma segura.
##### KDC, es el Servidor d’autenticació que coneix les claus secretes de A i B.
##### $R_{A1}$, $R_{A2}$, $R_B$, son “nonces” un valor generat aleatòriament que s’utilitza una sola vegada per assegurar el missatge i evitar possibles atacs de repetició.
##### Claus secretes,
##### $K_{A,KDC}$ es la clau secreta compartida entre Alice i el KDC.
##### $K_{B,KDC}$ es la clau secreta compartida entre Bob i el KDC.

##### $K_{A,B}$ es la clau secreta de sessió, una clau generada pel KDC perquè Alice i Bob es comuniquin de forma segura temporalment.
##### Identificació del Ticket:
##### Un ticket es un missatge xifrat que porta la clau secreta d’un sol us en la comunicació entre A i B.
##### El ticket apareix en el missatge 2 i s’utilitza en el missatge 3.
##### El KDC genera el ticket i el xifra amb la clau secreta de Bob ($K_{B,KDC}$).
##### El ticket porta la identitat de qui soŀlicita la connexió (A) i la clau secreta de la sessió ($K_{A,B}$).

##### Alice rep el ticket en el missatge 2 però no el pot llegir perquè Alice no te la clau secreta de Bob, només pot utilitzar-ho per enviar el missatge 3 a Bob. Això garanteix a Bob que la clau probe del KDC ja que només el KDC coneix la clau secreta de Bob.
##### Interacció del protocol:

| Missatge | Interacció | Explicació |
| :--- | :--- | :--- |
| **1** | Alice soŀlicita al KDC una connexió amb Bob. | Alice envia la seva identitat, la de Bob i el nonce $R_{A1}$. |
| **2** | El KDC respon xifrant tot amb la clau de Alice. | El nonce de Alice $R_{A1}$. La identitat de Bob (B). La nova clau secreta de sessió ($K_{A,B}$). I el **ticket** per a Bob que Alice no pot desxifrar. |
| **3** | Alice ha desxifrat el missatge 2 i te la clau de sessió. | Alice envia a Bob el ticket i un missatge xifrat amb la nova clau $K_{A,B}(R_{A2})$. |
| **4** | Bob desxifra el ticket, identifica a Alice i obté la clau $K_{A,B}$. | Bob respon a Alice amb $R_{A2}-1$ per informar a Alice de que realment es Bob. I Bob envia un nonce $R_B$ per verificar que Alice està activa. |
| **5** | Alice desxifra el missatge de Bob i modifica $R_B$. | Alice el retorna xifrat amb la clau de sessió com $R_B-1$. Al completar la seqüència Alice i Bob disposen de la clau de sessió $K_{A,B}$ i asseguren la comunicació xifrada. |

#### Pregunta 4
##### Respon les preguntes següents sobre transaccions:
##### a) Analitzi el rendiment, l’escalabilitat i les fallades en els protocols Two-Phase Commit i Three-Phase Commit. Proposi exemples de sistemes reals on s’utilitzi cadascun d’ells.
##### Two-Pahse Commit (2PC), es el mes utilitzat en transaccions distribuïdes. El rendiment està lligat a la latència de la xarxa, requereix dues rondes de comunicació entre el coordinador i tots els participants. El Coordinador representa un coll d’ampolla ja que ha de comunicar-se individualment amb cada participant, recollir tots els vots i distribuir la decisió final. En cas de fallada del Coordinador, el protocol pot patir grans retards que afecten al rendiment. A mes, si el Coordinador falla després de que un Participant hagi votat i abans de rebre la decisió final, el Participant entra en un estat incert. Un participant en estat incert no pot decidir si confirmar o avortar la transacció i ha de bloquejar els recursos que ha modificat fins que es defineix un nou Coordinador.

##### Exemple, el *middleware* CORBA que ofereix serveix per a transaccions d’objectes.
##### Three-Phase Commit (3PC), la informació que apareix en el llibre només parla de que aquest protocol s’ha dissenyat per solucionar els problemes de retards en el bloqueig del protocol 2PC. En cas de fallada del Coordinador, els Participants poden posar-se d’acord per finalitzar la transacció sense haver d’esperar.

##### Exemple, no he trobat cap exemple d’utilització del protocol 3PC.
##### b) Descrigui com es mesura el rendiment del protocol de commit en dues fases.

##### El rendiment es mesura mitjançant el temps de latència per completar la transacció, les dues fases (votació i finalització) i la durada dels retards en cas de fallada, la durada del bloqueig.

##### Expliqui el rendiment per a 4 transaccions en què:

##### (1) totes fan commit
##### Totes les transaccions ($T_1, T_2, T_3, T_4$) fan commit, el rendiment es defineix per la finalització completa de les dues fases.
##### El rendiment serà la suma del temps que triguen les 4 transaccions a completar el cicle. Com que totes tenen èxit, el temps de bloqueig es el mínim, però significatiu.

##### (2) dues fan abort
##### Dues transaccions ($T_1, T_2$) fan commit, el rendiment es defineix per la finalització completa de les dues fases.
##### Dues transaccions ($T_3, T_4$) fan abort, el rendiment es mínim perquè el Coordinador comunica immediatament a tots els participants el resultat.
##### El rendiment serà la suma del temps que triguen les transaccions $T_1$ i $T_2$ a completar el cicle. I la suma de comunicar l’*abort* de les transaccions $T_3$ i $T_4$.
##### El rendiment es millor en un escenari on hi ha *aborts* ja que la carrega de recursos bloquejats es menor.

#### Pregunta 5
##### En els escenaris següents, on tant les fletxes com els punts indiquen el progrés del rellotge en cada procés, respongui aquestes preguntes:
##### a) Amb rellotges de Lamport. Tots els rellotges comencen a zero. Etiqueti el diagrama amb els valors dels rellotges de Lamport i mostri l’estat final dels rellotges.

<!-- IMAGEN EXTRAÍDA: Image003 -->
### Figura Image003: Diagrama d'Events de Rellotges (Escenari 1)

**Descripción Técnica Detallada:**
Aquesta figura representa el diagrama d'esdeveniments per a l'Escenari 1 dels rellotges lògics. El diagrama, format per línies temporals (processos $P_1$ a $P_4$), punts (esdeveniments locals) i fletxes (intercanvi de missatges), defineix la causalitat i l'ordre parcial utilitzat per calcular els valors dels rellotges de Lamport i vectorials. En l'etiquetatge amb rellotges de Lamport, aquest diagrama condueix als següents valors finals: P1=11, P2=9, P3=10, P4=7.

| Procés | Resultat Final (Escenari 1) |
| :--- | :--- |
| P1 | 11 |
| P2 | 9 |
| P3 | 10 |
| P4 | 7 |

<!-- IMAGEN EXTRAÍDA: Image004 -->
### Figura Image004: Diagrama d'Events de Rellotges (Escenari 2)

**Descripción Técnica Detallada:**
Aquesta figura representa el diagrama d'esdeveniments per a l'Escenari 2 dels rellotges lògics. Igual que l'Escenari 1, mostra l'estructura d'intercanvi de missatges i esdeveniments locals entre els processos $P_1$ a $P_4$, però amb una configuració causal diferent. Aquest diagrama s'utilitza per calcular el segon conjunt de resultats. En l'etiquetatge amb rellotges de Lamport, aquest diagrama condueix als següents valors finals: P1=10, P2=6, P3=9, P4=9.

| Procés | Resultat Final (Escenari 2) |
| :--- | :--- |
| P1 | 10 |
| P2 | 6 |
| P3 | 9 |
| P4 | 9 |

##### b) Amb rellotges vectorials. Tots els rellotges comencen en (0,0,0,0). Etiqueti el diagrama amb els valors dels rellotges vectorials i mostri l’estat final dels rellotges.

| Procés | Resultat Final (Escenari 1) |
| :--- | :--- |
| P1 | (10,2,3,6) |
| P2 | (7,5,3,3) |
| P3 | (8,2,6,4) |
| P4 | (1,0,0,6) |

| Procés | Resultat Final (Escenari 2) |
| :--- | :--- |
| P1 | (10,1,1,8) |
| P2 | (1,4,2,3) |
| P3 | (4,1,7,6) |
| P4 | (7,1,1,8) |

##### c) Fent servir la relació “va ocórrer abans”, expliqui què es pot aconseguir amb els rellotges vectorials que és impossible amb els rellotges de Lamport.

##### Es pot determinar si dos esdeveniments són concurrents o si tenen una relació causal només observant els seus segells de temps.
##### Amb els rellotges de Lamport no poden distingir la causalitat de la concurrència.

##### d) Com s’aplicaren els rellotges vectorials a Amazon Dynamo per detectar la necessitat de reconciliació semàntica?
##### Dynamo es un sistema de base de dades clau-valor altament disponible.
##### Els rellotges vectorials s’apliquen per detectar conflictes de versions d’una mateixa dada que ha estat actualitzada de manera concurrent en diferents servidors (rèpliques).

##### Quan es detecta un conflicte, Dynamo no intenta resoldre’l automàticament, ho delega al Client, aquest es el procés de reconciliació semàntica. El conflicte es resol a una capa superior que entén el significat de les dades.

#### Referencies

##### Distributed Systems Concepts and Desing Fifth Edition
##### Kafka 4.1 Documentation (https://kafka.apache.org/documentation/)
##### Protocol Kerberos (RFC 4120) (https://datatracker.ietf.org/doc/html/rfc4120)
##### Needham-Schroeder Public key protocol (NSPK) (https://www.f4tm4n.com/needham-schroeder-public-key-protocol-nspk)
##### Amazon Dynamo (https://dl.acm.org/doi/abs/10.1145/1323293.1294281)
##### OAuth.net (https://oauth.net/2/)

##### Generació de gràfics amb llenguatge markdown (MD) amb la funció “graph” i visualització en un entorn Github per a la seva representació.
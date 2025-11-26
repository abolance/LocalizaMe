\# Sistemes Distribuïts - PAC 2



Abel Bolance Olivares



---



\## Pregunta 1



Proposeu el disseny d’una plataforma distribuïda, escalable i d’alt rendiment que permeti publicar, emmagatzemar i processar fluxos de dades en temps real. La plataforma ha de seguir el model de publicació/subscripció, és a dir, les dades són publicades en temps real pels productors dels temes, i posteriorment consumides pels usuaris.



La meva proposta es una plataforma distribuïda de publicació/subscripció dissenyada per l’alta disposició i consistència. Utilitza un sistema de replicació passiva (mestre-esclau) amb un node líder elegit dinàmicament amb l’algoritme “Bully”.



El líder garanteix l’ordre en el processament dels missatges amb una comunicació “atomic multicast”. Per gestionar l’accés i garantir la integritat el sistema utilitza un control de concurrència pessimista (pessimistic method).



Per establir la seqüència d’esdeveniments, el sistema utilitza rellotges lògics ordenats.



El disseny del sistema ha de considerar les característiques següents:



\### a) Coordinació i consens entre els diferents nodes del sistema

Analitzi com dissenyaria la coordinació i l’acord entre els diferents nodes del sistema. Discuteixi com implementaria l’exclusió mútua, l’elecció de líder, per què és necessari un líder, i la comunicació entre grups. En cas d’utilitzar algun algorisme diferent dels vistos a la teoria del mòdul, especifiqui el pseudocodi corresponent.



L’algorisme d’exclusió mútua distribuïda es important per coordinar l’accés a recursos compartits evitant condicions de carrera i inconsistència en les dades.



L’algoritme “Bully” es adequat per un sistema síncron, permet elegir de forma fiable un nou líder quan l’actual falla i garanteix que el procés amb l’identificador més alt assumeix el nou rol. Un líder simplifica la coordinació, actua com seqüenciador global i garanteix un ordre total dels missatges garantint que tots el nodes els processin en la mateixa seqüència.



Fer servir multicast atòmic assegura que tots els nodes correctes rebin els mateixos missatges. L’ordenació total garanteix que es processin en el mateix ordre.



\### b) Replicació i tolerància a fallades

Argumenti per què és important la tolerància a fallades en un sistema d’aquest tipus, quin tipus de replicació utilitzaria (activa/passiva), com realitzaria la comunicació en grup entre les diferents rèpliques del sistema, quin paradigma de replicació aplicaria (mestre-mestre / mestre-esclau), i quin algorisme de replicació considera més adequat per a aquest sistema (Gossip, Bayou o Coda)?



La tolerància a fallades es important per aconseguir una alta disponibilitat i alt rendiment. La replicació de les dades permet un sistema sense interrupcions per als usuaris quan un o més nodes fallen.



La comunicació entre rèpliques síncrona, garanteix que tots els nodes tinguin una visió consistent dels nodes actius en cada moment. Assegura que el canvis (nous nodes o fallades) es gestionin de manera ordenada.



Un paradigma mestre-esclau amb replicació passiva ja que simplifica el sistema, els clients només interactuen amb el mestre. El mestre executa les operacions i les replica l’estat actualitzat als esclaus.



Un algorisme de replicació passiva es més adient per a un sistema que requereix una alta consistència en temps real. Gossip, Bayou o Coda estan dissenyats per a sistemes desconnectats.



\### c) Gestió de la concurrència i ordenació d’esdeveniments

Discuteix quin tipus de concurrència implementaria en el seu sistema (optimista o pessimista) i quin mètode utilitzaria per dur a terme l’ordenació dels esdeveniments.



Un sistema de concurrència pessimista. Es ideal per a sistemes on les operacions son majoritàriament actualitzacions, com el cas de la publicació de missatges. Detecta els conflictes en el moment de l’accés evitant inconsistències.



L’ordenació dels esdeveniments es gestiona amb rellotges lògics ordenats. El temps físic no és fiable per ordenar esdeveniments en un sistema distribuït.



\### d) Comparació amb Apache Kafka

Compari la plataforma distribuïda proposada en els apartats anteriors amb Apache Kafka, i discuteixi els avantatges i/o inconvenients de la seva plataforma respecte d’aquesta.



\*\*Característiques\*\*



| | Sistema proposat | Apache Kafka |

|---|---:|---|

| Coordinació i consens entre nodes | Utilitza “Bully”. El líder és responsable de la coordinació total. | Depèn d’un sistema de consens robust amb el seu propi protocol (KRaft). Un node “controller” gestiona l’estat del líder de cada partició. |

| Replicació i tolerància a fallades | Replicació passiva (mestre-esclau). | Model de log replicat i particionat. |

| Gestió de concurrència i ordenació | Ordre total mitjançant un seqüenciador central. | Garanteix ordre dins de cada partició. |



\*\*Avantatges i inconvenients\*\*



\- Sistema proposat: la garantia d’orde total simplifica el disseny; el líder pot convertir-se en un coll d’ampolla.

\- Apache Kafka: l’arquitectura per particions és més escalable; la càrrega es divideix en múltiples líders; no garanteix ordre total global.



---



\## Pregunta 2



Compari els conceptes i casos d’ús següents, tenint en compte els seus avantatges, limitacions i escenaris d’aplicació:



\### a) Autenticació: Needham-Schroeder vs Kerberos

Analitzi la seguretat davant d’atacs, l’eficiència en la comunicació i la facilitat d’implementació en sistemes distribuïts.



\*\*Anàlisis\*\*



| Concepte | Needham-Schroeder | Kerberos |

|---|---:|---|

| Seguretat davant d’atacs | Vulnerable a atacs de repetició. | Segur davant atacs de repetició; soluciona amb timestamps. |

| Eficiència en la comunicació | Menys eficient per a sessions múltiples. | Molt eficient per a sessions múltiples. |

| Facilitat d’implementació | Conceptualment més simple. | Més complex d’implementar i gestionar. |



\### b) Serveis altament disponibles: Gossip vs Bayou vs Coda

Compari la tolerància a fallades, la consistència eventual, la disponibilitat i els tipus d’aplicacions per als quals cada protocol és més adequat.



\*\*Anàlisis\*\*



| Protocol | Tolerància i disponibilitat | Consistència | Tipus d’aplicacions |

|---|---:|---|---|

| Gossip | Molt alta; client pot connectar-se amb qualsevol rèplica disponible. | Utilitza segells de temps vectorial. | Aplicacions amb alta disponibilitat i baixa latència de lectura (news, social). |

| Bayou | Dissenyat per entorns mòbils; alta disponibilitat en mode desconnectat. | Transformació operacional i procediments de fusió. | Aplicacions compartides (editores, calendaris). |

| Coda | Molt alta; utilitza AVSG. | Vectors de versió de servidor (CVV). | Sistemes d’arxius distribuïts accessibles des de diferents dispositius. |



\### c) Control de concurrència: Google Docs vs Wikipedia

Descrigui com gestionen la concurrència, la resol




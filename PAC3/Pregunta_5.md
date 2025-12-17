Basant-me exclusivament en els documents proporcionats (específicament el \*\*Capítol 21: Designing Distributed Systems: Google Case Study\*\*, el \*\*Capítol 10: Peer-to-Peer Systems\*\* i el \*\*Paper de Chord\*\*), proposo el següent disseny de middleware.



\### a. Disseny del Middleware i Model de Consistència



\*\*Proposta de Disseny:\*\*

El disseny es basarà en una arquitectura de \*\*magatzem de dades distribuït estructurat\*\*, inspirat en \*\*Bigtable\*\* (descrit a la secció 21.5.3). El sistema organitzarà les dades en taules multidimensionals distribuïdes, on les dades s'indexen per una clau de fila, una clau de columna i una marca de temps.



\*   \*\*Emmagatzematge:\*\* Utilitzarem un sistema de fitxers distribuït subjacent similar a \*\*GFS\*\* (Google File System, secció 21.5.1) per gestionar la replicació física dels blocs de dades (chunks) i garantir la tolerància a fallades bàsica.

\*   \*\*Coordinació:\*\* Utilitzarem un servei de bloqueig distribuït similar a \*\*Chubby\*\* (secció 21.5.2) per a l'elecció de mestres i la gestió de metadades de particions.



\*\*Model de Consistència:\*\*

Triaria un model de \*\*Consistència Eventual\*\* per a la visió global de les dades, combinat amb \*\*Atomicitat a nivell de fila\*\* (Row-level atomicity) per a les escriptures.



\*   \*\*Justificació:\*\*

&nbsp;   1.  \*\*Escalabilitat vs. Consistència Estricta:\*\* Segons el cas d'estudi de Google, mantenir una consistència estricta en un sistema distribuït a gran escala (com un sistema global) penalitza greument el rendiment i la disponibilitat. Bigtable, per exemple, no suporta transaccions completes entre files (cross-row transactions) precisament per mantenir l'escalabilitat (Secció 21.5.3).

&nbsp;   2.  \*\*Atomicitat necessària:\*\* L'atomicitat a nivell de fila garanteix que, tot i que les rèpliques puguin trigar a convergir (eventual), operacions concurrents sobre una mateixa entitat (fila) no deixin les dades en un estat inconsistent o corrupte.

&nbsp;   3.  \*\*Tolerància a fallades:\*\* La consistència eventual permet que el sistema continuï acceptant lectures i escriptures fins i tot si algunes rèpliques no estan disponibles temporalment o si hi ha particions de xarxa, alineant-se amb els requisits de disponibilitat.



\### b. Estratègia d'Escalat



Per gestionar milers de clients i l'augment de dades, l'estratègia es basarà en tres pilars extrets dels documents:



1\.  \*\*Particionament de Dades (Sharding) mitjançant "Tablets":\*\*

&nbsp;   \*   Seguint el model de Bigtable (Secció 21.5.3), l'espai de claus de les taules es dividirà dinàmicament en rangs de files anomenats \*tablets\*.

&nbsp;   \*   A mesura que una taula creix, els \*tablets\* es divideixen automàticament. Això permet distribuir les dades de manera granular entre molts servidors (\*tablet servers\*).

&nbsp;   \*   A diferència del \*hashing\* pur (com a Chord), utilitzar rangs de claus permet consultes eficients per proximitat de claus (localitat de dades).



2\.  \*\*Balanceig de Càrrega Dinàmic:\*\*

&nbsp;   \*   Un node mestre (similar al \*Master\* de GFS o Bigtable) monitoritzarà la càrrega dels servidors. Si un servidor està sobrecarregat o falla, el mestre reassignarà els seus \*tablets\* a altres servidors amb capacitat disponible.

&nbsp;   \*   Aquesta reassignació és ràpida perquè les dades estan emmagatzemades en el sistema de fitxers distribuït subjacent (GFS); moure un \*tablet\* només implica canviar punters de metadades, no copiar físicament totes les dades immediatament (Secció 21.5.3).



3\.  \*\*Replicació Dinàmica:\*\*

&nbsp;   \*   Les dades es replicaran (per defecte 3 còpies, com a GFS - Secció 21.5.1) en diferents màquines i, si és possible, en diferents racks.

&nbsp;   \*   Si un node falla, el sistema detectarà la pèrdua de rèpliques (mitjançant \*heartbeats\*) i iniciarà automàticament la re-replicació de les dades afectades en altres nodes vius per restaurar el factor de replicació desitjat.



\### c. Comparació amb Sistemes Distribuïts Reals (dels documents)



A continuació es compara el disseny proposat amb \*\*OceanStore\*\* i \*\*Chord\*\*, basant-se en els capítols 10 i el paper de Chord.



| Característica | El meu Disseny (Basat en Bigtable/GFS) | OceanStore (Capítol 10, Secció 10.6.2) | Chord (Paper Chord / Capítol 10) |

| :--- | :--- | :--- | :--- |

| \*\*Arquitectura\*\* | Centralitzada/Híbrida (Mestre per control, P2P per dades). | Totalment descentralitzada (Peer-to-Peer global). | Totalment descentralitzada (Peer-to-Peer estructurat). |

| \*\*Consistència\*\* | \*\*Atomicitat a nivell de fila\*\*. Consistència relaxada entre rèpliques (depenent de GFS). | \*\*Híbrida\*\*: \*Promiscuous caching\* (eventual) per a velocitat, però permet consistència forta mitjançant un protocol d'acord Bizantí al "inner ring" per a actualitzacions. | \*\*Eventual\*\*. Durant l'estabilització (joins/failures), les taules de \*fingers\* poden ser inconsistents, fent que les cerques fallin o triguin més, però garanteix correcció eventual (Teoremes 4 i 5 del paper). |

| \*\*Escalabilitat\*\* | \*\*Sharding per rangs (Tablets)\*\*. Escala bé dins d'un datacenter, però el Mestre pot ser un coll d'ampolla si no es dissenya bé (reduint la seva càrrega com fa GFS). | \*\*Tapestry (DHT)\*\*. Utilitza un overlay d'encaminament per prefixos. Escala globalment a milions de nodes gràcies a la localitat de dades i l'encaminament logarítmic. | \*\*Consistent Hashing\*\*. Escala logarítmicament ($O(\\log N)$) tant en estat mantingut com en missatges d'encaminament. No té punt únic de fallada (sense mestre). |

| \*\*Tolerància a Fallades\*\* | Replicació de \*chunks\* i re-replicació ràpida orquestrada pel mestre. Recuperació mitjançant logs d'operacions. | Ús de \*\*codis d'esborrament\*\* (\*erasure coding\*) per arxivar dades, que requereix menys espai que la replicació simple per a la mateixa fiabilitat. | Ús de \*\*llistes de successors\*\*. Si un node falla, el sistema utilitza el següent node viu de la llista per mantenir l'anell connectat i garantir la disponibilitat de les claus. |



\*\*Diferències Clau:\*\*

\*   El meu disseny prioritza el rendiment en un entorn controlat (datacenter) amb un mestre per gestionar el balanceig, mentre que \*\*Chord\*\* i \*\*OceanStore\*\* estan dissenyats per a entorns hostils o d'Internet obert on no hi ha autoritat central.

\*   \*\*OceanStore\*\* ofereix un model de consistència més complex (Acord Bizantí) per gestionar nodes en els quals no es confia, mentre que el meu disseny assumeix que els nodes són fiables (encara que puguin fallar per errors de maquinari), simplificant el model de consistència a atomicitat de fila.


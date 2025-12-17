Basant-me exclusivament en el \*\*Capítol 6 (Indirect Communication)\*\* dels documents proporcionats, aquí tens l'explicació de les diferències entre els quatre sistemes, amb exemples i projectes de programari lliure associats.



\### 1. Group Communication (Comunicació en grup)



\*   \*\*Descripció:\*\* Aquest paradigma ofereix un servei on un missatge s'envia a un grup abstracte i es lliura a tots els membres d'aquest grup. L'emissor no coneix la identitat dels receptors (desacoblament espacial), però normalment requereix que els receptors estiguin actius en el moment de l'enviament (acoblament temporal). Gestiona la pertinença al grup (\*membership\*) i la detecció de fallades.

\*   \*\*Diferència clau:\*\* Està orientat a la difusió \*\*un-a-molts\*\* (\*one-to-many\*) amb garanties de fiabilitat i ordre, sovint utilitzat per mantenir l'estat replicat entre diversos processos.

\*   \*\*Exemple:\*\* Un sistema de pissarra compartida on diversos usuaris dibuixen simultàniament; quan un usuari dibuixa una línia, s'envia un missatge "multicast" a tots els altres membres del grup perquè actualitzin la seva vista.

\*   \*\*Projecte de programari lliure:\*\*

&nbsp;   \*   \*\*JGroups:\*\* Es descriu a la secció 6.2.3 com un \*toolkit\* per a la comunicació fiable en grup escrit en Java. És un projecte de codi obert (\*open source community\*) que forma part de la comunitat de middleware JBoss. Permet als processos unir-se o deixar grups i enviar missatges a tots els membres.



\### 2. Publish-Subscribe (Publicació-subscripció)



\*   \*\*Descripció:\*\* Els "editors" (\*publishers\*) generen esdeveniments i els "subscriptors" (\*subscribers\*) expressen interès en patrons d'esdeveniments específics. El sistema s'encarrega de fer coincidir (\*matching\*) els esdeveniments amb els interessos i lliurar-los.

\*   \*\*Diferència clau:\*\* Ofereix un desacoblament molt fort. A diferència de la comunicació en grup (on s'envia a tothom del grup), aquí el filtratge és clau (basat en canal, tema o contingut). Pot ser \*\*un-a-molts\*\* o molts-a-molts.

\*   \*\*Exemple:\*\* Un sistema de sala de mercats financers (\*dealing room\*). Un proveïdor d'informació publica l'actualització del preu d'una acció (esdeveniment) i el sistema la lliura només als agents de borsa que s'hagin subscrit específicament a aquella acció.

\*   \*\*Projecte de programari lliure:\*\*

&nbsp;   \*   El document (Secció 6.4.3) llista implementacions de l'especificació \*\*JMS\*\* (que suporta el model \*publish-subscribe\*). Entre les implementacions de codi obert esmentades hi ha \*\*Joram\*\* (de l'OW2), \*\*Apache ActiveMQ\*\* i \*\*OpenJMS\*\*.



\### 3. Message Queues (Cues de missatges)



\*   \*\*Descripció:\*\* Proporciona un servei \*\*punt-a-punt\*\* (\*point-to-point\*) on els productors envien missatges a una cua específica i els consumidors els extreuen. La cua actua com a intermediari que emmagatzema el missatge fins que el consumidor està llest.

\*   \*\*Diferència clau:\*\* A diferència dels anteriors, està dissenyat principalment per a comunicació \*\*un-a-un\*\* (\*one-to-one\*). Ofereix desacoblament temporal (el consumidor no ha d'estar actiu quan s'envia el missatge) i espacial. És ideal per al processament de transaccions i la integració d'aplicacions empresarials.

\*   \*\*Exemple:\*\* Un sistema de processament de comandes. Un servidor web rep una comanda i l'envia a una cua. Un altre servidor de facturació, potser més lent o ocupat, recull la comanda de la cua quan queda lliure per processar-la, sense bloquejar el servidor web.

\*   \*\*Projecte de programari lliure:\*\*

&nbsp;   \*   Igual que amb \*publish-subscribe\*, el document cita implementacions de l'estàndard \*\*JMS\*\* (Java Messaging Service) que suporten cues. Els exemples de programari lliure inclouen \*\*Joram\*\*, \*\*Apache ActiveMQ\*\* i \*\*OpenJMS\*\*.



\### 4. Shared Memory (Memòria compartida distribuïda i Espais de Tuples)



\*   \*\*Descripció:\*\* Ofereix l'abstracció d'una memòria compartida entre ordinadors que no comparteixen memòria física. En el cas dels \*\*Espais de Tuples\*\* (\*Tuple Spaces\*), els processos es comuniquen col·locant "tuples" (estructures de dades) en un espai compartit i llegint-les o extraient-les mitjançant patrons.

\*   \*\*Diferència clau:\*\* És un estil de comunicació "generativa". A diferència de l'enviament de missatges, aquí es manipulen dades en un espai persistent. Permet un desacoblament total en temps i espai, i també suporta la sincronització de processos.

\*   \*\*Exemple:\*\* Un programa paral·lel mestre-treballador. El mestre deixa descripcions de tasques (tuples) a l'espai compartit. Els treballadors agafen (\*take\*) una tasca quan estan lliures, la processen i escriuen (\*write\*) el resultat de nou a l'espai.

\*   \*\*Projecte de programari lliure:\*\*

&nbsp;   \*   El document destaca \*\*JavaSpaces\*\* (part de la tecnologia Jini) com a cas d'estudi principal. Tot i que Jini va ser desenvolupat per Sun, el text esmenta que el codi font està disponible a través de la comunitat \*\*Apache River\*\* (encara que el document es refereix a l'època de Sun/Jini). També s'esmenten sistemes de \*middleware\* peer-to-peer com \*\*LIME\*\* i \*\*TOTA\*\* (Secció 6.5.1 i 6.5.2).



\### Resum comparatiu (basat en la Figura 6.27)



| Paradigma | Estil de servei | Patró de comunicació | Escalabilitat |

| :--- | :--- | :--- | :--- |

| \*\*Group Communication\*\* | Basat en comunicació | 1-a-molts | Limitada |

| \*\*Publish-Subscribe\*\* | Basat en comunicació | 1-a-molts | Possible |

| \*\*Message Queues\*\* | Basat en comunicació | 1-a-1 | Possible |

| \*\*Shared Memory (Tuple Spaces)\*\* | Basat en estat | 1-a-1 o 1-a-molts | Limitada |


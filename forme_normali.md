# Forme normali dei database

## 1. Prima forma normale

Una relazione è in *1NF* se e solo se ciascun attributo è definito su un dominio con valori atomici. In altre parole, ciascuna colonna della tabella deve presentare un'unità atomica di informazione, che non va successivamente suddivisa per ricavarne i valori effettivamente utili. Un esempio di violazione di tale regola è:

|   ID  |              nome             | numero_telefono |                    indirizzo                   |      figli_a_carico      |
|:-----:|:-----------------------------:|:---------------:|:----------------------------------------------:|:------------------------:|
| 00001 |  nome: Mario, cognome: Rossi  |  +39 3979206604 |  città: Roma, via: Corso Centrale, civico: 20  | Francesco, Luca, Antonio |
| 00002 | nome: Luigi, cognome: Bianchi |  +39 3500008875 | città: Milano, via: Vicolo Stretto, civico: 11 |      Elisa, Gianluca     |

Una ristrutturazione legale di tale schema potrebbe essere:

|   ID  |  nome | cognome | numero_telefono |  città |       via      | civico |
|:-----:|:-----:|:-------:|:---------------:|:------:|:--------------:|:------:|
| 00001 | Mario |  Rossi  |  +39 3979206604 |  Roma  | Corso Centrale |   20   |
| 00002 | Luigi | Bianchi |  +39 3500008875 | Milano | Vicolo Stretto |   11   |

|   ID  | figlio_a_carico |
|:-----:|:---------------:|
| 00001 |    Francesco    |
| 00001 |       Luca      |
| 00001 |     Antonio     |
| 00002 |      Elisa      |
| 00002 |     Gianluca    |

---

## 2. Seconda forma normale

Prima di parlare della seconda forma normale è necessario parlare delle dipendenze funzionali.

### 2.1 Le dipendenze funzionali

Data una relazione $r$ su uno schema $R(X)$ e due sottoinsiemi non-vuoti $Y$ e $Z$ degli attributi di $X$, diciamo che c'è una *dipendenza funzionale* tra $Y$ e $Z$ se, per ogni paio di tuple $t_1$ e $t_2$ di $r$ aventigli stessi valori negli attributi $Y$, $t_1$ e $t_2$ hanno anche gli stessi valori negli attributi di $Z$. Una dipendenza funzionale tra due gruppi di attributi $Y$ e $Z$ è indicata come $Y \rightarrow Z$.

Volendo fare un esempio concreto di questa notazione, sappiamo che, data una relazione $R(X)$, $Y \subseteq X$ è *chiave* di $X$ se:
- è una sua superchiave: $Y \rightarrow X$
- minimale: $\nexists Z | \left( Z \subset Y \wedge Z \rightarrow X \right)$

A livello pratico, possiamo vedere una dipendenza funzionale tra due sottoinsiemi di attributi $X,Y$ della stessa relazione $R(X)$ come: se fisso il valore degli attributi di $X$, determinerò in maniera univoca il valore degli attributi di $Y$.

Vediamo un esempio reale: immaginiamo di gestire un'azienda che gestisce più impiegati e progetti, ciascuno di essi con un responsabile e un budget ben definiti. Abbiamo:

- il progetto *Alfa*, con un budget di *€100000* e con *Roberto Verdi* come responsabile
- il progetto *Beta*, con un budget di *€250000* e con *Matteo Neri* come responsabile

Immaginiamo che l'allocazione degli impiegati sia descritta dalla seguente relazione:

|  ID  |         nome        | progetto | budget |  responsabile |
|:----:|:-------------------:|:--------:|:------:|:-------------:|
| 0001 |   Adele Cremonesi   |   Alfa   | 100000 | Roberto Verdi |
| 0002 |      Ambra Boni     |   Beta   | 250000 |  Matteo Neri  |
| 0003 | Ferdinando Pagnotto |   Alfa   | 100000 | Roberto Verdi |
| 0004 |    Adriana Greece   |   Alfa   | 100000 | Roberto Verdi |
| 0005 |  Federico Lucchese  |   Beta   | 250000 |  Matteo Neri  |

È evidente che le colonne *budget* e *responsabile* siano ridontanti, in quanto gli stessi dati vengono ripetuti più volte. Di fatto, una volta fissato il valore dell'attributo *progetto* di una tupla, il contenuto delle sue celle *budget* e *responsabile* sono derivate da esso. In tal caso, infatti, si la dipendenza funzionale

$$\{ \text{progetto} \} \rightarrow \{ \text{budget, responsabile} \}$$

Tale dipendenza è la più evidente, ma non l'unica. In maniera analoga, possiamo notare che, fissato il valore di *ID* (che assumiamo sia la chiave primaria di questa relazione), tutti gli altri attributi assumono un valore in maniera univoca. Di conseguenza,

$$\{ \text{ID} \} \rightarrow \{ \text{nome, progetto, budget, responsabile} \}$$

## 2.2 Richieste della 2NF

Una relazione è in *seconda forma normale* se:

1. È in 1NF
2. Tutti i suoi attributi non-chiave dipendeono dall'intera chiave, ciè non possiede attributi che dipendono soltanto da una parte della chiave

Vediamo un esempio di violazione di questa regola. Immaginiamo di gestire il database di un azienda che possiede magazzini sparsi in giro per una città:

| _codice_articolo_ | _codice_magazzino_ | descrizione_articolo | indirizzo_magazzino | quantità |
|:-----------------:|:------------------:|:--------------------:|:-------------------:|:--------:|
|       AAAAA       |        0001        |   Gioco per bambini  |   Via delle Azalee  |    43    |
|       BBBBB       |        0002        |      Mele rosse      |     Via Rosmini     |    12    |
|       CCCCC       |        0003        |  Computer portatile  |   Via Delle Coste   |     9    |
|       BBBBB       |        0001        |      Mele rosse      |     Via Rosmini     |    72    |
|       CCCCC       |        0002        |  Computer portatile  |   Via Delle Coste   |    18    |

Possiamo notare che sono presenti più dipendenze funzionali, tuttavia assumendo che la chiave della relazione sia $\{ \text{codice\_articolo, codice\_magazzino} \}$ alcune di essere sono in contraddizione con la 2NF:

$$ \{ \text{codice\_articolo} \} \rightarrow \{ \text{descrizione\_articolo} \} \\
\{ \text{codice\_magazzino} \} \rightarrow \{ \text{indirizzo\_magazzino} \}  $$

In entrambi i casi, infatti, il membro di destra dipende solo da *parte* della chiave, e non da tutta la chiave. Per risolvere questo problema, ristrutturiamo la relazione come segue:

| _codice_articolo_ | _codice_magazzino_ | quantità |
|:-----------------:|:------------------:|:--------:|
|       AAAAA       |        0001        |    43    |
|       BBBBB       |        0002        |    12    |
|       CCCCC       |        0003        |     9    |
|       BBBBB       |        0001        |    72    |
|       CCCCC       |        0002        |    18    |

| _codice_articolo_ | descrizione_articolo |
|:-----------------:|----------------------|
|       AAAAA       | Gioco per bambini    |
|       BBBBB       | Mele rosse           |
|       CCCCC       | Computer portatile   |

| _codice_magazzino_ | indirizzo_magazzino |
|:------------------:|---------------------|
|        0001        | Via delle Azalee    |
|        0002        | Via Rosmini         |
|        0003        | Via Delle Coste     |

---

## 3. Terza forma normale

Una relazione è in 3NF se e solo se:

1. È in 2NF
2. Tutti gli attributi non-chiave dipendono direttamente dalla chiave, cioè non possiede attributi che dipendono da altri attributi che non sono in chiave

La terza forma normale elimina la dipendenza transitiva degli attributi della chiave.

Vediamo un esempio che non rispetta questa regola:

| _codice_impiegato_ |       nome       |      reparto     | telefono_reparto |
|:------------------:|:----------------:|:----------------:|:----------------:|
|        00001       |   Iacopo Pisano  |    Contabilità   |  +39 03593408982 |
|        00002       |  Verdiana Manna  |    Produzione    |  +39 03602737570 |
|        00003       |   Jole Udinesi   |    Contabilità   |  +39 03593408982 |
|        00004       | Serena Trevisani |    Produzione    |  +39 03602737570 |
|        00005       |   Duilio Piazza  |    Contabilità   |  +39 03593408982 |
|        00006       |   Giusy DeRose   | Servizio clienti |  +39 03205724106 |

Possiamo verificare la presenza delle seguenti dipendenze funzionali

$$ \{ \text{codice\_impiegato} \} \rightarrow \{ \text{reparto} \} \\
\{ \text{reparto} \} \rightarrow \{ \text{telefono\_reparto} \}  $$

La prima dipendenza è assolutamente lecita, tuttavia la seconda non ha una chiave al membro di sinistra, quindi è necessario scomporre la tabella in più relazioni:

| _codice_impiegato_ |       nome       |      reparto     |
|:------------------:|:----------------:|:----------------:|
|        00001       |   Iacopo Pisano  |    Contabilità   |
|        00002       |  Verdiana Manna  |    Produzione    |
|        00003       |   Jole Udinesi   |    Contabilità   |
|        00004       | Serena Trevisani |    Produzione    |
|        00005       |   Duilio Piazza  |    Contabilità   |
|        00006       |   Giusy DeRose   | Servizio clienti |

|     _reparto_    | telefono_reparto |
|:----------------:|:----------------:|
|    Contabilità   |  +39 03593408982 |
|    Produzione    |  +39 03602737570 |
|    Contabilità   |  +39 03593408982 |
|    Produzione    |  +39 03602737570 |
|    Contabilità   |  +39 03593408982 |
| Servizio clienti |  +39 03205724106 |

> Da notare, ora, che la relazione presentata per spiegare le dipendenze funzionali nel paragrafo 2.1 andrebbe scomposta in due relazioni per rispettare la 3NF: $\{ \text{\underline{ID}, nome, progetto} \}$ e $\{ \text{\underline{progetto}, budget, responsabile} \}$



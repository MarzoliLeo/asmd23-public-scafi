# Implementazione Lab 11 - Aggregate Computing.

Di seguito si riporta una breve descrizione di come è stata pensata la soluzione ai vari task richiesti per il lab 11.

Per vedere tutte le implementazioni riferirsi alla classe: 
* [Programs](src/main/scala/lab/demo/Programs.scala)

## Case 9: Experimenting with Branch vs Mux

In questo task, ho sperimentato con la funzione mux e il branching (if statement) per contare da 0 a 1000 quando il sensore sense1 è attivo, mantenendo il valore fisso a 1000 al termine del conteggio. Quando il sensore non è attivo, il contatore rimane a 0. Ho utilizzato mux per gestire la logica decisionale e ho esaminato cosa succede con più click. Ho poi implementato lo stesso comportamento utilizzando un blocco if per fare il confronto.

## Case 12: Experimenting with Scala Interoperability (Return a `Set[Int]`)
In questo task, ho raccolto in ogni nodo l'insieme degli ID dei vicini utilizzando la funzione foldhood. Per far sì che il tipo venga inferito correttamente, ho specificato che l'insieme è un Set[ID].

## Case 8: Experiment with Pairs
In questo task, ho utilizzato minHoodPlus per ottenere l'ID del vicino più vicino per ogni nodo. Ho costruito una coppia composta dalla distanza (nbrRange) e dall'ID (nbr{mid}), e grazie a minHoodPlus, il confronto tra le coppie è stato gestito correttamente.

## Case 14: Understand Gossiping (Max ID)
In questo task, ho propagato il massimo valore di ID attraverso la rete utilizzando maxHoodPlus. Ho riscontrato che, a causa di cambiamenti nella rete, la propagazione non si ripara correttamente, quindi ho utilizzato maxHoodPlus per gestire la propagazione del massimo ID in modo efficiente.

## Case 16: Defining a Gradient that Stretches Distances

In questo task, ho definito un gradiente che allunga le distanze in modo che, dove sense2 è vero, queste diventino 5 volte più grandi. Per farlo, ho modificato l'uso di nbrRange, applicando un fattore moltiplicativo di 5 quando sense2 è vero.


## Propagating a Pair (Partition)
Sono partito dall'esempio 16, importando `Builtins.Bounded.of_i`, e ho propagato una coppia invece di un valore double, come nel case 8. La prima componente della coppia è la distanza, mentre la seconda è l'ID della sorgente. Fortunatamente, minHoodPlus funziona correttamente con le coppie, quindi ho potuto usare nbr per ottenere separatamente la prima e la seconda componente. Alla fine, ho proiettato sulla seconda componente per ottenere le partizioni etichettate con un diverso ID.

## Do Channel 
Il blocco di codice definisce una nuova classe, Main16DoChannel, che estende il comportamento di un programma aggregato includendo funzionalità per creare una trasmissione di dati (broadcast) attraverso la rete sfruttando un gradiente precedentemente calcolato. Lo scopo finale è quello di calcolare una distanza bloccata basata sulla trasmissione del risultato del gradiente, utile per costruire una comunicazione di canale tra nodi nella rete.

1. **Broadcast**: La funzione broadcast è una funzione generica che permette di propagare un dato (initial) da un nodo sorgente (definito da source) al resto della rete. Si basa su:

    * Una condizione di partenza (source), che attiva la trasmissione.
    * Un valore iniziale da propagare.
    * Una funzione di accumulo che gestisce il modo in cui i dati vengono combinati quando attraversano i nodi vicini.
    * La funzione utilizza rep (che mantiene uno stato tra le esecuzioni) e foldhoodPlus, una funzione che aggrega i valori provenienti dai vicini, usando la funzione di accumulo specificata. Se il nodo è sorgente, viene ritrasmesso il valore iniziale, altrimenti viene calcolato sulla base dei valori ricevuti dai vicini.

Viene quindi calcolata la distanza bloccata come la minima distanza tra i nodi, utilizzando un’altra aggregazione con foldhoodPlus. La funzione minimizza le distanze propagando il valore del broadcast ricevuto dai vicini.
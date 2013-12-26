## Il costruttore `Array`

A causa dell'ambiguità con cui il costruttore `Array` tratta i propri parametri si consiglia caldamente di usare sempre la forma letterale - tramite la notazione `[]` - quando si creano nuovi array.

    [1, 2, 3]; // Risultato: [1, 2, 3]
    new Array(1, 2, 3); // Risultato: [1, 2, 3]

    [3]; // Risultato: [3]
    new Array(3); // Risultato: []
    new Array('3') // Risultato: ['3']

Quando al costruttore `Array` viene passato un unico argomento e quell'unico argomento è una istanza di `Number`, il costruttore restituirà un array *sparso* con la proprietà `length` impostata al valore dell'argomento. Va notato che, in questo modo, la proprietà `length` del nuovo array è l'unica che verrà impostata: gli effettivi indici dell'array non verranno inizializzati.

    var arr = new Array(3);
    arr[1]; // undefined
    1 in arr; // false, poiché l'indice non è stato inizializzato

La possibilità di impostare la lunghezza dell'array in anticipo è utile solamente in pochi casi, come nell'eventualità della ripetizione di una stringa, quando consente di evitare l'uso di un ciclo `for` nel codice.

    new Array(count + 1).join(stringToRepeat);

### Conclusione

L'uso del costruttore `Array` dovrebbe essere evitato. La notazione letterale è certamente preferibile: è più breve, ha una sintassi più chiara, e aumenta la leggibilità del codice.

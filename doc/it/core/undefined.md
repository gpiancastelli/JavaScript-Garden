## `undefined` e `null`

JavaScript possiede due valori distinti per esprimere la mancanza di un valore: `null` e `undefined`, di cui il secondo è quello più utile.

### Il valore `undefined`

`undefined` è un tipo con esattamente un valore: `undefined`.

Il linguaggio definisce anche una variabile globale che ha il valore `undefined`; anche questa variabile è chiamata `undefined`. Tuttavia, questa variabile non è *né* una costante né una parola chiave del linguaggio. Questo significa che il suo *valore* può facilmente essere sovrascritto.

> **Nota su ES5:** `undefined` in ECMAScript 5 **non è più** *modificabile* in modalità strict, ma il suo nome può ancora essere nascosto, per esempio da una funzione con il nome `undefined`.

Il valore `undefined` viene restituito nei seguenti casi:

 - accesso alla variabile globale `undefined` (se il suo valore non è stato modificato);
 - accesso a una variabile dichiarata *ma non* ancora inizializzata;
 - valore di ritorno implicito per le funzioni che non hanno istruzioni `return`;
 - valore di ritorno per istruzioni `return` che non restituiscono esplicitamente un valore;
 - risultato della ricerca di proprietà inesistenti;
 - parametri di funzione a cui non è stato esplicitamente passato un valore;
 - qualsiasi cosa a cui sia stato assegnato il valore `undefined`;
 - qualsiasi espressione della forma `void(expression)`.

### Gestire cambiamenti al valore di `undefined`

Dato che la variabile globale `undefined` mantiene solamente una copia del *valore* effettivo di `undefined`, assegnarle un nuovo valore **non** modifica il valore del *tipo* `undefined`.

Tuttavia, per confrontare un valore con il valore di `undefined`, è necessario prima ottenere il valore di `undefined`.

Per proteggere il codice contro la possibilità di sovrascrittura della variabile `undefined`, una tecnica comunemente usata prevede di aggiungere un parametro a una [funzione involucro anonima](#function.scopes) a cui non viene assegnato alcun argomento.

    var undefined = 123;
    (function(something, foo, undefined) {
        // undefined nell'ambito locale fa nuovamente
        // riferimento al valore `undefined`

    })('Hello World', 42);

Un altro modo di ottenere lo stesso effetto sarebbe quello di usare una dichiarazione all'interno della funzione.

    var undefined = 123;
    (function(something, foo) {
        var undefined;
        ...

    })('Hello World', 42);

L'unica differenza è che questa versione risulta più lunga di 4 byte nel caso venga minificata, e che non c'è nessun'altra istruzione `var` all'interno della funzione involucro anonima.

### Usi di `null`

Mentre `undefined` nel contesto del linguaggio JavaScript viene soprattutto usato nel senso del tradizionale *null*, l'effettivo `null` (sia come letterale che come tipo) è più o meno semplicemente un altro tipo di dato.

Viene usato internamente da JavaScript in alcuni casi (come la dichiarazione della termine della catena dei prototipi, che si indica impostando `Foo.prototype = null`), ma in quasi tutti i casi può essere rimpiazzato da `undefined`.

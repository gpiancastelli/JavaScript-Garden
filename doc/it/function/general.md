## Dichiarazioni di funzione ed espressioni funzione

Le funzioni in JavaScript sono oggetti di prima classe. Questo significa che possono essere utilizzati come qualsiasi altro valore. Un utilizzo comune di questa caratteristica consiste nel passare una *funzione anonima* come callback a un'altra funzione, possibilmente asincrona.

### La dichiarazione `function`

    function foo() {}

La funzione appena definita viene [hoisted](#function.scopes) prima che l'esecuzione del programma cominci; in questo modo, è disponibile *ovunque* nell'ambito in cui è stata *definita*, persino se viene invocata nel codice sorgente prima della sua effettiva definizione.

    foo(); // funziona perché foo viene creata prima di eseguire questa istruzione
    function foo() {}

### L'espressione `function`

    var foo = function() {};

L'esempio seguente assegna una funzione *anonima* alla variabile `foo`. 

    foo; // 'undefined'
    foo(); // questa istruzione solleva un errore di tipo TypeError
    var foo = function() {};

A causa del fatto che la dichiarazione `var` hoists il nome di variabile `foo` prima che l'effettiva esecuzione del codice abbia inizio, `foo` è già definita quando il programma viene eseguito.

Ma dato che le istruzioni di assegnamento avvengono solo a tempo di esecuzione, a `foo` viene assegnato il valore predefinito [undefined](#core.undefined) prima che il corrispondente codice venga eseguito.

### Espressioni funzione con nome

Un altro caso particolare è l'assegnamento di funzioni con nome.

    var foo = function bar() {
        bar(); // funziona
    }
    bar(); // ReferenceError

In questo caso `bar` non è visibile nell'ambito più esterno, dato che la funzione viene solo assegnata a `foo`, ma all'interno di `bar` quello stesso nome è visibile. Questo è dovuto al modo in cui la [risoluzione dei nomi](#function.scopes) funziona in JavaScript: il nome di una funzione è *sempre* visibile nell'ambito locale della funzione stessa.

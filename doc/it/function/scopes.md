## Ambiti di visibilità e spazi di nomi

Sebbene JavaScript supporti la sintassi per delimitare un blocco di codice con una coppia di parentesi graffe, ai blocchi non è associato un ambito di visibilità. L'unico ambito di visibilità che rimane nel linguaggio è l'ambito delle *funzioni*.

    function test() { // un ambito di visibilità
        for (var i = 0; i < 10; i++) { // non è un ambito di visibilità
            // conteggio
        }
        console.log(i); // 10
    }

> **Nota:** Quando non viene usata in un assegnamento, in una istruzione `return` o come argomento di una funzione, la notazione `{...}` verrà interpretata come un blocco e **non** come un letterale oggetto. Questo, combinato con [l'inserimento automatico dei punti e virgola](#core.semicolon), può condurre a errori difficili da individuare.

JavaScript è anche privo di spazi di nomi distinti, quindi ogni cosa viene definita in un unico spazio di nomi *condiviso globalmente*.

Ogni volta che si fa riferimento a una variabile, JavaScript attraverserà verso l'alto tutti gli ambiti di visibilità fino a quando non la trova. Nel caso in cui raggiunga l'ambito globale e ancora non riesca a trovare il nome richiesto, solleverà un errore di tipo `ReferenceError`.

### Il flagello delle variabili globali

    // programma A
    foo = '42';

    // programma B
    var foo = '42'

I due programmi di questo esempio **non** hanno lo stesso effetto. Il programma A definisce una variabile di nome `foo` nell'ambito *globale*, mentre il programma B definisce `foo` nell'ambito *corrente*.

L'effetto è **completamente** diverso: l'omissione di `var` può avere importanti implicazioni.

    // ambito globale
    var foo = 42;
    function test() {
        // ambito locale
        foo = 21;
    }
    test();
    foo; // 21

L'omissione della istruzione `var` all'interno della funzione `test` implica che l'assegnamento sovrascriverà il valore globale di `foo`. Sebbene questo possa non sembrare granché a prima vista, l'omissione di `var` in mezzo a migliaia di righe di codice JavaScript può introdurre errori molto difficili da individuare.
    
    // ambito globale
    var items = [/* una qualche lista */];
    for (var i = 0; i < 10; i++) {
        subLoop();
    }

    function subLoop() {
        // ambito di subLoop
        for (i = 0; i < 10; i++) { // omissione di var
            // fai qualcosa di meraviglioso!
        }
    }
    
Il ciclo più esterno terminerà dopo la prima invocazione di `subLoop`, dato che `subLoop` sovrascrive il valore globale di `i`. L'uso di `var` nel secondo ciclo `for` avrebbe facilmente consentito di evitare questo errore. L'istruzione `var` non dovrebbe essere **mai** omessa a meno che l'effetto *desiderato* non sia quello di influenzare l'ambito più esterno.

### Variabili locali

Le variabili locali in JavaScript possono presentarsi solamente in forma di parametri di [funzione](#function.general) e di variabili dichiarate tramite l'istruzione `var`.

    // ambito globale
    var foo = 1;
    var bar = 2;
    var i = 2;

    function test(i) {
        // ambito locale della funzione test
        i = 5;

        var foo = 3;
        bar = 4;
    }
    test(10);

Mentre `foo` e `i` sono variabili locali all'interno dell'ambito della funzione `test`, l'assegnamento di `bar` sovrascriverà il valore della variabile globale con quello stesso nome.

### Innalzamento

JavaScript **innalza** le dichiarazioni. Questo significa che sia le istruzioni `var` sia le dichiarazioni di funzione verranno spostate in cima all'ambito che le racchiude.

    bar();
    var bar = function() {};
    var someValue = 42;

    test();
    function test(data) {
        if (false) {
            goo = 1;
        } else {
            var goo = 2;
        }
        for (var i = 0; i < 100; i++) {
            var e = data[i];
        }
    }

Questo codice viene trasformato prima che l'esecuzione cominci. JavaScript sposta le istruzioni `var` e le dichiarazioni di funzione in cima al più vicino ambito che le racchiude.

    // le istruzioni var vengono spostate qui
    var bar, someValue; // il loro valore predefinito è 'undefined'

    // anche la dichiarazione di funzione è stata spostata in alto
    function test(data) {
        var goo, i, e; // spostate perché ai blocchi non è associato un ambito
        if (false) {
            goo = 1;
        } else {
            goo = 2;
        }
        for (i = 0; i < 100; i++) {
            e = data[i];
        }
    }

    bar(); // genera un TypeError dato che bar è ancora 'undefined'
    someValue = 42; // gli assegnamenti non vengono spostati
    bar = function() {};

    test();

La mancanza di un ambito associato ai blocchi non solo provocherà lo spostamento delle istruzioni `var` al di fuori dei cicli e dei loro corpi, ma renderà anche il risultato di certi costrutti `if` poco intuitivo.

Nel codice originale, sebbene l'istruzione `if` sembrasse modificare la *variabile globale* `goo`, in realtà modifica la *variabile locale* - dopo che l'innalzamento è stato applicato.

Senza sapere dell'innalzamento, si potrebbe sospettare che il codice seguente sollevi un errore di tipo `ReferenceError`.

    // controlla se SomeImportantThing è stato inizializzata
    if (!SomeImportantThing) {
        var SomeImportantThing = {};
    }

Ma naturalmente questo funziona a causa del fatto che l'istruzione `var` è stata spostata in cima all'*ambito globale*.

    var SomeImportantThing;

    // altre istruzioni che potrebbero inizializzare SomeImportantThing, o no

    // assicuriamoci che esista
    if (!SomeImportantThing) {
        SomeImportantThing = {};
    }

### L'ordine nella risoluzione dei nomi

Tutti gli ambiti in JavaScript, compreso quello *globale*, contengono la definizione del nome speciale [`this`](#function.this), che fa riferimento all'*oggetto corrente*. 

Gli ambiti di funzione contengono anche la definizione del nome [`arguments`](#function.arguments), che contiene gli argomenti passati alla funzione.

Per esempio, quando si prova ad accedere alla variabile di nome `foo` all'interno dell'ambito di una funzione, JavaScript cercherà quel nome nell'ordine seguente:

 1. Tra le istruzioni `var` (i.e. `var foo`) nel corpo della funzione.
 2. Tra i parametri della funzione.
 3. Nel nome della funzione (i.e. se la funzione è chiamata `foo`).
 4. Nell'ambito di visibilità esterno successivo, ricominciando dal punto **#1**.

> **Nota:** La presenza di un parametro di una funzione chiamato `arguments` **impedirà** la creazione dell'oggetto `arguments` predefinito.

### Spazi di nomi

Un problema comune associato alla presenza di un unico spazio di nomi globale è la probabilità di incorrere in conflitti tra nomi di variabili uguali. In JavaScript, questo problema si può facilmente evitare con l'aiuto di *involucri anonimi*.

    (function() {
        // uno "spazio di nomi" autosufficiente
        
        window.foo = function() {
            // una chiusura esposta
        };

    })(); // esegui la funzione immediatamente

Le funzioni anonime sono considerate [espressioni](#function.general); quindi, per essere invocabili, devono prima essere valutate.

    ( // valuta la funzione all'interno delle parentesi
    function() {}
    ) // e restituisci l'oggetto funzione
    () // invoca il risultato della valutazione

Esistono altri modi di valutare e invocare direttamente l'espressione funzione, che si comporta allo stesso modo pur usando una sintassi differente.

    // alcuni altri stili per invocare direttamente una funzione
    !function(){}()
    +function(){}()
    (function(){}());
    // e così via...

### Conclusione

Si raccomanda di usre sempre un *involucro anonimo* per incapsulare codice nel proprio spazio di nomi. Questo non solo garantisce di proteggere il codice dai conflitti di nomi, ma consente anche una migliore modularizzazione dei programmi.

Inoltre, l'uso di variabili globali è considerato una pratica da **evitare**. **Qualsiasi** loro utilizzo indica codice scritto male che è soggetto a errori e difficile da manutenere.
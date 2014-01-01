### `setTimeout` e `setInterval`

Dato che JavaScript è asincrono, è possibile programmare l'esecuzione di una funzione usando le funzioni `setTimeout` e `setInterval`.

> **Nota:** I timeout **non** sono parte dello standard ECMAScript. Sono implementati come parte del [DOM][1].

    function foo() {}
    var id = setTimeout(foo, 1000); // restituisce una istanza di Number > 0

Quando `setTimeout` viene invocata, restituisce l'ID del timeout e programma l'esecuzione di `foo` **approssimativamente** mille millisecondi nel futuro. La funzione `foo` verrà quindi eseguita **una sola volta**.

A seconda della risoluzione del timer nel motore JavaScript che esegue il codice, e del fatto che JavaScript viene eseguito in un singolo thread e altro codice che viene eseguito potrebbe bloccare il thread, **non è assolutamente** sicuro che la funzione verrà eseguita con l'esatto ritardo specificato nella invocazione di `setTimeout`.

La funzione che viene passata come primo parametro verrà invocata dall'*oggetto globale*, e questo significa che [`this`](#function.this) all'interno della funzione invocata fa riferimento all'oggetto globale.

    function Foo() {
        this.value = 42;
        this.method = function() {
            // this fa riferimento all'oggetto globale
            console.log(this.value); // stamperà undefined
        };
        setTimeout(this.method, 500);
    }
    new Foo();


> **Nota:** Dato che `setTimeout` accetta un **oggetto funzione** come primo parametro, un errore comune è invocarla come `setTimeout(foo(), 1000)`, che userà il **valore di ritorno** della invocazione di `foo` anziché la funzione `foo`. La maggior parte delle volte questo è un errore che passerà sotto silenzio, dato che quando la funzione restituisce `undefined` `setTimeout` **non** solleverà alcun errore.

### Impilare invocazioni con `setInterval`

Mentre `setTimeout` esegue la funzione una sola volta, `setInterval` - come suggerisce il suo nome - eseguirà la funzione **ogni** `X` millisecondi, ma il suo uso è sconsigliato.

Quando il codice che si trova in esecuzione blocca l'invocazione del timeout, `setInterval` continua comunque a innescare invocazioni alla funzione specificata. Questo può, specialmente per piccoli intervalli, risultare in una impilazione di invocazioni di funzione.

    function foo() {
        // qualche operazione bloccante che dura 1 secondo
    }
    setInterval(foo, 1000);

In questo esempio, `foo` verrà invocata una volta e poi si bloccherà per un secondo.

Mentre `foo` blocca il codice, `setInterval` continuerà a programmare ulteriori invocazioni alla funzione. Ora, quando `foo` è terminata, ci saranno già **dieci** invocazioni aggiuntive in attesa di essere eseguite.

### Gestire codice potenzialmente bloccante

La soluzione più facile, e la più controllabile, è usare `setTimeout` all'interno della funzione stessa.

    function foo() {
        // qualche operazione bloccante che dura 1 secondo
        setTimeout(foo, 1000);
    }
    foo();

Questa soluzione non solo incapsula l'invocazione di `setTimeout`, ma evita la formazione di una pila di invocazioni e fornisce un controllo aggiutivo: ora è `foo` stessa che può decidere se vuole essere eseguita di nuovo oppure no.

### Cancellare manualmente i timeout

Per cancellare i timeout e gli intervalli si passano i rispettivi ID a `clearTimeout` o `clearInterval`, a seconda di quale funzione di impostazione è stata precedentemente usata.

    var id = setTimeout(foo, 1000);
    clearTimeout(id);

### Cancellare tutti i timeout

Non essendoci alcun metodo predefinito per cancellare tutti i timeout e/o gli intervalli è necessario usare la forza bruta per ottenere questa funzionalità.

    // cancella "tutti" i timeout
    for (var i = 1; i < 1000; i++) {
        clearTimeout(i);
    }

Ma potrebbero ancora esserci timeout che non sono influenzati da questo numero arbitrario. Un altro modo di fare questo è considerare che l'ID dato a un timeout viene incrementato di uno ogni volta che si invoca `setTimeout`.

    // cancella "tutti" i timeout
    var biggestTimeoutId = window.setTimeout(function() {}, 1),
        i;
    for (i = 1; i <= biggestTimeoutId; i++) {
        clearTimeout(i);
    }

Anche se questo esempio funziona su tutti i browser odierni più importanti, non è specificato che gli ID debbano essere ordinati in questo modo, e potrebbe cambiare. Quindi, si raccomanda piuttosto di tenere traccia di tutti gli ID di timeout in modo da poterli cancellare specificatamente.

### Uso nascosto di `eval`

`setTimeout` e `setInterval` possono anche accettare una stringa come primo parametro. Questa caratteristica non dovrebbe **mai** essere usata perché fa internamente uso di `eval`.

> **Nota:** Dato che le funzioni di timeout **non** sono specificate dallo standard ECMAScript, l'esatto funzionamento quando una stringa viene passata come primo argomento potrebbe essere diverso nelle varie implementazioni di JavaScript. Per esempio, JScript di Microsoft usa il costruttore `Function` al posto di `eval`.

    function foo() {
        // verrà invocata
    }

    function bar() {
        function foo() {
            // non verrà mai invocata
        }
        setTimeout('foo()', 1000);
    }
    bar();

Dato che in questo caso `eval` non viene invocata [direttamente](#core.eval), la string passata a `setTimeout` verrà eseguita nell'*ambito globale*; quindi non userà la variabile locale `foo` contenuta nell'ambito di `bar`.

Si raccomanda inoltre di **non** usare una stringa per passare argomenti alla funzione che verrà invocata da una qualsiasi delle due funzioni di timeout.

    function foo(a, b, c) {}
    
    // non usare MAI questo
    setTimeout('foo(1, 2, 3)', 1000)

    // si usi invece una funzione anonima
    setTimeout(function() {
        foo(a, b, c);
    }, 1000)

> **Nota:** Sebbene sia anche possibile usare la forma `setTimeout(foo, 1000, a, b, c)`, essa è sconsigliata in quanto potrebbe portare a sofisticati errori quando usata con i [metodi](#function.this).

### Conclusione

Una stringa non dovrebbe **mai** essere usata come parametro di `setTimeout` o `setInterval`. È un chiaro segno di qualità **veramente** bassa del codice quando argomenti devono essere passati alla funzione che viene invocata. Dovrebbe essere passata una *funzione anonima* che si prenda cura della reale invocazione.

Inoltre, l'uso di `setInterval` dovrebbe essere evitato perché il suo meccanismo di programmazione delle invocazini non viene bloccato dal codice JavaScript in esecuzione.

[1]: http://en.wikipedia.org/wiki/Document_Object_Model "Document Object Model"


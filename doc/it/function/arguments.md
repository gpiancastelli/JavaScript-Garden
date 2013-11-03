## L'oggetto `arguments`

Ogni ambito di funzione in JavaScript può accedere alla variabile speciale `arguments`, che fa riferimento a una lista di tutti gli argomenti che sono stati passati alla funzione.

> **Nota:** Nel caso in cui `arguments` sia già stato definito all'interno dell'ambito della funzione tramite una istruzione `var` o come nome di un parametro formale, l'oggetto `arguments` non verrà creato.

L'oggetto `arguments` **non** è un `Array`. Sebbene possieda parte della semantica di un array - ossia la proprietà `length` - non eredita da `Array.prototype` e in effetti è un `Object`.

Per questo motivo, **non** è possibile invocare i metodi standard degli array come `push`, `pop` o `slice` su `arguments`. Sebbene l'iterazione tramite un normale ciclo `for` funzioni bene, è necessario convertirlo in un vero `Array` per poter utilizzare i metodi degli array su di esso.

### Conversione in un `Array`

Il codice seguente restituirà un nuovo `Array` contenente tutti gli elementi dell'oggetto `arguments`.

    Array.prototype.slice.call(arguments);

Dato che questa conversione è **lenta**, si raccomanda di **non** usarla in sezioni di codice critiche per le prestazioni.

### Passaggio di argomenti

L'esempio seguente illustra il metodo consigliato per passare argomenti da una funzione a un'altra.

    function foo() {
        bar.apply(null, arguments);
    }
    function bar(a, b, c) {
        // fai qualcosa qui
    }

Un altro trucco consiste nell'usare `call` e `apply` insieme per creare involucri veloci e slegati.

    function Foo() {}

    Foo.prototype.method = function(a, b, c) {
        console.log(this, a, b, c);
    };

    // crea una versione slegata di "method" 
    // Accetta i parametri: this, arg1, arg2, ..., argN
    Foo.method = function() {
        // risultato: Foo.prototype.method.call(this, arg1, arg2, ..., argN)
        Function.call.apply(Foo.prototype.method, arguments);
    };

### Parametri formali e indici di `arguments`

L'oggetto `arguments` crea funzioni per la **lettura** e la **scrittura** di entrambe le proprie proprietà, così come per i parametri formali della funzione.

Come risultato, la modifica del valore di un parametro formale provocherà anche il cambiamento del valore della corrispondente proprietà dell'oggetto `arguments`, e viceversa.

    function foo(a, b, c) {
        arguments[0] = 2;
        a; // 2

        b = 4;
        arguments[1]; // 4

        var d = c;
        d = 9;
        c; // 3
    }
    foo(1, 2, 3);

### Miti e verità sulle prestazioni

L'unico caso in cui l'oggetto `arguments` non viene creato è quando è dichiarato come nome all'interno di una funzione o come uno dei suoi parametri formali. Non importa se viene usato oppure no.

Entrambi i metodi di **lettura** e **scrittura** vengono **sempre** creati; quindi, usarlo non ha quasi alcun impatto sulle prestazoini, specialmente non nel codice del mondo reale dove c'è più di un semplice accesso alle proprietà dell'oggetto `arguments`.

> **Nota su ES5:** Questi metodi di *lettura* e *scrittura* non vengono creati in modalità `strict`.

Comunque, c'è un caso in cui le prestazioni sui moderni motori JavaScript vengono drasticamente ridotte: quando viene usato `arguments.callee`.

    function foo() {
        arguments.callee; // fai qualcosa con questo oggetto funzione
        arguments.callee.caller; // e l'oggetto funzione chiamante
    }

    function bigLoop() {
        for (var i = 0; i < 100000; i++) {
            foo(); // verrebbe normalmente messo in linea...
        }
    }

In questo esempio, `foo` non può più essere soggetto a [inlining][1] dato che ha bisogno di conoscere sia se stesso sia il proprio chiamante. Questo non solo previene i possibili guadagni di prestazione che proverrebbero dalla messa in linea, ma vìola anche il principio di incapsulamento in quanto la funzione potrebbe ora dipendere da uno specifico contesto di invocazione.

Fare uso di `arguments.callee` o di una qualsiasi delle sue proprietà è **ampiamente sconsigliato**.

> **Nota su ES5:** In modalità `strict`, la presenza di `arguments.callee` genera un errore di tipo `TypeError` in quanto il suo uso è stato deprecato.

[1]: http://en.wikipedia.org/wiki/Inlining



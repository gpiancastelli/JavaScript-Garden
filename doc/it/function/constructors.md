## Costruttori

I costruttori in JavaScript sono ancora una volta differenti rispetto a molti altri linguaggi. Qualsiasi invocazione di funzione che viene preceduta dalla parola chiave `new` agisce come un costruttore.

All'interno del costruttore - la funzione invocata - il valore di `this` fa riferimento all'oggetto appena creato. Il [prototipo](#object.prototype) di questo **nuovo** oggetto viene impostato al prototipo dell'oggetto funzione che è stato invocato come costruttore.

Se la funzione che è stata invocata non ha una istruzione `return` esplicita, allora restituisce imnplicitamente il valore di `this` - il nuovo oggetto.

    function Foo() {
        this.bla = 1;
    }

    Foo.prototype.test = function() {
        console.log(this.bla);
    };

    var test = new Foo();

Questo esempio invoca `Foo` come costruttore e imposta la proprietà `prototype` dell'oggetto appena creato a `Foo.prototype`.

In caso di una istruzione `return` esplicita, la funzione restituisce il valore specificato da quella istruzione, ma **solo** se il valore di ritorno è di tipo `Object`.

    function Bar() {
        return 2;
    }
    new Bar(); // un nuovo oggetto

    function Test() {
        this.value = 2;

        return {
            foo: 1
        };
    }
    new Test(); // l'oggetto restituito

Quando la parola chiave `new` viene omessa, la funzione **non** restituisce un nuovo oggetto.

    function Foo() {
        this.bla = 1; // viene impostata sull'oggetto globale
    }
    Foo(); // undefined

Sebbene questo esempio possa apparire funzionante in certi casi, a causa del funzionamento di [`this`](#function.this) in JavaScript, userà l'*oggetto globale* come valore di `this`.

### Factory

Per poter omettere la parola chiave `new`, la funzione costruttore deve restituire esplicitamente un valore.

    function Bar() {
        var value = 1;
        return {
            method: function() {
                return value;
            }
        }
    }
    Bar.prototype = {
        foo: function() {}
    };

    new Bar();
    Bar();

Entrambe le invocazioni di `Bar` restituiscono la stessa cosa: un nuovo oggetto con una proprietà chiamata `method` che è una [chiusura](#function.closures).

Va sottolineato che l'istruzione `new Bar()` **non** modifica il prototipo dell'oggetto restituito. Sebbene il prototipo dell'oggetto creato venga modificato, la funzione `Bar` non restituisce mai quell'oggetto.

In questo esempio, quindi, non c'è alcuna differenza funzionale tra l'uso e l'omissione della parola chiave `new`.

### Creare nuovi oggetti attraverso le factory

Spesso si raccomanda di **non** usare `new` perché dimenticarla può condurre a errori.

Per creare un nuovo oggetto, si dovrebbe piuttosto usare una factory e costruire un nuovo oggetto all'interno di quella factory.

    function Foo() {
        var obj = {};
        obj.value = 'blub';

        var private = 2;
        obj.someMethod = function(value) {
            this.value = value;
        }

        obj.getPrivate = function() {
            return private;
        }
        return obj;
    }

Sebbene questo esempio sia robusto contro una parola chiave `new` mancante e certamente renda più semplice l'uso delle [variabili private](#function.closures), presenta anche alcuni aspetti negativi.

 1. Usa più memoria in quanto gli oggetti creati **non** condividono i metodi attraverso un prototipo
 2. Per ereditare, è necessario che la factory copi tutti i metodi da un altro oggetto oppure che imposti quell'oggetto come prototipo del nuovo oggetto.
 3. Abbandonare la catena di prototipi solo per una parola chiave `new` omessa è contrario allo spirito del linguaggio.

### Conclusione

Sebbene l'omissione della parola chiave `new` possa condurre a errori, di certo **non** è una ragione per abbandonare completamente l'uso dei prototipi. Si tratta di capire quale soluzione è più adatta per le esigenze dell'applicazione. È particolarmente importante scegliere uno stile specifico per la creazione degli oggetti e seguirlo in maniera **coerente**.
## Proprietà e utilizzo degli oggetti

Ogni entità in JavaScript si comporta come un oggetto, con le sole due eccezioni di [`null`](#core.undefined) e [`undefined`](#core.undefined).

    false.toString(); // 'false'
    [1, 2, 3].toString(); // '1,2,3'
    
    function Foo() {}
    Foo.bar = 1;
    Foo.bar; // 1

Per un comune malinteso, si considerano i letterali numero impossibili da utilizzare come oggetti, a causa di un difetto nel riconoscitore del linguaggio JavaScript che cerca di interpretare la *notazione a punto* su un numero come il punto decimale di un letterale in virgola mobile.

    2.toString(); // solleva un errore di tipo SyntaxError

Esistono un paio di soluzioni che consentono anche ai letterali numero di agire come oggetti.

    2..toString(); // il secondo punto viene riconosciuto correttamente
    2 .toString(); // si noti lo spazio a sinistra del punto
    (2).toString(); // 2 viene valutato per primo

### Oggetti come tipi di dato

In JavaScript gli oggetti possono anche essere usati come [*tabelle hash*][1], in quanto consistono principalmente di coppie in cui il nome di una proprietà è associato a un valore.

Usando un letterale oggetto - tramite la notazione `{}` - è possibile creare un nuovo oggetto che [eredita](#object.prototype) da `Object.prototype` ed è privo di [proprietà proprie](#object.hasownproperty)

    var foo = {}; // un nuovo oggetto vuoto

    // un nuovo oggetto con una proprietà 'test' con valore 12
    var bar = {test: 12};

### Accedere alle proprietà

È possibile accedere alle proprietà di un oggetto in due modi: tramite la notazione a punto oppure tramite la notazione a parentesi quadre.
    
    var foo = {name: 'kitten'}
    foo.name; // kitten
    foo['name']; // kitten
    
    var get = 'name';
    foo[get]; // kitten
    
    foo.1234; // SyntaxError
    foo['1234']; // funziona

Le due notazioni funzionano quasi allo stesso modo, e differiscono solo per il fatto che la notazione a parentesi quadre consente di impostare dinamicamente il nome della proprietà e permette di usare nomi di proprietà che altrimenti produrrebbero un errore sintattico.

### Eliminare proprietà

L'unico modo per rimuovere una proprietà da un oggetto è usare l'operatore `delete`. Impostare la proprietà a `undefined` o `null` rimuove solo il *valore* associato alla prroprietà, ma non la *chiave*.

    var obj = {
        bar: 1,
        foo: 2,
        baz: 3
    };
    obj.bar = undefined;
    obj.foo = null;
    delete obj.baz;

    for(var i in obj) {
        if (obj.hasOwnProperty(i)) {
            console.log(i, '' + obj[i]);
        }
    }

In questo esempio vengono stampati sia `bar undefined` che `foo null` - solo `baz` è stato rimosso e quindi non è presente nel testo stampato.

### Notazione delle chiavi

    var test = {
        'case': 'Sono una parola chiave: devo essere inserita in una stringa',
        delete: 'Sono una parola chiave: anche io!' // SyntaxError
    };

Le proprietà di un oggetto possono essere scritte come caratteri semplici e come stringhe. A causa di un ulteriore errore di progettazione nel riconoscitore del linguaggio JavaScript, la terza riga nell'esempio precedente solleverà un errore di tipo `SyntaxError` nelle versioni precedenti a ECMAScript 5.

Questo errore è provocato dal fatto che `delete` è una *parola chiave*, e quindi deve essere rappresentata come *letterale stringa* per assicurare una corretta interpretazione da parte dei motori JavaScript meno recenti.

[1]: http://it.wikipedia.org/wiki/Hash_table


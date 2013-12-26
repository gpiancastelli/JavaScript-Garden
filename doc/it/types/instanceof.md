## L'operatore `instanceof`

L'operatore `instanceof` confronta i costruttori dei suoi due operandi. È utile solamente quando si confrontano due oggetti personalizzati. Usato sui tipi predefiniti è quasi inutile quanto [l'operatore `typeof`](#types.typeof).

### Confrontare oggetti personalizzati

    function Foo() {}
    function Bar() {}
    Bar.prototype = new Foo();

    new Bar() instanceof Bar; // true
    new Bar() instanceof Foo; // true

    // La riga seguente imposta il valore di Bar.prototype all'oggetto
    // funzione Foo, non a una istanza di Foo
    Bar.prototype = Foo;
    new Bar() instanceof Foo; // false

### Usare `instanceof` con i tipi predefiniti

    new String('foo') instanceof String; // true
    new String('foo') instanceof Object; // true

    'foo' instanceof String; // false
    'foo' instanceof Object; // false

È importante notare che `instanceof` non funziona su oggetti che sono stati creati in contesti differenti (e.g. documenti diversi in un browser web) dato che il loro costruttore non sarà lo stesso identico oggetto.

### Conclusione

L'operatore `instanceof` dovrebbe essere usato **solamente** su oggetti personalizzati che provengono dallo stesso contesto JavaScript. Proprio come l'operatore [`typeof`](#types.typeof), qualsiasi altro uso dovrebbe essere **evitato**.

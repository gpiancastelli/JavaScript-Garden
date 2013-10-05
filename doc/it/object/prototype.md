## Il prototipo

Il modello di ereditarietà di JavaScript non è basato su classi, bensì su *prototipi*.

Sebbene questo sia spesso considerato uno dei punti deboli del linguaggio, il modello di ereditarietà basato su prototipi è in effetti più potente del modello basato su classi. Per esempio, è abbastanza banale costruire un modello basato su classi a partire da un modello basato su prototipi, mentre il viceversa è un compito ben più difficile.

JavaScript è l'unico linguaggio in largo uso caratterizzato da una ereditarietà prototipica, quindi può volerci tempo per adattarsi alle differenze tra i due modelli.

La prima grande differenza è che l'ereditarietà in JavaScript usa *catene di prototipi*.

> **Nota:** Usare semplicemente `Bar.prototype = Foo.prototype` avrà come risultato la condivisione dello **stesso** prototipo. Perciò, le modifiche al prototipo di uno dei due oggetti avranno effetto anche sul prototipo dell'altro oggetto, un effetto solitamente indesiderato.

    function Foo() {
        this.value = 42;
    }
    Foo.prototype = {
        method: function() {}
    };

    function Bar() {}

    // Impostiamo il prototipo di Bar a una nuova istanza di Foo
    Bar.prototype = new Foo();
    Bar.prototype.foo = 'Hello World';

    // Assicuriamoci di indicare Bar come l'effettivo costruttore
    Bar.prototype.constructor = Bar;

    var test = new Bar(); // creiamo una nuova istanza di Bar

    // Ecco la risultante catena di prototipi
    test [instance of Bar]
        Bar.prototype [instance of Foo]
            { foo: 'Hello World' }
            Foo.prototype
                { method: ... }
                Object.prototype
                    { toString: ... /* ecc. */ }

In questo esempio, l'oggetto `test` erediterà sia da `Bar.prototype` che da `Foo.prototype`; quindi, avrà accesso alla funzione `method` che è stata definita in `Foo`. Avrà anche accesso alla prorpietà `value` della **istanza** di `Foo` che ne svolge il ruolo di prototipo. È importante notare che `new Bar()` **non** crea una nuova istanza di `Foo`, ma riutilizza quella assegnata al suo prototipo; quindi, tutte le istanze di `Bar` condivideranno la **stessa** proprietà `value`.

> **Nota:** L'uso di `Bar.prototype = Foo` va **evitato**, in quanto non punta al prototipo di `Foo` ma piuttosto all'oggetto funzione `Foo`. Quindi la catena di prototipi si aggancerà a `Function.prototype` anziché `Foo.prototype`, e la funzione `method` non verrà inclusa.

### Ricerca di proprietà

Quando si accede alle proprietà di un oggetto, JavaScript attraverserà la catena di prototipi **verso l'alto** fino a quando non trova una proprietà con il nome richiesto.

Se raggiunge la cima della catena - cioè `Object.prototype` - senza trovare la proprietà specificata, restituirà il valore [undefined](#core.undefined).

### La prorpietà `prototype`

Sebbene la proprietà `prototype` venga usata dal linguaggio per costruire la catena di prototipi, è comunque possibile assegnarle un valore **qualsiasi**. Se questo valore è un oggetto di tipo primitivo, verrà semplicemente ignorato.

    function Foo() {}
    Foo.prototype = 1; // nessun effetto

Come mostrato nell'esempio precedente, l'assegnamento di un oggetto funzionerà in modo da consentire la creazione dinamica di catene di prototipi.

### Prestazioni

Il tempo impiegato per la ricerca di una proprietà che si trova in posizioni elevate nella catena di prototipi può avere un impatto negativo sulle prestazioni, che potrebbe essere significativo quando le prestazioni sono critiche. Inoltre, il tentativo di accedere a proprietà inesistenti provocherà sempre l'attraversamento dell'intera catena di prototipi.

Infine, quando si [itera](#object.forinloop) sulle proprietà di un oggetto, **tutte** le proprietà che si trovano sulla catena di prototipi verranno enumerate.

### Estendere i prototipi predefiniti

La possibilità di estendere `Object.prototype` è una perniciosa caratteristica del linguaggio che però viene spesso sfruttata nella pratica.

Questa tecnica è detta [monkey patching][1] e viola il principio di *incapsulamento*. Sebbene sia usata da popolari librerie come [Prototype][2], non c'è alcun buon motivo per stipare i tipi predefiniti di funzionalità aggiuntive **non standard**.

Esiste una **sola** buona ragione per estendere un prototipo predefinito: portare le nuove caratteristiche del linguaggio, per esempio [`Array.forEach`][3], sui motori JavaScript meno recenti.

### Conclusione

La comprensione del modello di ereditarietà basato su prototipi è essenziale per scrivere codice complesso che ne fa uso. La lunghezza delle catene di prototipi nel codice va sempre tenuta sotto controllo, ed eventualmente ridotta spezzando catene troppo lunghe che potrebbero provocare problemi di prestazioni. Inoltre, i prototipi predefiniti non dovrebbero **mai** essere estesi a meno che questo avvenga per garantire la compatibilità con nuove caratteristiche del linguaggio.

[1]: http://en.wikipedia.org/wiki/Monkey_patch
[2]: http://prototypejs.org/
[3]: https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Array/forEach


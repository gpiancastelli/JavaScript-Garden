## Come funziona `this`

JavaScript associa al particolare nome `this` un significato diverso rispetto alla maggior parte degli altri linguaggi di programmazoine. Ci sono esattamente **cinque** modi differenti in cui il valore di `this` viene stabilito.

### L'ambito globale

    this;

Quando si usa `this` nell'ambito globale, esso farà riferimento all'oggetto *globale*.

### Invocazione di una funzione

    foo();

Anche in questo caso `this` farà riferimento all'oggetto *globale*.

> **Nota su ES5:** In modalità strict, il caso globale **non** esiste più: in quel caso, a `this` viene assegnato il valore `undefined`.

### Invocazione di un metodo

    test.foo(); 

In questo esempio, `this` farà riferimento a `test`.

### Invocazione di un costruttore

    new foo(); 

Una invocazione d funzione preceduta dalla parola chiave `new` si comporta come un [costruttore](#function.constructors). All'interno della funzione, `this` farà riferimento all'istanza di `Object` appena creata.

### Impostazione esplicita del valore di `this`

    function foo(a, b, c) {}
                          
    var bar = {};
    foo.apply(bar, [1, 2, 3]); // l'array viene esploso come qui sotto
    foo.call(bar, 1, 2, 3); // darà come risultato a = 1, b = 2, c = 3

Quando si usano i metodi `call` e `apply` di `Function.prototype`, il valore di `this` all'interno delle funzioni invocate viene **esplicitamente impostato** al primo argomento della corrispondente invocazione di funzione.

In questo caso, le regole della *invocazione di un metodo* **non** si applicano, e `this` all'interno di `foo` avrà il valore `bar`.

> **Nota:** `this` **non può** essere usato per fare riferimento all'interno di un letterale `Object` per fare riferimento all'oggetto stesso. Quindi `var obj = {me: this}` **non** assegnerà un riferimento a `obj` a `me`, dato che il valore di `this` viene stabilito solo sulla base dei cinque casi elencati.

### Errori comuni

Nonostante la maggior parte dei casi elencati abbia senso, il primo può essere considerato l'ennesimo errore di progettazione del linguaggio, in quanto non ha **mai** alcun uso pratico.

    Foo.method = function() {
        function test() {
            // this fa riferimento all'oggetto globale
        }
        test();
    }

Per un comune malinteso, si ritiene che `this` all'interno di `test` faccia riferimento a `Foo`, quando invece **non è così**.

Per poter accedere a `Foo` dall'interno di `test`, è necessario creare una variabile locale che faccia riferimento a `Foo` all'interno del metodo.

    Foo.method = function() {
        var that = this;
        function test() {
            // per fare riferimento a Foo si usi that anziché this
        }
        test();
    }

`that` è un normale nome di variabile, ma viene comunemente usato per fare riferimento a un `this` esterno. In combinazione con le [chiusure](#function.closures), può anche essere usato per passare in giro il valore di `this`.

### Assegnamento di metodi

Un'altra cosa che **non** funziona in JavaScript è il cosiddetto aliasing delle funzioni, che consiste in un **assegnamento** di un metodo a una variabile.

    var test = someObject.methodTest;
    test();

Come previsto dal primo caso, `test` ora agisce come una normale invocazione di funzione; perciò `this` al suo interno non farà più riferimento a `someObject`.

Sebbene il legame dinamico di `this` possa sembrare una cattiva idea all'inizio, in effetti è quello che permette alla [ereditarietà basata su prototipi](#object.prototype) di funzionare.

    function Foo() {}
    Foo.prototype.method = function() {};

    function Bar() {}
    Bar.prototype = Foo.prototype;

    new Bar().method();

Quando `method` viene invocato su una istanza di `Bar`, `this` farà riferimento proprio a quella istanza.



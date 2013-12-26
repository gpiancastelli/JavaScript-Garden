## L'operatore `typeof`

L'operatore `typeof` è quasi **completamente inutilizzabile**, e in quanto tale (insieme all'operatore [`instanceof`](#types.instanceof)) costituisce probabilmente il più grande errore di progettazione del linguaggio JavaScript.

Sebbene `instanceof` abbia ancora usi limitati, `typeof` ha realmente un solo caso d'uso pratico, che capita **non** abbia nulla a che fare con il controllo del tipo di un oggetto.

> **Nota:** Nonostante `typeof` possa anche essere utilizzato con una sintassi simile a quella della invocazione di funzioni, l'espressione `typeof(obj)` non invoca alcuna funzione. Le parentesi assumono il loro usuale comportamento e il valore che restituiscono verrà usato come operando dell'operatore `typeof`. **Non** esiste alcuna funzione `typeof`.

### La tabella dei tipi di JavaScript

    Value               Class      Type
    -------------------------------------
    "foo"               String     string
    new String("foo")   String     object
    1.2                 Number     number
    new Number(1.2)     Number     object
    true                Boolean    boolean
    new Boolean(true)   Boolean    object
    new Date()          Date       object
    new Error()         Error      object
    [1,2,3]             Array      object
    new Array(1, 2, 3)  Array      object
    new Function("")    Function   function
    /abc/g              RegExp     object (function in Nitro/V8)
    new RegExp("meow")  RegExp     object (function in Nitro/V8)
    {}                  Object     object
    new Object()        Object     object

Nella tabella precedente, la colonna *Type* si riferisce al valore restituito dall'operatore `typeof`. Come si può chiaramente vedere, tutto si può dire di questi valori tranne che siano coerenti.

La colonna *Class* si riferisce al valore della proprietà interna `[[Class]]` di un oggetto.

> **Dalla specifica del linguaggio:** Il valore di `[[Class]]` può essere una delle stringhe seguenti: `Arguments`, `Array`, `Boolean`, `Date`, `Error`, `Function`, `JSON`, `Math`, `Number`, `Object`, `RegExp`, `String`.

Per ottenre il valore della proprietà `[[Class]]`, è necessario usare il metodo `toString` di `Object.prototype`.

### La classe di un oggetto

La specifica del linguaggio fornisce esattamente un modo per accedere al valore della proprietà `[[Class]]`, tramite l'uso di `Object.prototype.toString`.

    function is(type, obj) {
        var clas = Object.prototype.toString.call(obj).slice(8, -1);
        return obj !== undefined && obj !== null && clas === type;
    }
    
    is('String', 'test'); // true
    is('String', new String('test')); // true

In questo esempio, `Object.prototype.toString` viene invocata con il valore di [this](#function.this) impostato all'oggetto della cui proprietà `[[Class]]` si vuole ottenere il valore.

> **Nota su ES5:** Per comodità, il valore di ritorno di `Object.prototype.toString`  per `null` e `undefined` è stato **cambiato** da `Object` a `Null` e `Undefined` rispettivamente in ECMAScript 5.

### Controllo per le variabili non definite

    typeof foo !== 'undefined'

Questa espressione controlla se la varaibile `foo` è stata effettivamente dichiarata oppure no; il solo atto di farvi riferimento risulterebbe in un errore di tipo `ReferenceError`. Questo è l'unico caso in cui `typeof` ha effettivamente una sua utilità.

### Conclusione

Per controllare il tipo di un oggetto si raccomanda caldamente di usare `Object.prototype.toString` perché è l'unico modo affidabile di farlo. Come mostrato nella tabella dei tipi più sopra, alcuni valori restituiti da `typeof` non sono definiti nella specifica del linguaggio, e quindi potrebbero differire a seconda delle implementazioni. A meno di non controllare se una variabile è definita, l'operatore `typeof` dovrebbe essere evitato.

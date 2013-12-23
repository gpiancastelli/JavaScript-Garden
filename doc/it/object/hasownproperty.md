## `hasOwnProperty`

Per controllare se un oggetto possiede una proprietà definita su **sé stesso** e non da qualche parte nella propria [catena di prototipi](#object.prototype), è necessario usare il metodo `hasOwnProperty` che tutti gli oggetti ereditano da `Object.prototype`.

> **Nota:** Controllare se una proprietà vale `undefined` **non** è sufficiente. La proprietà potrebbe esistere, ma avere semplicemente il valore impostato a `undefined`.

`hasOwnProperty` è l'unico meccanismo di JavaScript che lavora con le proprietà e **non** attraversa la catena di prototipi.

    // inquiniamo Object.prototype
    Object.prototype.bar = 1;
    var foo = {goo: undefined};

    foo.bar; // 1
    'bar' in foo; // true

    foo.hasOwnProperty('bar'); // false
    foo.hasOwnProperty('goo'); // true

Solo `hasOwnProperty` darà i risultati attesi e corretti; questo è essenziale quando si itera attraverso le proprietà di qualsiasi oggetto. Non esiste **nessun** altro modo di escludere proprietà che non sono definite sull'oggetto stesso, ma da qualche parte nella sua catena di prototipi.

### `hasOwnProperty` come proprietà

JavaScript non offre alcuna protezione al nome di proprietà `hasOwnProperty`; quindi, nel caso in cui un oggetto possa avere una proprietà con quel nome, è necessario usare una proprietà `hasOwnProperty` *esterna* per ottenere i risultati corretti.

    var foo = {
        hasOwnProperty: function() {
            return false;
        },
        bar: 'Here be dragons'
    };

    foo.hasOwnProperty('bar'); // restituisce sempre false

    // È necessario usare la proprietà hasOwnProperty di un
    // altro oggetto e invocarla con this impostato a foo
    ({}).hasOwnProperty.call(foo, 'bar'); // true

    // A questo scopo è anche possibile usare la proprietà
    // hasOwnProperty del prototipo di Object
    Object.prototype.hasOwnProperty.call(foo, 'bar'); // true


### Conclusione

`hasOwnProperty` è il **solo** metodo affidabile per verificare l'esistenza di una proprietà definita in un oggetto. Se ne raccomanda l'uso in **tutti** i [cicli `for in`](#object.forinloop) per evitare errori dovuti alla estensione di [prototypes](#object.prototype) predefiniti.
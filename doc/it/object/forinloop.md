## Il ciclo `for in`

Similmente all'operatore `in`, il ciclo `for in` attraversa la catena di prototipi quando itera sulle proprietà di un oggetto.

> **Nota:** Il ciclo `for in` **non** includerà proprietà il cui attributo `enumerable` è impostato a `false`, come per esempio la proprietà `length` di un array.
    
    // inquiniamo Object.prototype
    Object.prototype.bar = 1;

    var foo = {moo: 2};
    for (var i in foo) {
        console.log(i); // stampa sia bar che moo
    }

Non essendo possibile modificare il comportamento del ciclo `for in`, è necessario filtrare le proprietà indesiderate all'interno del corpo del ciclo; questo si può fare usando il metodo [`hasOwnProperty`](#object.hasownproperty) di `Object.prototype`.

> **Nota:** Dato che il ciclo `for in` attraversa sempre l'intera catena di prototipi, risulterà più lento quanti più livelli addizionali di ereditarietà vengono aggiunti a un oggetto.

### Usare `hasOwnProperty` come filtro

    // foo è la variabile definita più sopra
    for (var i in foo) {
        if (foo.hasOwnProperty(i)) {
            console.log(i);
        }
    }

Questa è l'unica versione corretta. A causa dell'utilizzo di `hasOwnProperty`, stamperà **solo** `moo`. Quando `hasOwnProperty` viene omesso, il codice diventa soggetto a errori nei casi in cui i prototipi predefiniti - per esempio `Object.prototype` - sono stati estesi.

Una libreria largamente diffusa che estende `Object.prototype` è [Prototype][1]. Quando questa libreria viene inclusa, i cicli `for in` che non usano `hasOwnProperty` hanno la garanzia di produrre comportamenti inattesi o erronei.

### Conclusione

Si raccomanda di utilizzare **sempre** `hasOwnProperty`. Nessuna assunzione dovrebbe essere fatta a proposito dell'ambiente in cui il codice viene eseguito, o sulla possibilità che i prototipi predefiniti siano stati estesi o meno.

[1]: http://www.prototypejs.org/


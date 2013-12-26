## Array: iterazione e proprietà

Sebbene gli array in JavaScript siano oggetti, non ci sono buone ragioni per usare il ciclo [`for in`](#object.forinloop). Infatti, ci sono alcune ragioni contro l'utilizzo di `for in` sugli array.

> **Nota:** Gli array in JavaScript **non** sono *array associativi*. JavaScript usa solamente gli [oggetti](#object.general) per stabilire corrispondenze tra chiavi e valori. E mentre gli array associativi **preservano** l'ordine, gli oggetti **non** lo fanno.

Dato che ciclo `for in` enumera tutte le proprietà che si trovano sulla catena di prototipi, e dato che l'unico modo per escludere quelle proprietà è di utilizzare [`hasOwnProperty`](#object.hasownproperty), è già fino a **venti volte** più lento di un normale ciclo `for`.

### Iterazione

Allo scopo di ottenere le prestazioni migliori quando si itera sugli array, è meglio usare il classico ciclo `for`.

    var list = [1, 2, 3, 4, 5, ...... 100000000];
    for (var i = 0, l = list.length; i < l; i++) {
        console.log(list[i]);
    }

C'è un trabocchetto aggiuntivo nell'esempio qui sopra, che è la memorizzazione della lunghezza dell'array tramite `l = list.length`.

Nonostante la proprietà `length` sia definita sull'array stesso, c'è comunque un costo aggiunto dovuto alla esecuzione della ricerca per ogni iterazione del ciclo. E sebbene gli interpreti JavaScript più recenti **possano** applicare ottimizzazioni in questo caso, non c'è modo di sapere se il codice verrà eseguito su uno di questi interpreti più nuovi oppure no.

In effetti, omettere la memorizzazione può risultare in un ciclo più lento del cinquanta per cento rispetto a un ciclo con la memorizzazione della lunghezza.

### La proprietà `length`

Mentre l'operazione di recupero della proprietà `length` restituisce semplicemente il numero di elemento che sono contenuti nell'array, l'operazione di impostazione del valore della proprietà può essere usata per **troncare** l'array.

    var foo = [1, 2, 3, 4, 5, 6];
    foo.length = 3;
    foo; // [1, 2, 3]

    foo.length = 6;
    foo.push(4);
    foo; // [1, 2, 3, undefined, undefined, undefined, 4]

L'assegnamento di una lunghezza più piccola tronca l'array. L'assegnamento di una lunghezza più grande crea un array sparso.

### Conclusione

Per ottenere le prestazioni migliori si raccomanda di usare sempre il semplice ciclo `for` e di memorizzare la proprietà `length`. L'uso di `for in` su un array è segno di codice scritto male che può essere incline a errori e cattive prestazioni.
## Perché evitare `eval`

La funzione `eval` eseguirà una stringa di codice JavaScript nell'ambito locale.

    var foo = 1;
    function test() {
        var foo = 2;
        eval('foo = 3');
        return foo;
    }
    test(); // 3
    foo; // 1

Tuttavia, `eval` esegue il codice nell'ambito locale solo quando viene invocato direttamente *e* quando il nome della funzione invocata è effettivamente `eval`.

    var foo = 1;
    function test() {
        var foo = 2;
        var bar = eval;
        bar('foo = 3');
        return foo;
    }
    test(); // 2
    foo; // 3

L'uso di `eval` dovrebbe essere evitato. Il 99,9% dei suoi "usi" può essere realizzato **senza** di esso.
    
### `eval` sotto mentite spoglie

Le [funzioni di timeout](#other.timeouts) `setTimeout` and `setInterval` possono accettare entrambe una stringa come primo argomento. Questa stringa verrà **sempre** eseguita nell'ambito globale dato che in questo caso `eval` non viene invocato direttamente.

### Problemi di sicurezza

L'uso di `eval` pone anche un problema di sicurezza, poiché esegue **qualsiasi** codice gli venga passato. Non dovrebbe **mai** essere usato con stringhe di origine ignota o inaffidabile.

### Conclusione

La funzione `eval` non dovrebbe mai essere usata. Qualsiasi programma che ne faccia uso dovrebbe essere esaminato sotto diversi aspetti, dal funzionamento alle prestazioni e alla sicurezza. Se qualcosa richiede `eval` per funzionare, **non** dovrebbe neanche essere usata: una *progettazione migliore* che non richiede l'uso di `eval` dovrebbe essere impiegata.

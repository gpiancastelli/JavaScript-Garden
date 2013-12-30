## Inserimento automatico dei punti e virgola

Sebbene JavaScript possegga una sintassi nello stile del linguaggio C, **non** impone l'utilizzo dei punti e virgola nel codice sorgente, quindi è possibile ometterli.

JavaScript non è un linguaggio privo di punti e virgola. Infatti, ha bisogno dei punti e virgola allo scopo di capire il codice sorgente. Quindi, il riconoscitore del linguaggio JavaScript li inserisce **automaticamente** ogni volta che incontra un errore di riconoscimento dovuto a un punto e virgola mancante.

    var foo = function() {
    } // errore di riconoscimento, previsto un punto e virgola
    test()

L'inserimento avviene, e il riconoscitore riprova.

    var foo = function() {
    }; // nessun errore, il riconoscitore prosegue
    test()

L'inserimento automatico dei punti e virgola viene considerato come uno dei **più grandi** difetti di progettazione del linguaggio perché *può* cambiare il comportamento del codice

### Come funziona

Il codice seguente non ha punti e virgola, quindi tocca al riconoscitore decidere dove inserirli.

    (function(window, undefined) {
        function test(options) {
            log('collaudo!')

            (options.list || []).forEach(function(i) {

            })

            options.value.test(
                'ecco una lunga stringa da passare',
                'e una seconda lunga stringa da passare'
            )

            return
            {
                foo: function() {}
            }
        }
        window.test = test

    })(window)

    (function(window) {
        window.someLibrary = {}

    })(window)

Di seguito viene riportato il risultato delle congetture del riconoscitore.

    (function(window, undefined) {
        function test(options) {

            // nessun punto e virgola inserito,
            // le righe sono state unite
            log('collaudo!')(options.list || []).forEach(function(i) {

            }); // <- inserito

            options.value.test(
                'ecco una lunga stringa da passare',
                'e una seconda lunga stringa da passare'
            ); // <- inserito

            return; // <- inserito, spezza l'istruzione di ritorno
            { // trattato come un blocco

                // una etichetta e una istruzione composta
                // da una singola espressione
                foo: function() {} 
            }; // <- inserito
        }
        window.test = test; // <- inserito

    // le righe sono state unite di nuovo
    })(window)(function(window) {
        window.someLibrary = {}; // <- inserito

    })(window); //<- inserito

> **Nota:** Il riconoscitore del linguaggio JavaScript non gestisce "correttamente" le istruzioni `return` che sono seguite da un carattere di nuova riga. Nonostante questo non sia necessariamente colpa dell'inserimento automatico dei punti e virgola, può comunque essere un effetto collaterale indesiderato.

Il riconoscitore ha modificato drasticamente il comportamento del codice. In alcuni casi, fa la **cosa sbagliata**.

### Parentesi iniziali

Nei casi di una parentesi iniziale, il riconoscitore **non** inserirà un punto e virgola.

    log('collaudo!')
    (options.list || []).forEach(function(i) {})

Questo codice viene trasformato in una sola riga.

    log('collaudo!')(options.list || []).forEach(function(i) {})

Le probabilità che `log` **non** restituisca una funzione sono **molto** alte; quindi, la riga precedente produrrà un errore di tipo `TypeError` lamentandosi del fatto che `undefined` non è una funzione.

### Conclusione

Si raccomanda caldamente di non omettere **mai** i punti e virgola. Si raccomanda caldamente anche di mantenere le parentesi sulla stessa riga delle istruzioni corrispondenti e di non ometterle mai per le istruzioni `if`/`else` i cui blocchi contengano una sola riga. Questi provvedimenti non solo miglioreranno la coerenza del codice, ma evitaranno che il riconoscitore del linguaggio modifichi il comportamento del codice.

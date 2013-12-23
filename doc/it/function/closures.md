## Chiusure e riferimenti

Una delle caratteristiche più potenti di JavaScript è la disponibilità delle *chiusure*. Con le chiusure, gli ambiti mantengono **sempre** l'accesso all'ambito più esterno nel quale sono stati definiti. Dato che gli unici ambiti in JavaScript sono gli [ambiti di funzione](#function.scopes), tutte le funzioni si comportano come chiusure.

### Emulare le variabili private

    function Counter(start) {
        var count = start;
        return {
            increment: function() {
                count++;
            },
            get: function() {
                return count;
            }
        }
    }

    var foo = Counter(4);
    foo.increment();
    foo.get(); // 5

In questo esempio `Counter` restituisce **due** chiusure: la funzione `increment` e la funzione `get`. Entrambe le funzioni mantengono un **riferimento** all'ambito di `Counter`, quindi possono sempre accedere alla variabile `count` che è stata definita in quell'ambito.

### Perché le variabili private funzionano

Dato che in JavaScript non è possibile fare riferimento a un ambito o assegnarlo a una variabile, **non** esiste alcun modo per accedere alla variabile `count` dall'esterno. L'unico modo per interagire con essa è attraverso le due chiusure.

    var foo = new Counter(4);
    foo.hack = function() {
        count = 1337;
    };

Questo codice **non** modificherà la variabile `count` nell'ambito di `Counter`, dato che `foo.hack` non è stato definito in **quel** particolare ambito. Invece, provocherà la creazione - o la ridefinizione - della variabile *globale* `count`.

### Chiusure all'interno di cicli

Spesso si commette l'errore di usare le chiusure all'interno di cicli come se esse si copiassero il valore della variabile usata come indice nel ciclo.

    for (var i = 0; i < 10; i++) {
        setTimeout(function() {
            console.log(i);  
        }, 1000);
    }

Questo esempio **non** mostrerà i numeri da `0` a `9`, ma stamperà semplicemente il numero `10` per dieci volte.

La funzione *anonima* mantiene un **riferimento** a `i`. Nel momento in cui `console.log` viene invocato, il ciclo `for` si è già concluso, e il valore di `i` è stato impostato a `10`.

Per ottenere il comportamento desiderato è necessario creare una **copia** del valore di `i`.

### Come evitare il problema del riferimento

Per copiare il valore della variabile usata come indice del ciclo, la soluzione migliore consiste nell'usare una [funzione anonima](#function.scopes) come involucro.

    for (var i = 0; i < 10; i++) {
        (function(e) {
            setTimeout(function() {
                console.log(e);  
            }, 1000);
        })(i);
    }

La funzione anonima più esterna viene invocata immediatamente passando `i` come primo argomento e riceve una copia del **valore** di `i` nel suo parametro `e`.

La funzione anonima che viene passata a `setTimeout` ora contiene un riferimento a `e`, il cui valore **non** viene modificato dal ciclo.

Esiste un altro modo per poter ottenere questo effetto, che consiste nel restituire una funzione dall'involucro anonimo che avrà lo stesso comportamento del codice precedente.

    for (var i = 0; i < 10; i++) {
        setTimeout((function(e) {
            return function() {
                console.log(e);
            }
        })(i), 1000)
    }

Ed esiste un altro modo ancora per poter ottenere lo stesso effetto, che consiste nell'invocare il metodo `bind` per legare un contesto di esecuzione `this` e una serie di argomenti a una funzione. Il comportamento è identico a quello del codice precedente.

    for (var i = 0; i < 10; i++) {
        setTimeout(console.log.bind(console, i), 1000);
    }

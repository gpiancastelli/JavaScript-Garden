## Conversioni di tipo

JavaScript è un linguaggio *debolmente tipato*, quindi applicherà una *conversione forzata di tipo* **ovunque** sia possibile.

    // Queste uguaglianze restituiscono true
    new Number(10) == 10; // Number.toString() viene convertito
                          // di nuovo in numero

    10 == '10';           // Le stringhe vengono convertite in numeri
    10 == '+10 ';         // Altra follia delle stringhe
    10 == '010';          // E ancora un'altra
    isNaN(null) == false; // null viene convertito in 0
                          // che naturalmente non è NaN
    
    // Queste uguaglianze restituiscono false
    10 == 010;
    10 == '-10';

> **Nota su ES5:** I letterali numero che cominciano con `0` vengono interpretati come numeri ottali (in base 8). Il supporto per questi numeri è stato **rimosso* dalla modalità strict di ECMAScript 5

Per evitare i problemi illustrati, si raccomanda **caldamente** di usare l'[operatore di uguaglianza stretta](#types.equality) . Sebbene questo consenta di evitare un buon numero di trabocchetti comuni, ci sono molti altri problemi dovuti al sistema di tipi deboli di JavaScript.

### Costruttori di tipi predefiniti

I costruttori di tipi predefiniti come `Number` e `String` si comportano in maniera differente a seconda che vengano usati o meno con la parola chiave `new`.

    new Number(10) === 10;     // false, Object e Number
    Number(10) === 10;         // true, Number e Number
    new Number(10) + 0 === 10; // true, a causa della conversione implicita

Usare un tipo predefinito come `Number` nel ruolo di costruttore porta alla creazione di un nuovo oggetto `Number`, ma quando si omette la parola chiave `new` la funzione `Number` si comporta come un convertitore.

In più, passare letterali o valori che non siano oggetti risulterà in altre conversioni forzate di tipo.

L'opzione migliore consiste nell'effettuare la conversione a uno di tre possibili tipi **in modo esplicito**.

### Conversione in stringa

    '' + 10 === '10'; // true

Aggiungendo in testa una stringa vuota, un valore può facilmente essere convertito in stringa.

### Conversione in numero

    +'10' === 10; // true

Usando l'operatore unario `+` è possibile convertire in un numero.

### Conversione in boolean

Usando l'operatore **not** due volte consecutive è possibile convertire un valore in boolean

    !!'foo';   // true
    !!'';      // false
    !!'0';     // true
    !!'1';     // true
    !!'-1'     // true
    !!{};      // true
    !!true;    // true



## Uguaglianza e confronti

JavaScript utilizza due modalità differenti per confrontare i valori degli oggetti e verificarne l'uguaglianza. 

### L'operatore di uguaglianza

L'operatore di uguaglianza consiste in due segni di uguale: `==`

JavaScript utilizza una *tipizzazione debole*. Questo significa che l'operatore di uguaglianza **converte** forzatamente i tipi allo scopo di poterli confrontare.
    
    ""           ==   "0"           // false
    0            ==   ""            // true
    0            ==   "0"           // true
    false        ==   "false"       // false
    false        ==   "0"           // true
    false        ==   undefined     // false
    false        ==   null          // false
    null         ==   undefined     // true
    " \t\r\n"    ==   0             // true

La tabella precedente mostra i risultati della conversione di tipo, ed è la ragione principale per cui l'uso di `==` viene largamente considerato una cattiva pratica. Introduce difetti che sono difficili da rintracciare a causa delle sue complicate regole di conversione.

In più la conversione di tipo ha un impatto anche sulle prestazioni; per esempio, una stringa deve essere convertita in numero prima di poter essere confrontata con un altro numero.

### L'operatore di uguaglianza stretta

L'operatore di uguaglianza stretta consiste in **tre** segni di uguale: `===`.

Funziona come il normale operatore di uguaglianza, a parte il fatto che l'operatore di uguaglianza stretta **non** effettua alcuna conversione forzata di tipo tra i suoi operandi.

    ""           ===   "0"           // false
    0            ===   ""            // false
    0            ===   "0"           // false
    false        ===   "false"       // false
    false        ===   "0"           // false
    false        ===   undefined     // false
    false        ===   null          // false
    null         ===   undefined     // false
    " \t\r\n"    ===   0             // false

I risultati in questa tabella sono molto più chiari e consentono di individuare immediatamente eventuali errori nella scrittura di codice. Questo rende il codice più solido in una certa misura, e offre miglioramenti nelle prestazioni nel caso in cui gli operandi siano di tipo diverso.

### Confrontare oggetti

Sebbene entrambi gli operatori `==` e `===` siano detti di **uguaglianza**, si comportano in modo differente quando almeno uno dei loro operandi è una istanza di `Object`.

    {} === {};                   // false
    new String('foo') === 'foo'; // false
    new Number(10) === 10;       // false
    var foo = {};
    foo === foo;                 // true

In questi casi, entrambi gli operatori lavorano sull'**identità** e **non** sull'uguaglianza; cioè, verificano che gli oggetti siano la stessa **istanza**, in modo molto simile all'operatore `is` in Python e al confronto tra puntatori in C.

### Conclusione

Si suggerisce caldamente di usare solamente l'operatore di **uguaglianza stretta**. La conversione di tipo, nei casi in cui è necessaria, dovrebbe essere effettuata [in maniera esplicita](#types.casting) anziché essere lasciata alle complicate regole di conversione del linguaggio.


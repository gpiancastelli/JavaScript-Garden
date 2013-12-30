## L'operatore `delete`

In breve, è *impossibile* eliminare variabili globali, funzioni, e tutto quello che in JavaScript ha un attributo `DontDelete` definito.

### Codice globale e codice di funzione

Quando una variabile o una funzione viene definita nell'ambito globale o in un [ambito di funzione](#function.scopes) diventa una proprietà dell'oggetto globale o dell'oggetto Activation. Tali proprietà possiedono un insieme di attributi, uno dei quali si chiama `DontDelete`. Dichiarazioni di variabili e di funzioni nell'ambito globale o in un ambito di funzione creano sempre proprietà con un attributo `DontDelete`, e quindi non possono essere cancellate.

it is a property of either the Activation object or
the Global object. Such properties have a set of attributes, one of which is
`DontDelete`. Variable and function declarations in global and function code
always create properties with `DontDelete`, and therefore cannot be deleted.

    // variabile globale:
    var a = 1; // DontDelete è definito
    delete a; // false
    a; // 1

    // funzione normale:
    function f() {} // DontDelete è definito
    delete f; // false
    typeof f; // "function"

    // il riassegnamento non aiuta:
    f = 1;
    delete f; // false
    f; // 1

### Proprietà esplicite

Proprietà impostate esplicitamente possono essere cancellate normalmente.

    // proprietà esplicitamente impostata:
    var obj = {x: 1};
    obj.y = 2;
    delete obj.x; // true
    delete obj.y; // true
    obj.x; // undefined
    obj.y; // undefined

In questo esempio `obj.x` e `obj.y` possono essere cancellate perché non possiedono alcun attributo `DontDelete`. Questo è anche il motivo per cui il prossimo esempio funziona.

    // questo funziona bene, tranne che su IE:
    var GLOBAL_OBJECT = this;
    GLOBAL_OBJECT.a = 1;
    a === GLOBAL_OBJECT.a; // true - semplicemente una variabile globale
    delete GLOBAL_OBJECT.a; // true
    GLOBAL_OBJECT.a; // undefined

Qui usiamo un trucco per cancellare `a`. [`this`](#function.this) qui si riferisce all'oggetto globale, e noi dichiariamo esplicitamente una variabile `a` come sua proprietà, cosa che ci consente di cancellarla.

IE (almeno nelle versioni tra la 6 e la 8) contiene alcuni difetti che impediscono a questo codice di funzionare correttamente.

### Argomenti di funzione e proprietà predefinite

Anche i normali argomenti di funzione, l'[oggetto `arguments`](#function.arguments) e le proprietà predefinite possiedono un attributo `DontDelete`.

    // argomenti di funzione e proprietà:
    (function (x) {
    
      delete arguments; // false
      typeof arguments; // "object"
      
      delete x; // false
      x; // 1
      
      function f(){}
      delete f.length; // false
      typeof f.length; // "number"
      
    })(1);

### Oggetti dell'ambiente ospite

Il comportamento dell'operatore `delete` può essere imprevedibile per gli oggetti dell'ambiente ospite. Secondo la specifica, a tali oggetti è consentito implementare qualsiasi tipo di comportamento.

### Conclusione

L'operatore `delete` ha spesso un comportamento inaspettato e può essere usato tranquillamente solo per cancellare esplicitamente le proprietà impostate su oggetti normali.

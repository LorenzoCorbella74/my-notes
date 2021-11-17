# TypeScript Getting Started

Appunti del corso [TypeScript Getting Started](https://app.pluralsight.com/library/courses/getting-started-typescript/table-of-contents). 
- File del corso: [Demo app](https://github.com/bricewilson/Typescript-Getting-Started)
- [Sito ufficiale typescript](https://www.typescriptlang.org)
- versione usata `4.2.2`

## Cos'è
E' un linguaggio opensource, superset di javascript, che produce tramite transpilazione codice javascript, aggiungendo molte funzionalità tra cui la static type definition (static types significa “at compile time” o “without running a program”, mentre dynamic types significa “at runtime”).

## Installazione e configurazione
E' necessario avere [node](https://nodejs.org) e poi tramite npm:
```bash
# localmente
& npm install typescript --save-dev
# o globalmente
& npm install typescript -g
```
Per avviare la tranpilazione:
```bash
& tsc              # usa le impostazioni dentro tsconfig.json 
& tsc --init       # compila automaticamente tsconfig.json con impostazioni di default
```
E' possibile configurare un progetto typescript tramite un file chiamato `tsconfig.json` che permette di indicare le opzioni di compilazione, file da includere e da escludere nella compilazione e supporta la configuration inheritance.
```javascript
{
    compilerOptions:{
        "target":"es5",         // quale versione di js
        "outFile":"output.js",  // il nome del file di output o della cartella
        "noUnusedLocals":true,
        "sourceMap":true,       // per debug
        "strict":true,          // applica strict check (in caso usare ! dopo il nome della variabile per specificare che siamo sicuri che l'obj esista... ! è il "not null assertion operator")
        "watch":true,           // ricompila quando i file cambiano
    },
    files:['app.ts', ]            // array di file di input 
}
```
Per tutte le opzioni vedere la relativa pagina di [documentazione](https://www.typescriptlang.org/tsconfig)

Per avere maggiori possibilità di configurazione si può mettere un file `tsconfig.base.json` all'esterno e dentro cartelle figlie è possibile specificare opzioni particolari in altri file `tsconfig.json`. Tramite la configuration inheritance le informazioni verranno opportunamente unite. 
```javascript
// esempio di file di configurazione "figlio"
{
    extends:"../tsconfig.base.json",    // indica che file estende
    compilerOptions:{
        "removeComments":true,  // rimuove i commenti
        "strictNullChecks":true,  // null diventa un tipo
        "module":"commonjs",
        "traceResolution":true // il compilatore indicherà come i moduli sono stati risolti
        "baseUrl":"./modules" // path "base" quando si vuole risolvere dei moduli non relative module imports
    },
    include:['./**/*.ts']       // array di pattern di file di input, directory e subdirectory
}
```

## Strict mode
Lo strict mode equivale a 6 regole ed è consigliato per rendere il compilatore typescript meno permissivo possibile:

```json
{
    // ...,
    "compilerOptions": {
        // a set of cool rules
        "noImplicitAny": true,  // forza a definire tutte i i tipi dell'applicazione 
        "noImplicitThis": true,
        "strictNullChecks": true,   // l'opzione si assucura che non sia possibile accedere ad una proprietà di un valore null
        "strictPropertyInitialization": true,
        "strictBindCallApply": true,
        "strictFunctionTypes": true,
        // a shortcut enabling 6 rules above
        "strict": true,
        // ...
    }
}
```

# Types

## Built-in Types
- Boolean 
- Number   
- String 
- Array
- Enum
- Void -> indica l'assenza di un tipo
- Null, Undefined -> 
- Never : è il tipo usato per valori che non accadranno mai
- Any : quando non si conosce il tipo (per librerie esterne as esempio)

### Dichiarazioni con let and const
Typescript lancia un errore di compilazione se l'uso di una variabile è precedente alla dichiarazione (come non avviene con var per l'hoisting di js), pertanto l'uso di let è preferibile, così come l'uso di const per variabili che non cambiano il loro valore.

### Type annotations and type inference
E' possibile annotare le variabili tramite la sintassi:
```typescript
let x: string = 'stringa' // se poi x= 42 ts lancia un errore di compilazione

let someValue : number | string // union type trmite "|"
```
Da notare che anche se non specifico il tipo il compilatore ne assume uno in base al valore dato in fase di dichiarazione.

### Managing null and undefined
Con l'opzione del compilatore `--strictNullChecks` null e undefined non sono considerati dei tipi validi, salvo non venga specificato.
```typescript
let x: string = 'stringa'
x = null        // errore compilatore
x = undefined   // errore compilatore

let y :string | null // si specifica l'union type
y = null // ok del compilatore
y = undefined // errore del compilatore

let z :string | null | undefined // si specifica l'union type
```

Per gestire undefined in typescript si possono utilizzare vari metodi. 

```typescript
function validateToken(token: string) {
  return token;
}

const token = 'kjadj' as string | undefined;

validateToken(token);
```

Considerato che il token può essere undefined si possono utilizzare vari metodi:
```typescript
/* 1) condizioni IF ELSE */
if (token) {
  validateToken(token);
}
// oppure con typeof
if (typeof token === 'string') {
  validateToken(token);
}

/* 2) operatore OR */
 validateToken(token || 'default');

/* 
    3) operatore AS 
    La keyword as, può essere usata per indicare a TypeScript, 
    che sappiamo per certo che il valore sarà qualunque del tipo indicato.
*/
validateToken(token as string)

/* 
    4) ! non-null assertion operator 
    Si indica al compilatore che la variabile sia null o undefined
*/
validateToken(token!)

/* 
    5) Nullish coalescing operator (??)
    Metodo simile all'OR la differenza è:
    - OR logical operator checks for falsy values (false, undefined, null, 0, NaN, and a empty string).
    - Nullish coalescing operator (??) only checks for undefined or null.
*/
validateToken(token ?? 'default-token');
```

### Type assertions
Per le asserzioni si usa la due sintassi:
```typescript
let x: any = 5
let fixedStr :string = (<number>x).toFixed(4) // assertion
let fixedStr :string = (x as number).toFixed(4) // assertion
```

### Control flow-based type analysis
Il compilatore di typescript fa constantemente un controllo (analisi) sul tipo di ogni variabile tenendo conto del flusso del codice, riducecendo la confusione ed aumentando la chiarezza del codice e riducendo la passibilità di avere effetti non voluti aumentando la stabilità generale del codice prodotto.


# Using types and functions
Si può aggiungere i tipi al valore ritornato (`:void` se non ritorna niente) e ai parametri di una funzione con la seguente sintassi:
```typescript
function fn (score:number = 10, message?:string):string {

}

function getInputValue(elementId:string): string | undefined {
    const inputElement: HTMLInputElement = <HTMLInputElement>document.getElementById(elementId)
    if (inputElement.value===''){
        return undefined
    } else {
        return inputElement.value
    }
}

function postScore(score:number, playerName:string):void {
    const scoreElement:HTMLElement | null = document.getElementById('postedScores')
    scoreElement!.innerText = `${score} - ${playerName}`
}
```

Nel caso in cui i tipi dei parametri non siano indicati ts li considera di tipo `any` a meno che sia usata l'opzione del compilatore `--noImplicitAny` che permette di avere dei warning in fase di compilazione.

Da notare che è possibile mettere dei valori di default degli attributi: in tali situazioni ___è come mettere tali attributi opzionali___ !!

Per le Arrow function (lambda fn) si usa:
```typescript
 let greeting = (message:string):void => console.log(`Hello ${message}`)
```

Quando si passano delle funzioni si usa come tipo `function`

```typescript
let logger: (value:string)=> void;
```

# Type utility
Typescript permette di usare delle [utility type](https://www.typescriptlang.org/docs/handbook/utility-types.html): 

## Union type
Permette di specificare che una variabile può essere di più tipi

```typescript
function logIdentifier(id: string | undefined) {
    if(!id) {
        console.error('no identifier found');
    } else {
        console.log('id', id);
    }
}
```
E' possibile anche utilizzare l'`intersection types` che è principalmente usato per combinare dei tipi di oggetti esistenti. L' `intersection type` è definito usando l'operatore `&`.
Se vogliamo però esprimere types/interfaces in una maniera più dinamica e potente, si deve usare i generics


## keyof
Per ogni tipo T, keyof T è l'unione dei nomi delle proprietà pubbliche conosciute di T.

```typescript

interface Person {
  age: number;
  name: string;
}

type PersonKeys = keyof Person; // "age" | "name"
```

Se usato insieme ad extends permette di limitare il tipo di un parametro generico  <T, K extends keyof T> , K può essere soltanto una chiave di una proprietà pubblica di T (non ha niente a che fare con estendere un tipo o ereditarietà). Un [caso d'uso](https://mariusschulz.com/blog/keyof-and-lookup-types-in-typescript) può essere:

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person: Person = {
  age: 22,
  name: "Tobias",
};

// name is a property of person
// --> no error
const name = getProperty(person, "name");

// gender is not a property of person
// --> error
const gender = getProperty(person, "gender");
```

## Partial
Permette di rendere tutte le proprietà opzionali in un tipo dove tutte le proprietà originariamente dovrebbero essere richieste.

```typescript
type Worker = {
  name: string;
  profession: string;
}

// Not defining 'profession' is allowed
const worker: Partial<Worker> = {
  name: 'Jeroen' 
}
```

# Required
L'opposto di  `Partial` è `Required`, invece di rendere le proprietà opzionali, le rende required.

```typescript
type Worker = {
  name?: string;
  profession?: string;
}

// You should defining 'name' and 'profession'
const worker: Required<Worker> = {
  name: 'Jeroen',
  profession: 'Developer',
}
```

## Readonly
Vediamo un esempio di `Readonly` che permette di rendere tutte le proprietà di una entità read only:

```typescript
interface Teacher {
  name: string;
  email: string;
}

/*
Invece di mettere:

    interface Teacher {
        readonly  name: string;
        readonly email: string;
    }

Si può usare:
*/

type ReadonlyTeacher = Readonly<Teacher>;

const t: ReadonlyTeacher = { name: 'jose', email: 'jose@test.com'};

t.name = 'max'; // Error: Cannot assign to 'name' because it is a read-only property.(2540)

// oppure con l'operatore  -  che toglie il modificatore successivo
type Writeable<T> = { -readonly [P in keyof T]: T[P] };

const t: Writeable<Teacher> = { name: 'jose', email: 'jose@test.com' };

t.name = 'max'; // works fine
```

L'idea è quello di avere delle strutture immutabili usando `readonly` o `Readonly`. 
Da typescript 3.4 si può utilizzare anche `as const` che rende la struttura non modificabile.


## Pick
`Pick`, permette di includere solo alcune proprietà selettivamente:

```typescript
type Worker = {
  name: string;
  profession: string;
  isWorking: boolean;
}

// Only 'name' and 'isWorking' are included
const worker: Pick<Worker, 'name' | 'isWorking'> = {
  name: 'Jeroen',
  isWorking: true,
}
```

## Omit
L'opposto di `Pick` è chiamato `Omit`, che esclude le proprietà che non si vuole usare.

```typescript
type Worker = {
  name: string;
  profession: string;
  isWorking: boolean;
}

// 'profession' is now excluded
const worker: Omit<Worker, 'profession'> = {
  name: 'Jeroen',
  isWorking: true,
}
```

## Record
Il tipo `Record` permette di definire un oggetto dove le chiavi hanno un tipo differente del corrispettivo valore.

```typescript
type Worker = 'developer' | 'architect'
type Person = { name: string }

// a worker object has now 'developer' or 'architect' as keys
const worker: Record<Worker, Person> = {
  'developer': { name: 'Jeroen' },
  'architect': { name: 'Bob' },
}
```

## NonNullable
Un modo facile per escludere null and undefined dai valori è usare il tipo `NonNullable`.
```typescript
// Returns only 'Jeroen'
const worker: NonNullable<'Jeroen', undefined, null>;
```

## Extract
Per estrarre valori in questo caso di tipo `keyof`, si può usare `Extract` per includere soltanto le chiavi che esistono nei tipi dati.

```typescript
type Worker = {
  name: string;
  age: string;
  isWorking: boolean
}
type Person = {
  name: string;
  age: string;
}
// 'name' or 'age' is both in type Person and Worker therefore only allowed 
const worker: Extract<keyof Worker, keyof Person> = 'name' || 'age';
```


## Exclude
Per escludere i valori, in questo caso di tipo `keyof`, si può usare `Exclude` per escludere le chiavi che sono duplicatein entrambi i tipi dati:

```typescript
type Worker = {
  name: string;
  age: string;
  isWorking: boolean
}
type Person = {
  name: string;
  age: string;
}
// 'isWorking' is not in type Person and therefor only allowed 
const worker: Exclude<keyof Worker, keyof Person> = 'isWorking';
```

# Classes e interfaces (Custom Types)

Sia le Interfacce che le Classi permettono di creare tipi "custom". Le interfacce definiscono delle propietà e dei metodi generici (signatures) mentre le classi permettono di definire proprietà e metodi e loro valorizzazione ed implementazione. Le interfacce definiscono un contratto (le caratteristiche ola forma che deve avere un obj senza la sua implementazione, una astrazione senza dettagli) ma non possono essere istanziate mentre le classi possono essere istanziate. 

## Interface

```typescript
interface Employee {
    name:string,
    title:string,
    age?:number   // opzionale quindi è uno union type  number | undefined
}

interface Manager extends Employee {
    department:string,
    numOfEmployees:number,
    scheduleMeeting: (topic:string)=> void
}

let develop: Employee = {
    name:"Lore",
    title:"Developer",
    editot:"VSC
}

```
Da notare che Le inferfacce sono uno strumento usato dal compilatore ma non esistono nel codice compilato javascript.

### Classi
Tutti i metodi sono pubblici di default anche se esiste un access modifiers `public` (oltre che `private`)

```typescript

class Developer { 
    department:string
    what?:number 
    private _title:string   // usato dal compilatore per controllo ma poi è compilato. In JS ora si usa #nomeprop per indicare prop private
    get title():string {
        return this._title;
    }
    set title(newTitle:string){
        this._title = newTitle.toUpperCAse()
    }

    constructor(){
        // initialisation of new istances (not mandatory)
    }

    methodOne(){}

    static jobDescription:string = 'Build website!'
    // metodo che può essere chiamato senza istanziare la classe
    // si usano i metodi statici generalmente per utility e non dipendano 
    // da nessun dato conservato nell'istanze della classe 
    static logDesc():void{
        console.log(Developer.jobDescription)
    }
}

interface Worker {
    favoriteIDE:string
}

// se una classe implementa una interfaccia deve avere almeno tutto
// ciò che c'è dentro l'interfaccia + altre proprietà ed eventuali metodi
class WebDeveloper extends Developer implements Worker{
    readonly favoriteIDE:string // readonly previene la modifica una volta che è settata nel costruttore !!!
    writeTypeScript():void {
    }

    constructor(editor:string){
        super() // chiama il costruttore della classe che estende
        this.favoriteIDE = editor
    }
}

/*
    Da notare che nel costruttore invece di 

    player: PLayer
    costructor (newPlayer:PLayer){
        this.player = newPLayer
    }

    si usa direttamente lo shortcut:

    constructor(public player:PLayer) {}
*/

let webdev: WebDeveloper = new WebDeveloper()
webdev.department = 'Sowtware Engineering'
```

# Creare e consumare Modules
Per creare dei riferimenti ad altri file che permettono al compilatore di avere tutti gli elementi da cui un file è dipendente si usa la sintassi (triple slash directive):
```typescript
/// <reference path="player.ts">
```
Invece di usare i riferimenti sopra può essere più flessibile utilizzare i moduli che esportano ed importano solo specifiche funzionalità creando una API constistente che permette di:
- incapsulare codice 
- riutilizzare codice
- creare delle astrazioni di alto livello

Il compilatore di typescript può produrre moduli js AMD, CommonJs(node), UMD, System, ES2015, mentre adotta nativamente il formato ES2015.

## Export 
Si può esportare tutto ciò che è dichiarato, 
```typescript
// .person.ts
export interface Person {}
export default class PersonDev {} // default indica che sarà l'elemento di default esportato dal modulo
export function hireDeveloper():void {}
export const lore = {}

// oppure una lista di elementi
export {Person, hireDeveloper, PersonDev as StaffMember}    // as come alias
```


## Import
```typescript
import {Person, hireDeveloper} from './person' // /person sarebbe stato
let human:Person;

import Worker from './person' // si dà al default il nome di Worken
let engineer:Worker = new Worker()


import {StaffMember as CoWorker} from './person'; // alias
let emp:CoWorker = new CoWorker()


import * as HR from './person';     // si prende tutto come un obj HR
```
Quando si ha `/person` si indica il root del filesystem mentre `./person`  o `../path/person` indica un percorso relativo rispetto al file corrente. 
```typescript
import *as $  from 'jquery' // path NON relativo, senza indicazioni per 3rd party module
```
Typescript risolve la posizione del modulo prima vedendo se è un path relativo o non relativo e poi usando la Module Resoltion Strategy (--moduleResolution Classic | Node)
- `classic` quando produce moduli AMD, UMD,System o ES2015
- `node` per CommonJs module


## [Oggetti dinamici](https://stackoverflow.com/a/44441178)
Per assegnare delle proprietà ad un oggetto vuoto visto che non avrebbe senso assegnare any si usano i così chiamati "index types":
```typescript
var obj = {};
obj.prop = "value"; // in typescript questo dà un errore

interface LooseObject {
    [key: string]: any
}

var obj: LooseObject = {};

// o in forma compatta 
var obj: {[k: string]: any} = {};

interface MyType {
    typesafeProp1?: number,
    requiredProp1: string,
    [key: string]: any
}

var obj: MyType ;
obj = { requiredProp1: "foo"}; // valid
obj = {} // error. 'requiredProp1' is missing
obj.typesafeProp1 = "bar" // error. typesafeProp1 should be a number
obj.prop = "value";
obj.prop2 = 88;
```

# Type declaration files 
Le informazioni di tutti le definizioni di tipi di una libreria (tipi per variabili, funzioni, etc), che permettono l'uso del code completion, sono contenuti in file `.d.ts`. 

Dove si trovano? Generalmente si possono trovare nel github repository DefinitelyTyped che contiene centinaia di librerie, che vengano manutenute indipendentemente dalle relative librerie javascript e si installano con
```bash
$ npm install @type/<name_library> # scarica file da DefinitelyTyped
```

E' possibile cercare sul [sito](https://www.typescriptlang.org/dt/search?search=)

Per creare .d.ts file da .js file usando la sintassi di JSDoc vedere la [documentazione](https://www.typescriptlang.org/docs/handbook/declaration-files/dts-from-js.html)







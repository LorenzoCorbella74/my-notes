[Back to index](README.md)

# Notes

In typescript l'idea è quando abbiamo chiaro lo stato di una applicazione dovremmo poi definire il tipo di ogni variabile dello stato e come questo viene manipolato da funzioni e metodi ognuno con i propri tipi di input e di output. Per non moltiplicare i tipi l'idea è quella di estendere i tipi dello stato con tipi dell'ui.


# undefined value
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

# Types

- [typescript-object-types](https://medium.com/nerd-for-tech/typescript-object-types-61c956564f87)


# type alias

```typescript
type Persona = {
    [key:string]: string | number | boolean,    // sintassi per specificare proprietà di cui non si conosce la chiave
    mode: string
}
```


# Union type
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

# generics
Data una funzione:

```typescript
function addItem(item: string, array: string[]) {
    array = [...array, item];
  return array;
}
```

Si rende "generica" con la seguente sintassi:

```typescript
function addItem<T>(item: T, array: T[]) {
  array = [...array, item];
  return array;
}
```

Come si può restringere i tipi ammessi in `T`? Si può usare la keyword `extends`
```typescript
function addItem<T extends boolean | string>(item: T, array: T[]) {
  array = [...array, item];
  return array;
}

addItem('hello', []);

addItem(true, [true, true]);

addItem(new Date(), []);
//      ^^^^^^^^^^
// Argument of type 'Date' is not assignable to parameter of type 'string | boolean'
```

# Tuples
I tipi Tuple permettono di esprimere un array con un numero fisso di elementi i cu itipi sono noti ( e non devono essere gli stessi):
```typescript
// esplicitamente
const array: [string, number] = ['test', 12];

// implicitamente
// as const rende l'array read only
const array = ['test', 12] as const;
```

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

# Mapped types
Typescript permette di usare delle [utility type](https://www.typescriptlang.org/docs/handbook/utility-types.html): 

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




# Type guards
Le Type guards sono funzioni che ritornano un booleano, ma hanno la caratteristica specifica di assicurarsi, a seconda del booleano ritornato, che il valore testato sia di un tipo dato. Si basano sui seguenti operatori:
- typeof operator from JavaScript to check primitive types
- instanceof operator from JavaScript to check inherited entities
- is T declaration from TypeScript allows checking complex types and interfaces. But you should be very careful using this feature because you make the correct type check your responsibility, not TypeScript's.

```typescript
function isNumber(x: any): x is number {
  return typeof x === "number";
}

function add1(value: string | number) {
  if (isNumber(value)) {
     return value +1;
  }
  return +value + 1;
}
```

```typescript
interface Hunter {
    hunt: () => void;
}

// function type guard
function isHunter(x: unknown): x is Hunter {
    return (x as Hunter).hunt !== undefined;
}

const performAction = (x: unknown) => {
  if (isHunter(x)) {
    x.hunt();
  }
}

const animal = {
  hunt: () => console.log('hunt')
}

performAction(animal);
```

# Strict mode
Lo strict mode equivale a 6 regole

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

# String Literals & Numerical literal types
E' possibile specificare quali valori una variabile stringa o variabile numerica possono prendere specificando i possibili valori,separandolo con il pipe | .  

```typescript
type PersonName = 'Joe' | 'Jane' | 'Amy';
let personName: PersonName = 'Joe';

// errore
let personName: PersonName = 'foo';
Type '"foo"' is not assignable to type 'PersonName'.(2322)

type Num = 1 | 2 | 3;
let num: Num = 1;

// errore
let num: Num = 6;
Type '6' is not assignable to type 'Num'.(2322)
```

E' possibile inoltre creare delle unioni discriminate del tipo:

```typescript
interface Cat {
  kind: "cat";  // è la proprietà discriminante
  whiskerLength: number;    
}
interface Dog {
  kind: "dog";
  snoutSize: number;    
}
interface Bird {
  kind: "bird";
  beakSize: number;
}
interface Hippo {
  kind: "hippo";
  teethSize: number;
}

type Animal = Cat | Dog | Bird | Hippo;

function getSize(animal: Animal) {
  switch (animal.kind) {
      // ci devono stare tutti e 4 i tipi specificati in Animal
    case "cat": return animal.whiskerLength;
    case "dog": return animal.snoutSize;
    case "bird": return animal.beakSize;
    case "hippo": return animal.teethSize;
  }
}
```

# Enum
[typescript-enums](https://medium.com/jspoint/typescript-enums-af03567d662)


## Rimuovere errori
```typescript
// 2) Eliminare l'errore
if (false) {
    // @ts-ignore
    console.log('x');
}
```

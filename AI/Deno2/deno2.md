# [Deno 2](https://deno.com/)
E' un runtime JavaScript open-source moderno. Le sue carateristiche principali sono:

## 1. [TypeScript nativo](https://docs.deno.com/runtime/fundamentals/typescript/)
Esegue codice typescript nativamente senza bisogno di compilazione o dipendenze esterne (e supporta nativamente JSX e TSX ed ultime versioni di ECMAScript).

```typescript
// hello.ts
function hello(name: string): string {
  return `Hello, ${name}`;
}

console.log(hello("Deno"));
```
E' possibile eseguire il codice con:
```bash
#esegue la REPL
deno

# esegue il codice
deno run hello.ts # o direttamente deno hello.ts

# senza type checking
deno run --watch hello.ts

## con type checking
deno run --check hello.ts
# or also type check remote modules and npm packages
deno run --check=all hello.ts
```
per fare il type check **senza** eseguire il codice:
```bash
deno check module.ts
# or also type check remote modules and npm packages
deno check --all module.ts
# code snippets written in JSDoc can also be type checked
deno check --doc module.ts
# or type check code snippets in markdown files
deno check --doc-only markdown.md
```
Altri comandi sono:
```bash
## per vedere i principali comandi
deno --help

## per creare un progetto
deno init my-demo

## deno upgrade per aggiornare Deno o per mettere 
## una versione specifica come Node version manager
deno upgrade --version1.0.1
deno upgrade rc # per l'ultima release candidate

## per vedere le dipendenze non aggiornate
deno outdated

# Run a server
deno serve main.ts

# Run a task defined in the configuration file deno.json
deno task dev

# per eseguire i test
deno test main_test.ts
```
Relativamente ai task, è possibile definire un file `deno.json` con i comandi da eseguire:
```json
{
  "tasks": {
    "dev": "deno task dev:api & deno task dev:vite",
    "dev:api": "deno run --allow-env --allow-net --allow-read api/main.ts",
    "dev:vite": "deno run -A npm:vite",
    "build": "deno run -A npm:vite build",
    "serve": {
      "command": "deno task dev:api",
      "description": "Run the build, and then start the API server",
      "dependencies": ["deno task build"]
    }
  },
  "imports": {
    "@hono/hono": "jsr:@hono/hono@^4.6.12",
    "@solidjs/router": "npm:@solidjs/router@^0.14.10"
  },
  "compilerOptions": {
    "jsx": "react-jsx",
    "jsxImportSource": "solid-js",
    "lib": ["DOM", "DOM.Iterable", "ESNext"]
  }
}
```
Notare che gli imports sono degli `alias` che puntano a specifici moduli.

## 2. Uso di moduli Node e NPM
Con la versione 2 è possibile usare sia i moduli node che quelli di NPM con gli opportuni specifiers `node:` e `npm:`.
```typescript
import * as os from "node:os";
console.log(os.cpus());

import * as emoji from "npm:node-emoji";
console.log(emoji.emojify(`:sauropod: :heart:  npm`));
```

Per formattare il codice, Prettier e ESLint sono integrati in Deno 2, quindi è possibile usare `deno lint --fix` per eseguire il linting e `deno fmt` per formattare il codice. 
Per grandi progetti è possibile gestire le dipendenze con un file manifest. Inserire un specifier npm: in un file `deno.json` per importare facilmente il pacchetto:

```json
// deno.json
{
  "imports": {
    "chalk": "npm:chalk@5.3.0"
  }
}
```
In deno 2 sono stati introdotti diversi subcommands to simplify dependency management: `deno add`, `deno remove`, and `deno install`. Usare  deno add per aggiungere dipendenze direttamente ai file `deno.json` o `package.json`:

```bash
deno add jsr:@std/path npm:chalk
```
If no config files exist, Deno will create a deno.json with an import map:
```json
{
  "imports": {
    "@std/path": "jsr:@std/path@^1.0.8",
    "chalk": "npm:chalk@^5.3.0"
  }
}
```
Per rimuovere una dipendenza:
```bash 
deno remove jsr:@std/path
``` 
E' possibile clonare un progetto Node, ed usare `deno install` per importare tutte le dipendenze. Come npm install, deno install installs all dependencies listed in `deno.json` or `package.json`. Se il `package.json` è presente, le dependencies npm saranno installate nella cartella `node_modules`. Altrimenti, Deno installa i pacchetti nella global cache. 
```bash 
deno install
```


## 3. New JavaScript Registry (JSR)
Ha un suo repository e package manager [JSR](https://jsr.io/), che supporta TypeScript nativamente (è possibile pubblicare codice TypeScript senza doverlo compilare).
I moduli possono essere usati oltre che con Deno anche Bum, NPM, PNPM. Si veda il modulo di esempio [cheer-reader](https://jsr.io/@paoramen/cheer-reader).

## 4. Deno Standard Library
La deno standard library, [presente in JSR](https://jsr.io/@std), è stabile e contiene moduli di utilità per tutto, dalla manipolazione dei dati alla logica correlata al web e alle funzionalità specifiche di JavaScript.
```typescript
import { copy } from "@std/fs";
import { join } from "@std/path";

import {parse} from "https://deno.land/std@1.2.1/flags/mod.ts"; // per importare un modulo da un URL

await copy("foo.txt", join("dist", "foo.txt"));
``` 
## 5. Browser Wep APIS compatibile
Deno utilizza le wep api come fetch, localstorage, etc.  
```typescript
const response = await fetch("https://api.github.com/users/denoland");
const json = await response.json();
console.log(json);
```

## 6. Secure by default
- Nessun accesso al file system, alla rete o all'ambiente di esecuzione a meno che non sia esplicitamente concesso.
- Nessuna possibilità di eseguire codice non sicuro a meno che non sia esplicitamente concesso.
Per poter eseguire il codice si usa:
Per rendere possibile i comandi di lettura e scrittura dei file, l'accesso alla rete, l'accesso all'ambiente di esecuzione e l'esecuzione di comandi esterni, l'utente deve concedere esplicitamente il permesso al runtime Deno. Questo viene fatto passando i flag `--allow-read`, `--allow-write`, `--allow-net`, `--allow-env` e `--allow-run` al comando deno.
```bash
deno run -A script.ts # equivalente a --allow-all
deno run -RWNE script.ts  # equivalente a --allow-read --allow-write --allow-net --allow-env
deno run -R script.ts  # equivalente a --allow-read 


deno run --allow-all script.ts
```

## 7. Built-in compiler
E' possibile utilizzando il comando `deno compile` creare un eseguibile standalone del proprio codice Deno.
```typescript
// main.ts
import open from "jsr:@rdsq/open";
await open("https://www.youtube.com/watch?v=dQw4w9WgXcQ");
```
```bash
deno compile -A -o runme main.ts
```
Per le varie opzioni e l'utlizzo in windows vedere la pagina in [documentazione](https://deno.com/blog/deno-compile-executable-programs).


## Links
- [VSC Deno extension](https://marketplace.visualstudio.com/items?itemName=denoland.vscode-deno)
- [Official Deno Docs](https://docs.deno.com)
- [debug deno](https://docs.deno.com/runtime/fundamentals/debugging/)
- [Standard Library](https://jsr.io/@std)
- [Fresh: official deno FE framework](https://github.com/denoland/fresh)
- [Official deno 2 tutorials](https://www.youtube.com/playlist?list=PLvvLnBDNuTEov9EBIp3MMfHlBxaKGRWTe) Learn Deno

## Progetto di esempio
- l'[articolo](https://deno.com/blog/build-solidjs-with-deno) ed il [codice](https://github.com/denoland/examples/tree/main/with-solidjs) relativo allo scaffolding di una app SolidJS app with Vite e Back end con Kono
- [websearch with ollama](https://github.com/technovangelist/videoprojects/blob/main/2024-10-01-websearch/main.ts)
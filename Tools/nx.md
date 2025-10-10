[Back to index](../README.md)

# NX

[NX](https://nx.dev/) è un tool/piattaforma/build system per la **gestione di
monorepo (workspace)**, che consente di organizzare, costruire e testare progetti
di grandi dimensioni in modo efficiente (senza essere legato a nessun framework
in particolare, anche se supporta vari framework come React, Angular, Vue,
Node.js etc) offrendo funzionalità avanzate come:

- il caching delle build/test,
- l'analisi delle dipendenze
- code generation
- automatic migration
- l'integrazione con CI/CD.
- intelligent build optimisation (build test only what changed)
- VSC plugin
- supporta MCP server via NX console plugin
- supporta migrazione di progetti esistenti in monorepo

# I Monorepo

Monorepo superano i limiti dei "monoliti", consentendo di gestire più progetti
in un'unica repository, facilitando la condivisione del codice (unified code
standard) e le dipendenze e la collaborazione tra team. I monorepo posso
includere linguaggi di programmazione diversi. I difetti sono:

- la dimensione
- branching & merging più complessi
- CI/CD più complessi

## Installazione NX

Per installare NX, è necessario avere Node.js e npm installati. Consideriamo Nx
21.2.2, Angular 20, Node 22.17. Si può installare NX globalmente con il seguente
comando:

```bash
npm install -g nx
```

# 1. Migrazione progetto Angular esistente

Per migrare da un progetto angular ad un Nx Workspace **standalone** si può
usare il comando:

```bash
npx nx@latest init
```

Quest istalla l'ultima versione di Nx ed inizia il progesso di migrazione e poi
ci chiede quale processo dovrebbe essere reso cachable (build, serve, test, etc)
e se il caching è locale o remoto. **Una volta finito il progetto Angular è un
Nx Workspace**.

Oltre al file `nx.json` (che indica quali task sono cachati e le configurazioni per l'interno workspace) il file
`package.json` ha gli script che non sono più dipendenti da Angular CLI ma da Nx
CLI. Inoltre è stato aggiunto il `project.json` che contiene le informazioni
specifiche del progetto (ogni app/libreria nel workspace ne ha uno). Da notare
che ogni progetto ha il **name**, **projectType** (application/library) e
**targets** (build, serve, test, lint etc) **un pò come l'angular.json prodotto
dall'Angular CLI**.

Si esegue l'app Angular con:

```bash
npm start # mappato su nx serve

# si builda con con il buildato in dist/<nome-app>
npx nx build

# test
npx nx test --watch=false
```

Per generrare un grafo delle dipendenze si usa:

```bash
npx nx graph
```

Viene generato un sito all'url `http://localhost:4211/projects` con il grafo
delle dipendenze.

Partendo da un progetto Angular creato non l'angular cli tramite il comando:

```bash
npx nx@latest init --integrated
```

Questa volta con l'opzione `--integrated` Nx aggiorna il progetto esistente e
quando finisce la cartella `src` è stata spostata in `apps/<nome-app>/src` e
viene creato il file `project.json` con le informazioni del progetto. Il file
`nx.json` ha in `defaultBase` a master. Questo rappresenta la nuova struttura
dei progetti in un workspace Nx. In questo caso dalla root della cartella si
usa:

```bash
# per servire l'app
npx nx serve <project-name>

# per buildare, testare, lintare
npx nx build <project-name>
# per testare
npx nx test <project-name> --watch=false
```

# 2. Creare un workspace nuovo

## 2.1 STANDALONE

Per creare un nuovo workspace stanalone con NX, si può utilizzare il comando:

```bash
npx create-nx-workspace@latest myworkspace
```

Si possono scegliere

- vari preset (Angular, React, Next.js, Node.jsetc)
- tipologia workspace **Integrated** (un monorepo che contiene molti progetti) o
  **Standalone (un singolo progetto da ottimizzare tramite cache, ma
  eventualmente più librerie)**.
- bundler (esbuild, webpack, Rspack etc)
- ssr
- styling (css, scss, tailwind etc)
- test runner (jest, vitest, etc)
- e2e test (cypress, playwright etc)
- ci provider (github actions, azure, gitlab etc)

Scegliendo standalone workspace si ha una src/app con il file `project.json`
esterno al src che contiene le informazioni del progetto. Si starta
l'applicazione con `npx ng serve`

## 2.2 INTEGRATED

Per creare un nuovo workspace integrato con NX, si può utilizzare il comando:

```bash
npx create-nx-workspace@latest myworkspace
```

Questa volta si sceglie il preset **Integrated monorepo** e poi si sceglie il
framework (Angular, React, Vue.js, Node.js etc) e le altre opzioni come sopra.
In questo caso si ha una cartella `apps` con l'applicazione `<nome-app>` e anche
una cartella `<nome-app-e2e>` per i test e2e. Ancora non abbiamo aggiunto
librerie consivise che invece finiscono dentro la cartella `libs`. Per servire
l'app si usa:

```bash
npx nx serve <nome-app>
# per buildare
npx nx build <nome-app>
# per testare
npx nx test <nome-app> --watch=false
```

Per aggiungere un'altra app (ad esempio una app React) si usa:

```bash
npx nx generate @nx/angular:application
```

Nel prompt che seguen specificare la directory: `apps/<nome-second-app>` e le
precedenti configurazioni. Per servire la seconda app si usa:

```bash
npx nx serve <nome-second-app>
```

Notare bene che **le applicazioni sono indipendenti e possono avere dipendenze
diverse.** così come ad una versione di NX corrisponde una versione specifica di
Angular (per questo vedere
[Nx and Angular version](https://nx.dev/docs/technologies/angular/guides/angular-nx-version-matrix))


# 4. Rapporto tra Nx e Angular
Angular è di fatto un plugin per Nx in quanto si deve usare il package `@nx/angular` per creare applicazioni e librerie Angular. Per aggiungere applicazioni angular usare:

```bash
# per applicazioni
npx nx generate @nx/angular:application apps/appName
npx ng g @nx/angular:app apps/appName

# per librerie
npx nx generate @nx/angular:library libs/libName
npx ng g @nx/angular:lib libs/libName
```
Per le applicazioni si creano automaticamante i seguenti targets:
- build
- serve
- test
- lint
- e2e
Per le librerie si hanno:
- build
- test
- lint

## 5. Comandi principali
I comandi sono generalmente: `nx` `<target>` `<project-name> <overwrite params>` , se non si ha un project-name si intende il progetto di default (specificato in `nx.json`). Alternativamente si può usare `nx run <project-name>:<target>`. Facciamo due esempi:

```bash 
# per servire l'app di default
nx lint dogs-domain --watch

nx run dogs-domain:lint --watch
```
Oppure si possono far girare comandi per tutti i progetti contemporaneamente con `run-many`:

```bash
nx run-many --target=test --watch
nx run-many --target=lint # -t lint

#oppure
nx run-many --projects=project1,project2 --target=test --watch
```

# 6. Esempio

Si genera un nx workspace integrato con Angular e si crea una app `dogs` e una libreria `dogs-domain`:

```bash
npx create-nx-workspace@latest my-test-workspace --preset=angular --appName=dogs --style=scss --nx-cloud=skip --ci=github
cd my-test-workspace
```
Da notare che all'interno del tsconfig.base.json del workspace sono  configurati gli alias con gli entry points delle librerie (una volta aggiunge), mentre se non esistono tali percorsi le dipendenze vengono prese da node_modules:

```json
{
    ...
    paths: {

        "@my-test-workspace/dogs-domain": ["libs/dogs-domain/src/index.ts"]
    },
    ...
}
```
Per far girare l'app (da notare che la configurazione di default è per la produzione):

```json
{
    "scripts": {
        "start": "nx serve dogs --configuration=development",
        "build": "nx build dogs",
        "test": "nx test dogs --watch=false",
        "lint": "nx lint dogs --fix",
        "e2e": "nx e2e dogs-e2e"
    }
}
```

Da notare che parti dell'app possono essere **specifiche** (e finire nell'applicazione sotto una feature specifica) e **generiche** (riutilizzabili e finire nella libreria). 


# 7. Tipi di librerie
Le librerie hanno l'obiettivo di condividere codice tra più applicazioni ma anche di fornire fornire feature ad una sola applicazione ed essere così completamente flessibili. Le librerie possono essere di vario tipo:
- fornire feature ad una singola app
- multiple libs = one feature
- grouped by scope
- can be only for code organisation
- can be publishable con un artefatto pubblicabile su npm

Possono essere di vario tipo:
- feature or container (container component)
- domain or data access (librerie che permettono di accedere a dati quali servizi, models, state management etc)
- UI (componenti, direttive, pipes etc)
- Utils (helpers, functions etc)
- api (librerie che contengono servizi per chiamate http a backend etc)

L'idea è comunque di avere delle applicazioni "leggere" (20% del codice) e delle librerie "pesanti" (80% del codice) che contengono la logica di business e che possono essere riutilizzate in più applicazioni.  

Per muovere una libreria da una parte all'alltra si può usare il comando:

```bash
nx g @nrwl/workspace:move --project my-lib --destination shared/my-lib
```

Per rimuovere una libreria si usa:

```bash
nx g @nrwl/workspace:remove  my-lib
```

## Creazione di una libreria
Per creare una libreria si usa:

```bash
nx g @nx/angular:library dogs-domain
nx g @nx/angular:library libs/shared/util-environment
```
Oppure si specifica proprio il path che verrà utilizzato come alias dentro il tsconfig.base.json:

```bash
nx g @nx/angular:library shared/util-environment --importPath=@dogs/shared/util-environment

## nell'app utilizzatrice
# import {environment} from '@dogs/shared/util-environment';
```
Importantissimo: ogni library deve avere un file `index.ts` **che espone tutto ciò che si vuole rendere pubblico**. Ad esempio:

```typescript
// libs/dogs-domain/src/index.ts
export * from './lib/services/dogs.service';
export * from './lib/models/dog.model';
```
## 8. Affeted
Nx permette di eseguire comandi **solo sui progetti che sono stati modificati (affected) rispetto ad un branch di riferimento** (ad esempio main/master). Questo è molto utile in un monorepo con molte applicazioni e librerie per ridurre i tempi di build e test. Si usa:

```bash
nx affected -t lint
nx graph --affected 
```
Il comando sopra mostra il grafo delle dipendenze dei progetti che sono stati modificati rispetto al branch di riferimento (di default main/master). 

# 9. Buildable Libraries
Per creare librerie che possono essere buildate e pubblicate su npm (e consumate fuori da un nx workspace) si usa:
```bash
# Pubblicabile (come la --buildable ma con la possibilità di essere pubblicabile su npm o verdaccio etc)
nx g @nx/angular:library dogs-ui --publishable 

# simile a pubblicabile in quanto puà essere buildata
# ma supporta incremental build ed il meccanismo di cache di MX 
nx g @nx/angular:library dogs-ui --buildable 
```
Il buildable flag segna la libreria come buildable e crea un target di build nel file `project.json` della libreria. Per buildare la libreria si usa:

# 9. Formatting the workspace
Nx viene fornito con il supporto per la formattazione del codice utilizzando Prettier. Per formattare l'intero workspace si usa:

```bash 
nx format:check # per controllare i file non formattati correttamente
nx format:write # per formattare i file (ancora non formattati correttamente)
```
il file di configurazione di Prettier è `.prettierrc` nella root del workspace mentre `.prettierignore` contiene i file da ignorare.

# 10. Linting
Nx supporta vari linter come ESLint, TSLint (deprecato) e Stylelint. Per lintare un progetto si usa:

```bash
nx lint <project-name> --fix
```

# 11. Updating an NX Workspace
Per aggiornare un workspace Nx alla versione più recente si hanno principalmente due steps:
- 1. aggiornare il package.json alla versione più recente di Nx (ma non installa le dipendenze attraverso `npm install`)
- 2. creare un file migrations.json che contiene le migrazioni da eseguire per aggiornare il workspace alla nuova versione di Nx.
- 3. eseguire le migrazioni

Il comando successivo inizia e guida l'utente attraverso il processo di migrazione:
```bash
npx nx migrate latest
``` 

mentre per eseguire le migrazioni si usa:

```bash
npx nx migrate --run-migrations
```

E' importante notare che bisogna gestire manualmente tutte le third party dependencies che vengono usate (ad esempio `@angular/core`, `rxjs` etc) che non vengono aggiornate automaticamente da Nx. E' cruciale capire che le versioni di Nx e di Angular sono indipendenti le une dall'altre ma considerare sempre la "Nx and Angular Version Compatibility Matrix".

Esempio di migrazione da A19 a A20 e Nx 20 a Nx 21:
```bash
nx migrate latest # per migrare all'ultima versione creando un migrations.json
nx migrate --run-migrations # per eseguire le migrazioni e installare le dipendenze
npm install # per ultime versioni
```

# 12. E2E Testing
Per eseguire i test end to end si usa:

```bash
nx e2e <project-name>-e2e 
```

# 13. Plugins
Nx supporta vari [plugins](https://nx.dev/docs/plugin-registry) per estendere le funzionalità del workspace. Alcuni plugin popolari includono:
- @nx/angular: per applicazioni e librerie Angular
- @nx/react: per applicazioni e librerie React
- @nx/node: per applicazioni e librerie Node.js
- @nx/express: per applicazioni Express.js
- storybook: per integrare Storybook nel workspace
- jest: per integrare Jest come test runner
- cypress: per integrare Cypress per i test end to end





# Links

- [Github corso](https://github.com/FabianGosebrink/pluralsight-angular-monorepos-with-nx-2025)
- [Monorepo.tools](https://monorepo.tools)
- [@nx/angular](https://nx.dev/docs/technologies/angular/introduction)
- [Angular Monorepo](https://nx.dev/docs/getting-started/tutorials/angular-monorepo-tutorial)

- [Nx recipes](https://nx.dev/recipes)
- [Nx Github recipes](https://github.com/nrwl/nx-recipes)

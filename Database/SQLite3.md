MongoDB ATLAS (MongoDB in the Cloud):
https://cloud.mongodb.com/v2/67d1b00aecc12a0576998be3#/overview

Tutorial:
https://www.freecodecamp.org/news/how-to-build-a-replit-clone-with-socketio-monaco-editor-and-copilotkit/

Node + ATLAS
https://github.com/mongodb-university/atlas_starter_nodejs/blob/master/app.js

Username: inglorenzocorbella 
Password: eYQKU5N9BvVIkVo2

npm install mongodb


const { MongoClient, ServerApiVersion } = require('mongodb');
const uri = "mongodb+srv://inglorenzocorbella:<db_password>@cluster0.peifq.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0";

// Create a MongoClient with a MongoClientOptions object to set the Stable API version
const client = new MongoClient(uri, {
  serverApi: {
    version: ServerApiVersion.v1,
    strict: true,
    deprecationErrors: true,
  }
});

async function run() {
  try {
    // Connect the client to the server	(optional starting in v4.7)
    await client.connect();
    // Send a ping to confirm a successful connection
    await client.db("admin").command({ ping: 1 });
    console.log("Pinged your deployment. You successfully connected to MongoDB!");
  } finally {
    // Ensures that the client will close when you finish/error
    await client.close();
  }
}
run().catch(console.dir);


SQLite3
- https://blog.logrocket.com/using-built-in-sqlite-module-node-js/
- https://nodejs.org/api/sqlite.html#sqlite
- https://github.com/WiseLibs/better-sqlite3
- https://dextrop.medium.com/how-to-build-a-fast-and-lightweight-api-with-node-js-and-sqlite-676cbbec1b6a
- Up and Running with SQLite3 in a NodeJS API: https://www.youtube.com/watch?v=_RtpUaBSie0
- https://www.freecodecamp.org/news/create-desktop-apps-with-electron-react-and-typescript/


Sei un software engineer esperto nello sviluppo di applicazioni con node.js e tecnologie front end. Il tuo compito è guidarmi nello sviluppo di una applicazione di chat per gestire le conversazioni con modelli LLM in locale tramite il software Ollama. 

1. Funzionalità dell'applicazione:
L'applicazione deve consentire ad un unico utente, appassionato di modelli AI e loro utilizzo in locale, di:
- creare conversazioni tra l'utente e assistenti AI inviando dei messaggi ad una rest pi che comunica con ollama. La chat deve gestire le conversazioni come liste di messaggi tra l'utente e le relative risposte degli assistenti AI.
- in ciascuna conversazione dovrà essere possible editare i messaggi ricevuti in risposta dagli assistenti AI ed esportarli in markdown. 
- editare i parametri della conversazione (modello scelto tra i modelli disponibili in ollama, temperature, system prompt)
- cancellare conversazioni
- l'applicazione è una unica pagina con a sx una sidebar con la lista delle conversazioni, ed in grassetto la coversazione selezionata e a dx il corpo della pagina con i messaggi dell'utente allineati a DX e quelli dell'assistente AI allineati a SX.
- Nella parte bassa della sidebar ci deve essere un bottone "Settings" che una volta premuto deve presentare una modale con i seguenti parametri di configurazione: 1. ollama server url 2. default ollama model (quello ci peso minore) 3. default ollama vision model 4 (il primo di tipo vision recuperando la lista di modelli diponibili). scelta tema (chiaro, scuro, Sistema) 5. un bottone per cancellare tutte le conversazioni.
- in alto a sx della pagina ci deve essere un bottone per poter motrare la sidebar o nasconderla con una animazione fluida.
- in alto a dx della pagina ci deve essere un bottone per aprire una sidebar sulla DX della pagina di configurazione dei parametri della conversazione corrente (modello LLM scelto tra i disponibili in ollama, temperature, system prompt e altri parametri previsti da ollama) 
- I messaggi dell'utente se contenente del codice markdown devono essere formattati in modo che le porzioni di codice, le tabelle e tutti gli altri elementi grafici vengano opportunamente stilizzati e formattati.
- l'applicazione non deve usare nessuna libreria css, NON utilizzare tailwind, ma deve avere un design moderno, accattivante  e coerente su tutti gli elementi grafici. L'interfaccia grafica deve prevedere due temi (chiaro e scuro con default a chiaro).

2. Tecnologie e vincoli
- L'applicazione deve prevedere un front end in React.js e Typescript realizzato con Vite 
- Un backednd in node + express.js che chiama il server di ollama.
- L'applicazione deve conservare in un db locale, tramite SQLite3 le conversazioni tra l'utente e gli assistenti AI.

Spiega passo passo come arrivare a tale risultato. Sto partendo da zero quindi devi piegarmi ogni passaggio necessario per configurare e sviluppare l'applicazione.



please use my two mcps for the following things:
1. sequential thinking mcp - for planning each step and ensuring we are completely this process to its maximum
2. context7 -during research and also before you implement any new third party API or change to the structure of the project or anything, you must first read the up to date documentation on that thing and you must always check the documentation as thimgs may have changed since you were trained.





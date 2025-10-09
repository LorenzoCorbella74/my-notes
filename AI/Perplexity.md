# Perplexity

Ogni tool ha una sua funzione:
- `ChatGPT` è ottimo per generare testi, 
- `Claude` gestisce le analisi complesse e il ragionamento
- `Gemini` è integrato nell'ecosistema google con un large context window
-  Perplexity è un **answer engine** progettato per rispondere a domande specifiche superando le ricerche grezze di Google (che tornano links e rankings) con un approccio research-focued che combina `ricerche in real time`, `citazione di fonti` e `accesso alle ultime versioni dei migliori Modelli AI` di fatto **combinando reasoning + real time data** (non è solo cercare dati ma pensare attraverso il problema posto sistematicamente).

L'architettura è basata su:
- Language model processing
- real time web search
- synthesis process

Le sue funzionalità principali includono:
- `Deep research`: risposte dettagliate con citazioni di fonti
- Content creation (industry report)
- Productivity & Collaboration: conoscenza condivisa negli `Spaces` (spazi per organizzare le ricerche `Threads` by topic or project econ la possibilità di condividerle creando collaborazioni)
- API access

`Threads` è dove si ceercano le risposte mentre `Pages` sono le pagine che vengono formattatae per essere condivise con l'esterno

All'interno degli `Spaces`, per definire un contesto specifico, si può:
- `aggiungere file`
- `aggiungere link`
- `aggiungere istruzioni` in cui si può specificare il system prompt per personalizzare come le risposte sono focalizzate e strutturate.



La `Discover` section è una raccolta di ricerche e news su vari argomenti curate su vari argomenti.


## Ricerche
All'interno della ricerca è possibile
- aggiungere Local files e Connect Files (Google Drive,  Dropbox) per fare ricerche anche all'interno dei propri documenti.
- dettare o scrivere la domanda
- scegliere tra gli ultimi modelli AI (Sonar di Perplexity è il default altrimenti GPT-4, Claude 4, Gemini 2.5 Pro, etc...). Il default mode scegli il modello automaticamente in base alla domanda.
- Source filters: per filtrare le fonti (web/news, Academic, Social, Finance)

Ci sono tre modalità di ricerca:
- `Search`: risposte brevi con citazioni a everyday question
- `Deep research`: risposte più lunghe e dettagliate con più sources, cross referencing, grafici e reasoning avanzato
- `Labs`: (Solo PRO) per generare ed eseguire codice (es. apps, dashboard...)

Alla fine delle ricerche sono disponibili Le `Related` searches per approfondire l'argomento, `Sources` e `Steps` e bottoni per:
- copiare il testo
- salvare la ricerca in uno Space
- salvare la ricerca in .md .pdf .docx
- condividere
- riscrivere la risposta

## Prompting
- La qualità del prompt impatta molto la qualità della risposta, l'importante è essere specifici e chiari!
- Si può chiedere di rispondere in un certo modo (es. come un esperto, in modo sintetico, con esempi, etc...)


## Strategic model selection
I seguenti modelli approcciano il problema in modo diverso e producono risposte diverse con output style diversi:
- GPT: comprehennsive analysis with systematic methodology (Business Analysts)
- Claude: Deep reasoning for strategic consultancy with balanced perspectives with contextua considerations (Startegic consultants)
- Gemini: creative synthesis with innovative presentation (creative Professionals)
- Sonar: real time analysis with up-to-date information (Researchers and journalists) market resarchers policy analysts and strategic planners
- O3: multi step reasoning and strategic problem solving scenarios
- Grok 4: creative solutions and problem solving


## Comet 
Il browser comet trasforma ricerche eseguite dall'AI in flussi scalabili automatizzati offrendo invece di una navigazione passiva un'esperienza attiva e produttiva dove l'Ai diventa un research assistant che capisce i contenuti web automaticamente capace di estrarre informazioni significative senza sforzo manuale.
I vantaggi dell'AI native approach includono:
- context understanding
- automated extraction
- pattern recognition
- scalability consistency
E' possibile eseguire delle Web Actions (filling forms, scheduling calendar events) e Workflow automation


# API Integration
Perplexity offre un'API che consente agli sviluppatori di integrare le funzionalità di Perplexity nelle loro applicazioni, andando oltre l'UI di Perplexity. L'utilizzo delle API permette:
- custom workflows
- recurring research avoiding manual task repetion
- business integration with proprietary tools
- specialized applications
# Marcatory nei prompt
L'utilizzo dei marcatori serve per far vedere qualcosa di specifico al modello LLM. Questi riconoscono dei pattern riconoscibili, strutturati.

Esistono marcatori di:
- guida tra cui marcatori di confine(o delimitatori), etichette, di attenzione, di memoria (o coerenza)
- template

1) Marcatori di confine sono """ oppure ---, ###, \\\ ed indicano l'inizio e la fine di un blocco. Servono per indicare "qui finisce una cosa, qui ne inizia un 'altra". Ad esempio:

###
testo da non toccare
###

***section start***
qui istruzioni
***section end***

La cosa importante è che siano coerenti all'interno del prompt (non mischiare + stili).
Per strutturare un prompt a "capitoli" si potrebbe utilizzare la sintassi markdon con #titolo1, ##titolo2, 1. per le sezioni etc
Per richiamare una sezione precedente indicare ## 1. per linkarsi

2) Marcatori di etichette
Servono per richiamare una parte, ossia "questa parte si chiama così, posso richiamarla + avanti".
Esempi sono:
<nom_elemento>
{nome}
!!nome!!
NOME

Esempio:
Nel Prompt inseriamo l'etichetta in maiuscolo: Il BUDGET massimo è .......
Se vogliamo richiamare l'etichetta budget: Inserisci BUDGET quando proponi le soluzioni

3. Marcatori di attenzione
Evidenziano al LLM delle parti informazioni (informazioni o istruzioni). Non seguono regole ben definite. Vediamo un esempio

IMPORTANTE: .... teto da evidenziare nel modello...
AVOID:
REMEMBER:
emoji:

In genere evitare la versione negative: meglio AVOID: che NOT DO:

4. Marcatori di memoria o di coerenza
Aiutano il prompt a mantenere la coerenza o a tenere vivo il comportamento anche per più risposte (quindi da system prompt). Se ne possono mettere più di uno ad altezze diverse del prompt. E' come dire, questa regola vale sempre, non dimenticarla mai. Da usare se il modello salta dei pezzi. Un esempio tipico è:

#always wait for my reply(#)
#rispondi in {lingua utente}


5. Marcatori di template
Permettono di inserire delle variabili ("Segui la struttura ed inserisci ciò che ti segnalo")
Facciamo un esempio di prompt, le parti tra {} identificano una variabile (non ne possiamo mettere troppe!!):
- Devi fare il compito X e ogni volta che devi fare X lo motivi {inventa tu motivazione}
- Il tuo compito è di rispondere a {necessità utente}

CORSO:
https://egghead.io/courses/scripting-local-language-models-with-ollama-and-the-vercel-ai-sdk~gmi9k
> pnpm i ollama-provider ai zod

// index.ts
import {ollama} from "ollama-ai-provider"
import {generateObject} from "ai"
import {z} from "zod"

const model = ollama('gemma:latest');
const prompt = concatenate all the markdown files in my Downloads folder into a single file.";
const {object} = await generateObject({
	model,
	schema: z.object({command:z.string().describe("The command to execute, e.g., 'summarize' or 'translate' ")})
	prompt
})

console.log(object)

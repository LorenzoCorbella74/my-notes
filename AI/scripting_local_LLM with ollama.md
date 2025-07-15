# Scripting Local Language Models with Ollama and the Vercel AI SDK

Installare le dipendenze: 
```bash
pnpm i ollama-provider ai zod
```
Mettere all'interno di un file `commands/summarize.md` il testo markdown (che di fatto costituirà le istruzioni per svolgere una certa azione):
```markdown
Please summarize the following content to a single sentence.
```
ed all'interno del file `commands/translate.md` il contenuto:
```markdown
Please translate the following content into French
```

Si fa partire con:
```bash
> bun index.ts "create a summary of my package file."
```

```typescript
// index.ts 
import {ollama} from "ollama-ai-provider";
import {generateObject, generateText} from "ai"; 
import {z} from "zod";
import {globby} from "globby"
import {parseArgs} from "node:util"
import {readFile} from "node:fs/promises"
import path from "node:path"

/* 
    all'inizio si caricano tutti i file per valorizzare l'enum 
    di tutti e soli file su cui si può agire con i diversi comandi/azioni
*/
const files = await globby("**/*.md",{
    gitignore:true
});

const isNotEmpty = (files:string[]):files is [string,...string[]]=> {
    return files.length>0;
}
if(!isNotEmpty(files)){
    console.log("No files found")
    process.exit(1)
}

// scanning params passes as inputs
const {positionals} = parseArgs({
    allowPositionals:true,
})

const model = ollama('gemma:latest'); 
// si carica la commands directory
const commandsFilePaths = await globby("commands/*.md",{
    gitignore:true
});

// map the command name to the file path
const commandMap = new Map<string,string>()
for (const commandFilePath of commandFilePaths){
    const commandName= path.parse(commandFilePath).name; // name del file senza estensione
    const commandContent = await readFile(commandFilePath,"utf-8");
    commandMap.set(commandName, commandContent)
}

const prompt = positionals[0]; 

const {object} = await generateObject({ 
    model, 
    schema: z.object({
        command:z.string().describe(`The command to execute, e.g., ${Array.from(commandMap.keys().join(", "))}`),
        filepath: z.enum(files).describe("The path to the file to process")
    }) 
    prompt 
});

const content = await readFile(object.filepath, "utf-8");

/* 
    si deve gestire i comandi NON trovati 
    e il sequenzial thinking (cioè prima nella fase di reasoning
    si deve definire i passi e poi si devono eseguire in ordine)    
*/

const textPrompt = `
<command>
${object.command}
</command>

<instructions>
${commandMap.get(object.command)}
</instructions>

<content>
${content}
</content>
`;

const {text} = await generateText({
    model, 
    prompt:textPrompt
})

console.log(text);
```


## Links
- [Corso Scripting Local Language Models with Ollama and the Vercel AI SDK](https://egghead.io/lessons/dynamically-inferring-file-types-from-user-prompts-in-local-ai-scripts~wljfl)
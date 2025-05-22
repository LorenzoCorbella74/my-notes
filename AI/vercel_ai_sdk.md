# [Vercel AI SDK](https://ai-sdk.dev/docs/introduction)

E' un SDK per costruire applicazioni AI in modo semplice e veloce. Permette di integrare modelli di intelligenza artificiale nelle proprie applicazioni uniformando l'API di utilizzo per diversi [providers](https://ai-sdk.dev/docs/foundations/providers-and-models).


## Indice

- [Introduzione](#vercel-ai-sdk)
- [Esempi di utilizzo](#esempi-di-utilizzo)
  - [generateText](#utilizzo-di-generatetext)
  - [streamText](#utilizzo-di-streamtext)
- [Structured output](#structured-output)
- [Generazione ENUM](#generazione-enum)
- [Generazione di array](#generazione-di-array)
- [Descrizione di immagini](#descrizione-di-immagini)
- [Analisi di documenti PDF](#analisi-di-documenti-pdf)
- [Embeddings](#embeddings)
- [Tool Calling](#tool-calling)
- [Uso di provider locali](#uso-di-provider-locali)
- [UI](#ui)
- [Node.js & TypeScript](#nodejs--typescript)
- [Integrazione MCP](#integrazione-mcp)
- [Links](#links)


## Esempi di utilizzo
Notare l'utilizzo dinamico dei modelli (basta cambiare la variabile `model`):

1. Utilizzo di `generateText`
```ts
import { createOpenAICompatible } from "@ai-sdk/openai-compatible";
import { anthropic } from "@ai-sdk/anthropic";
import { generateText } from "ai";

const model = anthropic("claude-3-5-haiku-latest"); // oppure openai("gpt-4o-mini-2024-07-18")
/* const model = createOpenAICompatible({
  name: "lmstudio",
  baseURL: `http://${getLocalhost()}:1234/v1`,
}); */

// con prompt normale
export const answerMyQuestion = async (prompt: string) => {
  const { text } = await generateText({ model, prompt});
  return text;
};

const answer = await answerMyQuestion("what is the chemical formula for dihydrogen monoxide?");
console.log(answer); // "H2O"

// con System prompt
export const summarizeText = async (input: string) => {
  const { text } = await generateText({
    model,
    prompt: input,
    system:
      `You are a text summarizer. ` +
      `Summarize the text you receive. ` +
      `Be concise. ` +
      `Return only the summary. ` +
      `Do not use the phrase "here is a summary". ` +
      `Highlight relevant phrases in bold. ` +
      `The summary should be two sentences long. `,
      maxRetries: 0, // Error on first failure to show network issues early
  });

  return text;
};
const text = readFileSync(path.join(import.meta.dirname,"my-file.md"),"utf-8");
const summary = await summarizeText(text);

// con messages
export const summarizeText = async (input: string) => {
  const { text } = await generateText({
    model,
    messages: [
      { role: "system", content:`You are a text summarizer.`},
      { role: "user", content: input },
    ],
  });
  return text;
};
```
2. Utilizzo di streamText
```ts
export const answerMyQuestion = async (prompt: string) => {
  const { textStream } = streamText({ model, prompt});

  // The textStream is an AsyncIterable, so it can be
  // iterated over like an array.
  for await (const text of textStream) {
    process.stdout.write(text);
  }

  return textStream;
};
```

## Structured output
Si aggiunge lo `schema` di output per generare un oggetto strutturato. In questo caso viene generata una ricetta a partire da un prompt.
```ts
import { generateObject } from "ai";
import { z } from "zod"

const model = anthropic("claude-3-5-haiku-latest"); // oppure openai("gpt-4o-mini-2024-07-18")

const schema = z.object({
  recipe: z.object({
    name: z.string().describe("The title of the recipe"),
    ingredients: z
      .array(
        z.object({
          name: z.string(),
          amount: z.string(),
        }),
      )
      .describe("The ingredients needed for the recipe"),
    steps: z
      .array(z.string())
      .describe("The steps to make the recipe"),
  }),
});

export const createRecipe = async (prompt: string) => {
  const { object } = await generateObject({
    model,
    schema,
    prompt,
    schemaName: "Recipe",
    system:
      `You are helping a user create a recipe. ` +
      `Use British English variants of ingredient names,` +
      `like Coriander over Cilantro.`,
  });

  return object.recipe;
};

const recipe = await createRecipe("How to make baba ganoush?");
console.dir(recipe, { depth: null });

// oppure come stream
export const createRecipe = async (prompt: string) => {
  const result = await streamObject({
    model,
    system:
      `You are helping a user create a recipe. ` +
      `Use British English variants of ingredient names,` +
      `like Coriander over Cilantro.`,
    schemaName: "Recipe",
    schema,
    prompt,
  });

  for await (const obj of result.partialObjectStream) {
    console.clear();
    console.dir(obj, { depth: null });
  }

  const finalObject = await result.object;

  return finalObject.recipe;
};
await createRecipe("How to make hummus?");
```

## Generazione ENUM
Si specifica la proprieta `enum` per generare un oggetto di tipo enum. In questo caso viene classificato il sentiment di un testo.
```ts
export const classifySentiment = async (
  text: string,
) => {
  const { object } = await generateObject({
    model,
    output: "enum",
    enum: ["positive", "negative", "neutral"],
    prompt: text,
    system:
      `Classify the sentiment of the text as either ` +
      `positive, negative, or neutral.`,
  });

  return object;
};
const result = await classifySentiment(`This is terrible`);
console.log(result); // "negative"
```

## Generazione di array
Si utilizza la funzione `generateObject` e lo schema. In questo caso viene generata una lista di ingredienti.
```ts
import { z } from "zod";
import { generateObject } from "ai";
import { smallAnthropicModel } from "../../_shared/models.ts";

const schema = z.object({
  name: z.string().describe("The name of the user"),
  age: z.number().describe("The user's age"),
  email: z.string().email().describe("The user's email address, @example.com",),
});

export const createFakeUsers = async (
  input: string,
) => {
  const { object } = await generateObject({
    model: smallAnthropicModel,
    prompt: input,
    system: `You are generating fake user data.`,
    output: "array",
    schema,
  });

  return object;
};

const fakeUsers = await createFakeUsers(
  "Generate 5 fake users from the UK.",
);
console.dir(fakeUsers, { depth: null });
```
## Descrizione di immagini
Sispecifica nel content `image` per descrivere le immagini passando l'url:
```ts
import { generateText } from "ai";

const model = (modello con vision);

const systemPrompt =
  `You will receive an image. ` +
  `Please create an alt text for the image. ` +
  `Be concise. ` +
  `Use adjectives only when necessary. ` +
  `Do not pass 160 characters. ` +
  `Use simple language. `;

export const describeImage = async (imageUrl: string) => {
  const { text } = await generateText({
    model,
    system: systemPrompt,
    messages: [
      {
        role: "user",
        content: [
          { type: "image", image: new URL(imageUrl)},
        ],
      },
    ],
  });

  return text;
};

const description = await describeImage("https://github.com/ai-hero-dev/ai-hero/blob/main/internal/assets/fireworks.jpg?raw=true");
console.log(description); // "A close-up of a firework explosion in the night sky, with vibrant colors and sparks radiating outward."
```
## Analisi di documenti PDF
Si utilizza un oggetto strutturato per analizzare i documenti PDF e si specifica il `mimeType` ed il `type` del file ed il `data`.
```ts
import { z } from "zod";
import { generateObject } from "ai";
import { readFileSync } from "fs";
import path from "path";

const schema = z
  .object({
    total: z.number().describe("The total amount of the invoice."),
    currency: z.string().describe("The currency of the total amount."),
    invoiceNumber: z.string().describe("The invoice number."),
    companyAddress: z.string().describe(  "The address of the company or person issuing the invoice.",),
  })
  .describe("The extracted data from the invoice.");

export const extractDataFromInvoice = async (
  invoicePath: string,
) => {
  const { object } = await generateObject({
    model,
    system:
      `You will receive an invoice. ` +
      `Please extract the data from the invoice.`,
    schema,
    messages: [
      {
        role: "user",
        content: [
          {
            type: "file",
            data: readFileSync(invoicePath),
            mimeType: "application/pdf",
          },
        ],
      },
    ],
  });

  return object;
};

const result = await extractDataFromInvoice(path.join(import.meta.dirname, "./invoice-1.pdf"));
console.dir(result, { depth: null });
// { total: 100, currency: "USD", invoiceNumber: "12345", companyAddress: "123 Main St, Anytown, USA" }
```

## Embeddings
```ts
import { lmstudio } from "../../_shared/models.ts";
import {
  embedMany,
  embed,
  cosineSimilarity,
} from "ai";

export const localModel =
  lmstudio.textEmbeddingModel("");

const values = ["Dog", "Cat", "Car", "Bike"];

const { embeddings } = await embedMany({
  model: localModel,
  values,
});

const vectorDatabase = embeddings.map(
  (embedding, index) => ({
    value: values[index]!,
    embedding,
  }),
);

const searchTerm = await embed({
  model: localModel,
  value: "Pedal",
});

const entries = vectorDatabase.map((entry) => {
  return {
    value: entry.value,
    similarity: cosineSimilarity(
      entry.embedding,
      searchTerm.embedding,
    ),
  };
});

const sortedEntries = entries.sort(
  (a, b) => b.similarity - a.similarity,
);

console.dir(sortedEntries, { depth: null });
```

## Tool Calling
Si aggiungono i `tools` per eseguire azioni specifiche:
```ts
import { tool } from "ai";
import { z } from "zod";
import { generateText } from "ai";

const model = modelWIthToolCalling;

const logToConsoleTool = tool({
  description: "Log a message to the console",
  parameters: z.object({
    message: z.string().describe("The message to log to the console"),
  }),
  execute: async ({ message }) => {
    console.log(message);
  },
});

const logToConsole = async (prompt: string) => {
  const { steps } = await generateText({
    model,
    prompt,
    system:
      `Your only role in life is to log messages to the console. ` +
      `Use the tool provided to log the prompt to the console.`,
    tools: {
      logToConsole: logToConsoleTool,
    },
  });
  console.dir(steps[0]?.toolCalls, { depth: null });
};
await logToConsole("Hello world!");
```
Una volta che il modello specifica che si devono eseguire delle funzioni si deve poi eseguirle e ripassarle al modello per produrre la risopsta finale:
Si specifica la proprietà `maxSteps` per limitare il numero di passi.
```ts
import { streamText, tool } from "ai";
import { z } from "zod";
import { smallToolCallingModel } from "../../_shared/models.ts";

const model = smallToolCallingModel;

const getWeatherTool = tool({
  description:
    "Get the current weather in the specified city",
  parameters: z.object({
    city: z
      .string()
      .describe("The city to get the weather for"),
  }),
  execute: async ({ city }) => {
    return `The weather in ${city} is 25°C and sunny.`;
  },
});

const askAQuestion = async (prompt: string) => {
  const { textStream } = await streamText({
    model,
    prompt,
    tools: {
      getWeather: getWeatherTool,
    },
    maxSteps: 2,
  });

  for await (const text of textStream) {
    process.stdout.write(text);
  }
};

await askAQuestion(`What's the weather in London?`);
```
Altro esempio di utilizzo:
```ts
import { generateText, tool } from "ai";
import { z } from "zod";
import { smallModel } from "../../_shared/models.ts";

const model = smallModel;

const systemPrompt =
  `You are interacting with the Star Wars API. ` +
  `Use the tools provided to fetch data from the API. ` +
  `Make a plan to find the data, then enact that plan step-by-step. ` +
  `If you cannot find a record in the Star Wars API, use the index pages to help: ` +
  `
    <index_pages>

    vehicles: https://swapi.py4e.com/api/vehicles/
    planets: https://swapi.py4e.com/api/planets/
    films: https://swapi.py4e.com/api/films/
    people: https://swapi.py4e.com/api/people/

    </index_pages>
  `;

export const askSwapiApi = async (prompt: string) => {
  // 1. Call the Vercel SDK, passing in our model (gpt-4o-mini)
  // and the system prompt (next image)
  const { steps, text } = await generateText({
    model,
    system: systemPrompt,
    prompt,
    tools: {
      // 2. We pass in a tool that calls the Star Wars API
      callSwapiApi: tool({
        description: "Call the Star Wars API",
        execute: async ({ endpoint }) => {
          const response = await fetch(endpoint);
          return response.json();
        },
        parameters: z.object({
          endpoint: z
            .string()
            .url()
            .startsWith("https://swapi.py4e.com/api/")
            .describe(
              "The URL to fetch data from, " +
                "such as https://swapi.py4e.com/api/films/",
            ),
        }),
      }),
    },
    // 3. maxSteps makes the model perform multiple
    // cycles, allowing it to interact with the tool
    // multiple times and fetch more data
    maxSteps: 10,
  });

  return { steps, text };
};
const { steps, text } = await askSwapiApi(
  "What is the name of the planet that Luke Skywalker is from?",
);
console.log(text); // "The planet that Luke Skywalker is from is Tatooine."
console.dir(steps, { depth: null });
```

## Uso di provider locali
Per ollama c'è un [provider custom](https://github.com/sgomez/ollama-ai-provider) installabile tramite `npm i ollama-ai-provider` e si utilizza in questo modo:
```ts
import { createOllama } from 'ollama-ai-provider';

const model = createOllama({
  baseURL: 'http://localhost:11434/api',
});

const result = streamText({
  model: model('nemotron-mini'),
  messages,
});

for await (const delta of result.textStream) {
  process.stdout.write(delta);
}
```

## UI
E' possibile utilizzare la UI di Vercel AI SDK per costruire applicazioni AI in modo semplice e veloce con react:
```tsx
'use client';

import { useChat } from 'ai/react';

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit } = useChat();
  return (
    <div className="flex flex-col w-full max-w-md py-24 mx-auto stretch">
      {messages.map(m => (
        <div key={m.id} className="whitespace-pre-wrap">
          {m.role === 'user' ? 'User: ' : 'AI: '}
          {m.content}
        </div>
      ))}

      <form onSubmit={handleSubmit}>
        <input
          className="fixed bottom-0 w-full max-w-md p-2 mb-8 border border-gray-300 rounded shadow-xl"
          value={input}
          placeholder="Say something..."
          onChange={handleInputChange}
        />
      </form>
    </div>
  );
}
```

## Node.js & TypeScript
Per inizializzare un progetto con node & typescript vedere la [pagina](https://ai-sdk.dev/docs/getting-started/nodejs) con la chat di esempio:
```ts
// index.ts 
// npm tsx index.ts
import { openai } from '@ai-sdk/openai';
import { CoreMessage, streamText } from 'ai';
import dotenv from 'dotenv';
import * as readline from 'node:readline/promises';

dotenv.config();

const terminal = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

const messages: CoreMessage[] = [];

async function main() {
  while (true) {
    const userInput = await terminal.question('You: ');

    messages.push({ role: 'user', content: userInput });

    const result = streamText({
      model: openai('gpt-4o'),
      messages,
    });

    let fullResponse = '';
    process.stdout.write('\nAssistant: ');
    for await (const delta of result.textStream) {
      fullResponse += delta;
      process.stdout.write(delta);
    }
    process.stdout.write('\n\n');

    messages.push({ role: 'assistant', content: fullResponse });
  }
}

main().catch(console.error);
```

## Integrazione MCP
E' possibile creare un [client MCP](https://ai-sdk.dev/docs/reference/ai-sdk-core/create-mcp-client) per utilizzare il Vercel AI SDK in questo modo:
```ts
import { experimental_createMCPClient } from "ai"

let mcpClient;
  try {
    mcpClient = await experimental_createMCPClient({
      transport: {
        type: "sse",
        url: "http://localhost:8083/sse",
        headers: {
          example: "header",
        },
      },
    });

    const toolset = await mcpClient.tools();    

    // Generate Answer
    const { text: answer } = await generateText({
      model: groq("qwen-2.5-32b"), 
      tools: toolset,
      maxSteps: 10,
      onStepFinish: async ({ toolResults }) => {
        console.log(`\nSTEP RESULTS: ${JSON.stringify(toolResults, null, 2)}`);
      },
      system: SYSTEM_PROMPT,
      prompt: question,
      //temperature: 1.5,
    });
      console.log("FINAL ANSWER:", answer);
    }
  } catch (error) {
    console.error("Error creating MCP client:", error);
  }
```
Vedere i seguenti articoli:
- [Building Powerful MCP SSE Tool](https://mychen76.medium.com/building-powerful-mcp-sse-tool-enable-ai-assistant-f94f401d2178) and [code](https://github.com/minyang-chen/customer-order-mcp-ai/blob/main/mcp-store-client/sse/client.ts)
- [Connecting to mcp servers with ai sdk](https://blog.marcnuri.com/connecting-to-mcp-server-with-ai-sdk)

# Links
- [Vercel AI SDK](https://ai-sdk.dev/)
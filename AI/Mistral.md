# Mistral AI
Mistral è una società che produce "foundational models" che nel 2023 è appasa sul mercato competendo con OpenAI. Ha un [chat assistant](https://chat.mistral.ai/chat) chiamato Lucia che permette di interagire con il modello. Si può interagire con il modello anche tramite - API Endopoint (bisogna prendere una API KEY).
- Cloud services (che forniscono un'infrastruttura.scalabile e sicura per eseguire modelli di IA)
- Infrastrutture on premise

## Indice
- Eseguire modelli localmente
- API e JS SDK
- Modelli
- Embeddings
- Vector databases (per dare alle app il così detto DOMAIN KNOLEDGE, come dati proprietari e real time information per cui il modello non è in precedenza stato allenato o informazioni extra )
- Retrieval augmented generation (RAG)
- AI Agents with function calling che permettono alle app di prendere azioni in base all'user prompt


Per poter utilizzare l'API bisogna recuperare una [API keys](https://console.mistral.ai/api-keys/)

## Chat completion
Per utilizzare il modello di chat completion si può eseguire il codice seguente:

```typescript
import MistralClient from '@mistralai/mistralai';

const apiKey = process.env.MISTRAL_API_KEY || 'your_api_key';
const client = new MistralClient(apiKey);

const chatResponse = await client.chat({
  model: 'mistral-tiny',
  messages: [{ role: 'user', content: 'What is the best French cheese?' }],
  temperature: 1 // livello di creatività del modello (0 è il più deterministico, 1 è il più creativo, 0.7 è il default)
});

console.log(chatResponse.choices[0].message.content);
```

Da notare che per guidare il comportamento del modello si possono aggiungere dei "system prompts":
```typescript

const chatResponse = await client.chat({
    model: 'mistral-tiny',
  messages: [
      { 
        role: 'system', 
        content: 'sei un appassionato di formaggio e quando ti verrà richiesto di formaggi, rispondi concisamente e con humour' },
      { role: 'user', content: 'What is the best French cheese?' }
    ],
  temperature: 1,
});

```
Le opzioni prevedono di aggiungere il:
-  supporto per lo streaming. 

```typescript
const chatResponse = await client.chatStream({
    model: 'mistral-tiny',
  messages: [
      { 
        role: 'system', 
        content: 'sei un appassionato di formaggio e quando ti verrà richiesto di formaggi, rispondi concisamente e con humour' },
      { role: 'user', content: 'What is the best French cheese?' }
    ],
  temperature: 1,
});

for await (const chunk of chatResponse) {   
    console.log(chunk.choices[0].delta.content);
} 
```


## Hugging face
Una piattaforma ricca di modelli open source e dataset è [Hugging Face](https://huggingface.co/) su cui è possibile recuperare vari modelli di [mistral](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.1)


234 OK
272
357
360
362

288 assegna procedimento  #1064



Ecco entrambi gli esempi – **configurazione statica** e **scelta dinamica** – su come un agente può chiamare altri agenti in Agenite.

---

## 1. **Configurazione statica (flusso predefinito negli step)**

In questo caso, la scelta di quale agente chiamare è cablata negli step dell’agente. Un esempio:

```typescript
const agentA = new Agent({ name: 'A', ... });
const agentB = new Agent({
  name: 'B',
  agents: [agentA], // agentA è disponibile per essere chiamato
  steps: {
    'call-agentA': {
      name: 'call-agentA',
      async *execute(params, context) {
        // agentB chiama agentA in modo esplicito e statico
        const iterator = context.agent.agents[0].iterate(params, { parentExecution: context });
        let result = await iterator.next();
        while (!result.done) {
          yield result.value;
          result = await iterator.next();
        }
        return { next: 'end', ...result.value };
      }
    },
    'end': { /* ... */ }
  },
  startStep: 'call-agentA',
});
```
Qui, il flusso è fisso: agentB esegue sempre lo step che chiama agentA.

---

## 2. **Scelta dinamica (selezione in base alla richiesta utente)**

Questa modalità sfrutta le capacità dell’LLM: l’agente principale riceve la richiesta utente, analizza il messaggio (spesso con l’ausilio dell’LLM), e decide a runtime quale agente chiamare tra quelli disponibili nel suo array.

La logica centrale che esegue questa scelta si trova nello step built-in `agenite.agent-call`, come nel file [agent-call.ts](https://github.com/subeshb1/agenite/blob/main/packages/agent/src/steps/agent-call.ts):

```typescript
// Pseudocodice semplificato
execute: async function* ({ targetAgent, input }, executionContext) {
  // Cerca l'agente con il nome desiderato nell'array agents
  const targetAgentInstance = executionContext.agent.agentConfig.agents?.find(
    (agent) => agent.agentConfig.name === targetAgent
  );
  if (!targetAgentInstance) throw new Error(`Agent ${targetAgent} not found`);

  // Esegue l'agente selezionato passando il contesto annidato
  const result = yield* targetAgentInstance.iterate(
    { messages: input },
    { parentExecution: executionContext, context: executionContext.context }
  );

  // Restituisce il risultato al flusso principale
  return { next: 'agenite.llm-call', state: { ...result } };
}
```

**Come viene scelto il targetAgent?**
- L’LLM, in base all’input utente e alle istruzioni, decide quale agente chiamare e inserisce un blocco nella risposta (`toolUseBlock`) con il nome dell’agente desiderato.
- Lo step `agenite.agent-call` riceve questa informazione e chiama dinamicamente l’agente corrispondente.

### Esempio pratico multi-agent

```typescript
const mathAgent = new Agent({ name: 'math-specialist', ... });
const weatherAgent = new Agent({ name: 'weather-specialist', ... });

const coordinatorAgent = new Agent({
  name: 'coordinator',
  agents: [mathAgent, weatherAgent],
  instructions: `
    For math questions, delegate to math-specialist.
    For weather questions, delegate to weather-specialist.
    For complex questions, coordinate both agents.
  `,
  // Gli step possono essere quelli di default, inclusi quelli built-in come agent-call
});

// L’utente invia: "Qual è la radice quadrata della temperatura attuale a New York?"
// L’LLM capisce che serve prima la temperatura (weatherAgent), poi la radice quadrata (mathAgent)
// Lo step agent-call chiama dinamicamente i sub-agent in base alla richiesta
```

---

## **In breve**
- **Configurazione statica**: la chiamata all’agente è definita negli step e non cambia.
- **Scelta dinamica**: la decisione su quale agente chiamare avviene a runtime, in base alla richiesta utente e alla logica dell’LLM, tramite lo step `agenite.agent-call`.

# Supabase

## Index
- [Introduzione](#supabase)
- [Setup e configurazione](#setup-e-configurazione)
- [Query the DB](#query-the-db)
    - [Aggregate functions](#aggregate-functions)
    - [Realtime subscription](#realime-subscription)
    - [Insert](#insert)
- [Links](#links)

E' un alternativa open source a Firebase (cioè una soluzione di Backend as a Service), che offre una database infrastructure per le applicazioni web e mobili basata su PostgreSQL, ma anche autenticazione, storage, edge functions, vector embeddings  e API in tempo reale. Fornisce sia backend services che client side SDK in vari linguaggi (Javascript, flutter) per interagire con i servizi.

Si crea un app con vite e si crea un account su supabase tramite l'account github (definendo una organizzazione e la password). 
```bash
npm create vite@latest
```
All'interno della dashboard di supabase si avrà accesso ai progetti, e si può creare un nuovo progetto andando su "Table editor" e creando una tabella (abilitando il flag "Enable realtime"). Poi premendo "Insert" è possibile inserire i dati di una riga (con un id che si auto incrementa).

Poi si installa l'SDK `supabase-js`:
```bash
npm install @supabase/supabase-js
```
L'SDK semplifica l'interazione con il DB permettendo:
- ascolta le modifiche in tempo reale
- invoca deno edge functions
- gestisce file di grandi dimensioni
- gestisce l'autenticazione (login e user management)


Ha una ottima [documentazione](https://supabase.com/docs/reference/javascript/introduction) e permette di interagire in sicurezza (prevenendo attacchi di tipo SQL injection e ddos protection) salvaguardando le performance (con caching e connection pooling).

Si crea un client per interagire con il DB prendendo nella dashboard la chiave API e l'url del progetto (nella pagina Data API):
```javascript
import { createClient } from "@supabase/supabase-js";

const supabaseUrl = import.meta.env.VITE_SUPABASE_URL;
const supabaseKey = import.meta.env.VITE_SUPABASE_KEY;

const supabase = createClient(supabaseUrl, supabaseKey);

export default supabase;
```


# Query the DB
Supabase fornisce un [translator](https://supabase.com/docs/guides/api/sql-to-rest) dove possiamo digitare SQL nel browser e traduce questo in una query che possiamo indicare al supabase-client,:
```SQL
SELECT name, value FROM sales_deals ORDER BY value DESC LIMIT 1;
```
Questo si converte in:
```javascript
import supabase from "./supabase-client.js";
import { useEffect } from 'react';

function Dashboard() {
  useEffect(() => {
    fetchMetrics();
  }, []);

  async function fetchMetrics() {
    const response = await supabase
      .from('sales_deals')
      .select(`name,value`)
      .order('value', { ascending: false })
      .limit(1)
    console.log(response);
  }
 
  return (<div>...</div>);
}

export default Dashboard;
```
## Aggregate functions
Oppure se volessimo fare una query più complessa (recuperare il totale per ogni nominativo) in una così detta [aggregate function](https://supabase.com/blog/postgrest-aggregate-functions) (sono disabilitate di default, tornano dati vuoti se non abilitate...):
```javascript
const response = await supabase
      .from('sales_deals')
      .select(`name,value.sum()`)
```
## Realime subscription
Una volta che si modificano i dati della tabella si vuole avere i dati aggiornati in tempo reale tramite la "supabase realtime subscription". Tramite l'estensione "Postgres changes" è possibile:
1. ascoltare i seguenti eventi:
    - INSERT
    - UPDATE
    - DELETE
    - * (tutti e tre i precedenti)
2. Ricevere informazioni degli eventi (cosa è cambiato)
3. scatenare azioni

Si aggiorna il componente react
```javascript
import supabase from './supabase-client';
import { useEffect, useState } from 'react';
import { Chart } from 'react-charts';

function Dashboard() {
  const [metrics, setMetrics] = useState([]);

  useEffect(() => {
    fetchMetrics()

  const channel = supabase
      .channel('deal-changes')
      .on('postgres_changes',
        { 
          event: '*', 
          schema: 'public', 
          table: 'sales_deals'  
        },
        (payload) => {
            /*
            // Action
            const { new: newRecord, eventType } = payload;
            const { name, value } = newRecord;
            if (eventType === 'INSERT') {
                // per gestire i diversi tipi di eventi ...
            }
            */
          fetchMetrics();
        })
      .subscribe();

    // Clean up subscription
    return () => {
      supabase.removeChannel(channel);
    };
  }, []);
	
	async function fetchMetrics() {
    try {
      const { data, error } = await supabase
        .from('sales_deals')
        .select(`name,value`)
      if (error) {
        throw error;
      }
      setMetrics(data);
    } catch (error) {
        console.error('Error fetching metrics:', error);
    }
  }
    return (<div>...</div>);
}
export default Dashboard;
```
## Insert
Per inserire i dati in una tabella si usa il metodo `insert`:
```javascript
 async function addDeal() {
    try {
      const { error } = await supabase
        .from('sales_deals')
        .insert(newDeal) // newDeal è un oggetto con i dati da inserire
      if (error) {
        throw error;
      }
    } catch (error) {
      console.error('Error adding deal: ', error);
    }
  }
```

## Suggerimenti Extra
- Se usi TypeScript, Supabase può generare i tipi a partire dallo schema → supabase gen types.
- Puoi anche creare view SQL in Supabase per semplificare le join complesse.
- Abilita Row Level Security (RLS) per proteggere i dati, e poi crea policy ad hoc per leggere/scrivere.

# Links
- [Supabase](https://supabase.com/)
- [Corso scrimbla](https://scrimba.com/intro-to-supabase-c0abltfqed)
- [Video Supabase is just Postgres](https://www.youtube.com/watch?v=T-qAtAKjqwc&ab_channel=Supabase)
- [Supabase in Deno](https://docs.deno.com/examples/supabase/)
- [Supabase in Jsr](https://jsr.io/@supabase/supabase-js)
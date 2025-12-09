# YAML
E' un formato di serializzazione dei dati umanamente leggibile, spesso utilizzato per configurazioni e scambi di dati tra linguaggi di programmazione. Il nome YAML è un acronimo ricorsivo che sta per "YAML Ain't Markup Language" (YAML non è un linguaggio di markup). Se JSON è un formato per passare dati, abbastanza leggibile e più leggibile di XML, YAML è ancora più leggibile e permette di esprimere strutture dati complesse in modo semplice e chiaro.

I file YAML hanno l'estensione `.yaml` o `.yml`. Utilizzano l'indentazione per definire la gerarchia dei dati, simile a Python.

# Sintassi di base
```yaml
# commento
chiave: valore  # coppia chiave-valore con stringa ( oppure anche "valore"o 'valore' per stringhe complesse)
version: 25     # valore numerico
virgola: 0.5    # valore decimale
vero: true      # valore booleano (sia maiuscolo che minuscolo o anche YES/yes/NO/no)
nullo: null     # valore nullo (può essere anche None o anche vuoto)
notaNumber: .NAN # Not a Number
services:
  web:
    image: nginx:latest # esempio oggetto con proprietà
    ports:    # lista 1         
      - 80:80  
      - 443:443
    colori: [rosso, verde, blu]  # lista 2
```

Se la striga è lunga allora si usa un folded block che indica una unica riga con \n in fondo ma + leggibile:
```yaml
long_string: >
  Questa è una stringa molto lunga che
  viene divisa su più righe ma sarà
  considerata come un'unica riga.
```
 se metto la pipe allora è una frase su più righe:
```yaml
long_string: |
  Questa è una stringa molto lunga che
  viene divisa su più righe ma sarà
  considerata come un'unica riga.
```
## Tipizzazioni
Si possono specificare anche i tipi di dati:
```yaml
integer_value: !!int 42  # intero
float_value: !!float 3.14  # float
boolean_value: !!bool true  # booleano
null_value: !!null null  # null

string_value: !!str "Hello, World!"  # stringa
```

# Ancore
Le ancore permettono di riutilizzare parti del file YAML, evitando la ripetizione di codice. Si definisce un'ancora con `&` e si fa riferimento ad essa con `*`.
```yaml
ancora1: &ancora1
  chiave1: valore1
  chiave2: valore2

ancora2: &ancora2
  <<: *ancora1  # si inserisce tutto il contenuto dell'ancora1 (come ... in JS)
  chiave3: valore3 # questo può fare l'override di chiave

uso_ancora: *ancora1 # si inserisce tutto il contenuto dell'ancora1
```

# Multi document
E' possibile avere più documenti in un singolo file YAML, separati da `---`.
```yaml
---
documento1:
  chiave1: valore1
  chiave2: valore2
...
---
documento2:
  chiaveA: valoreA
  chiaveB: valoreB
...
---
documento3:
  chiaveX: valoreX
  chiaveY: valoreY
``` 
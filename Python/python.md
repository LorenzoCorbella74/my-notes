# Python Cheatsheet

## Indice

- [Indentazione](#indentazione)
- [Commenti](#commenti)
- [Print](#print)
- [Variabili](#variabili)
  - [Stringhe](#stringhe)
    - [Formattazione stringhe](#formattazione-stringhe)
- [Operatori](#operatori)
  - [Operatori Aritmetici](#operatori-aritmetici)
  - [Operatori di Confronto](#operatori-di-confronto)
  - [Operatori Logici](#operatori-logici)
  - [Operatori di Assegnazione](#operatori-di-assegnazione)
  - [Operatori di Identità](#operatori-di-identità)
  - [Operatori di Appartenenza](#operatori-di-appartenenza)
- [Controllo del flusso](#controllo-del-flusso)
- [Liste e principali metodi](#liste-e-principali-metodi)
- [Tuple](#tuple)
- [Range](#range)
- [Dizionari e principali metodi](#dizionari-e-principali-metodi)
- [Insiemi e principali metodi](#insiemi-e-principali-metodi)
- [One line](#one-line)
- [Funzioni](#funzioni)
    - [Lamba function](#lamba-function)
- [Gestione delle eccezioni](#gestione-delle-eccezioni)
    - [Assertion](#assertion)
- [Programmazione Orientata agli Oggetti (OOP)](#programmazione-orientata-agli-oggetti-oop)
- [Moduli](#moduli)
  - [Moduli Built-in](#moduli-built-in)
- [Python Shell](#python-shell)
- [Uscire da Python](#uscire-da-python)

## Indentazione

L'indentazione in Python è fondamentale, poiché definisce i blocchi di codice. Tutto il codice all'interno di strutture come cicli, funzioni o condizioni deve essere correttamente indentato, solitamente con 4 spazi.

## Commenti

In Python, i commenti si scrivono utilizzando il simbolo `#`. Tutto ciò che segue il `#` sulla stessa riga viene ignorato dall'interprete.

```python
# Questo è un commento
x = 10  # Questo è un commento alla fine di una riga
```

## Print

La funzione `print()` viene utilizzata per visualizzare output sullo schermo. Può stampare stringhe, numeri, variabili e risultati di espressioni.
È possibile utilizzare più argomenti separati da virgole, e `print()` aggiunge automaticamente un carattere di nuova linea alla fine.
Per evitare la nuova linea automatica, si può utilizzare l'argomento `end`:

```python
print("Ciao", end=" ")
print("Mondo")

# Stampa più argomenti separati da virgole ed eventualmente il separatore desiderato.
print("Uno", 'due', 'tre', sep=", ")

```

Ricordarsi di mettere sempre gli **escape characters** quando necessario (ad esempio dentro le stringhe in un print statement).

```python
# Esempi di escape characters
newline = "Questa è una riga\nQuesta è un'altra riga"
tab = "Colonna1\tColonna2"
backslash = "Questo è un backslash: \\"
quote = "Questo è un apice singolo: \' e questo è un apice doppio: \""
```

Da notare che se si inizia una stringa con una r, come in `r"stringa"`, si crea una raw string in cui gli escape characters non vengono interpretati. Ad esempio:

```python
raw_string = r"Questa è una riga\nQuesta è un'altra riga"
print(raw_string)
```

## Variabili

Le variabili si dichiarano semplicemente con il nome e il valore assegnato. Python è un linguaggio a tipizzazione dinamica, quindi non è necessario dichiarare il tipo di variabile.

```python
x = 10              # Intero
pi = 3.14           # Virgola mobile
name = "Alice"      # Stringa
is_valid = True     # Booleano è True o False

# Assegnazione multipla
x, y, z = 1, 2, 3
size, color, shape = ["L", "rosso", "tondo"]

a = b = c = 42  # Tutti hanno lo stesso valore

# L'assegnazione multipla permette di fare lo swap di avariabili
x, y = y, x
```

### Stringhe

Per concatenerare le stringhe si utilizza l'operatore `+`( oppure si mette le stringhe accanto), mentre per ripetere le stringhe si usa l'operatore `*`.

```python
nome = "Alice"
nome[0]  # Primo carattere della stringa
nome[-1]  # Ultimo carattere della stringa
nome[1:4]  # Sottostringa dal secondo al quarto carattere (indice 1 incluso, indice 4 escluso)
nome[:4]  # Sottostringa dai primi quattro caratteri
nome[1:]  # Sottostringa dal secondo carattere fino alla fine
cognome = "Rossi"
nome_completo = nome + " " + cognome  # Utilizzo dell'operatore +
nome_completo2 = f"{nome} {cognome}"  # Utilizzo delle f-string
ripetizione = nome * 3  # Ripete la stringa 3 volte

# esempio di "interpolazione"
print('Esempio di {}:'.format(nome_completo))

# lunghezza di una stringa
lunghezza = len(nome_completo)

# conversione di intero in stringa
stringa = str(x)

# Conversione di un float in integer
intero = int(pi)
```

Per fare stringhe multi linea si unano le **triple quotes**:

```python
stringa=''' Caro
Lorenzo
Mio'''
```

Le funzioni di manipolazione più famose sono:

```python
# Esempi di funzioni di manipolazione delle stringhe
testo = "   Ciao Mondo!   "
testo_maiuscolo = testo.upper()  # Converte in maiuscolo
testo.islower()  # Verifica se la stringa è tutta in minuscolo
testo.isupper()  # Verifica se la stringa è tutta in maiuscolo
testo_minuscolo = testo.lower()  # Converte in minuscolo
testo_strip = testo.strip()      # Rimuove spazi iniziali e finali
testo_replace = testo.replace("Mondo", "Python")  # Sostituisce "Mondo" con "Python"
testo_split = testo.split()      # Divide la stringa in una lista di parole
testo_find = testo.find("Mondo")  # Restituisce l'indice della prima occorrenza di "Mondo" (o -1 se non trovato)
testo_count = testo.count("Mondo")  # Conta quante volte "Mondo" appare nella stringa
testo_startswith = testo.startswith("Ciao")  # Verifica se la stringa inizia con "Ciao"
testo_endswith = testo.endswith("Mondo!")  # Verifica se la stringa termina con "Mondo!"

', '.join(testo.split())  # Unisce le parole separate da spazi con ', '
```

### Formattazione stringhe

Si può usare:

- Le f-string (introdotte in Python 3.6) che permettono di inserire dentro le stringhe le variabili e le espressioni Python in modo semplice e leggibile
- Il metodo `format()`
- L'operatore `%` (vecchio stile)
  Facciamo degli esempi:

```python
nome = "Alice"
cognome = "Rossi"
# f-string
nome_completo = f"{nome} {cognome}"
# metodo format()
nome_completo2 = "{} {}".format(nome, cognome)
# operatore %
nome_completo3 = "%s %s" % (nome, cognome)
print(nome_completo, nome_completo2, nome_completo3)
```

## Operatori

Abbiamo le seguenti tipologie di operatori:

### Operatori Aritmetici

```python
x + y  # Addizione
x - y  # Sottrazione
x * y  # Moltiplicazione
x / y  # Divisione
x // y # Divisione intera
x % y  # Modulo (resto)
x ** y # Potenza
```

### Operatori di Confronto

```python
x == y  # Uguaglianza
x != y  # Diverso
x > y   # Maggiore
x < y   # Minore
x >= y  # Maggiore o uguale
x <= y  # Minore o uguale
```

### Operatori Logici

```python
x and y  # AND logico
x or y   # OR logico
not x    # Negazione
```

### Operatori di Assegnazione

```python
x += 1  # Equivalente a x = x + 1
x -= 1  # Equivalente a x = x - 1
x *= 2  # Equivalente a x = x * 2
x /= 2  # Equivalente a x = x / 2
x //= 2 # Equivalente a x = x // 2
x %= 2  # Equivalente a x = x % 2
x **= 2 # Equivalente a x = x ** 2
```

### Operatori di Identità

```python
x is y     # True se x e y sono lo stesso oggetto
x is not y # True se x e y sono oggetti diversi
```

### Operatori di Appartenenza

Gli operatori di appartenenza verificano se un elemento è presente o meno in una sequenza (come una lista, una tupla o una stringa).

```python
x in lista     # True se x è in lista
x not in lista # True se x non è in lista

# Esempio
numeri = [1, 2, 3, 4, 5]
trovato = 2 in numeri  # True
```

## Controllo del flusso

La sintassi per gli if statement è:

```python
x = 10
if x > 5:
    print("x è maggiore di 5")
elif x == 5:
    print("x è uguale a 5")
else:
    print("x è minore di 5")
```
L'**operatore ternario** in Python è un one line code che permette di scrivere un if-else in una sola riga. Ha la seguente sintassi:

```python
x = 10
messaggio = "x è maggiore di 5" if x > 5 else "x è minore o uguale a 5"
print(messaggio)
```

Il while loop invece:

```python
i = 0
while i < 5:
    print(i)
    i += 1
```

In caso di ciclo While infinito con condizione di uscita abbiamo:

```python
while True:
    print('Please type your name:')
    name = input()
    if name== 'Lore':
        break
    if name != 'Joe':
        continue
print('Fuori dal ciclo while....')
```

La keyword `continue` serve per saltare l'iterazione corrente di un ciclo e passare direttamente alla successiva.

Il ciclo loop `for` viene utilizzato per iterare su una sequenza (come una lista, una tupla, un dizionario, un insieme o una stringa). La sintassi è:

```python
for elemento in sequenza:
    # blocco di codice da eseguire per ogni elemento

# oppure con la funzione range
for i in range(0,10,2):  #anche all'indietro (5,-1,-1)
    print(i)
```

Esiste anche il `for else statement` dove l'else viene impiegato dopo il break:

```python
for i in range(5):
    print(i)
    if i == 3:
        break
else:
    print("Il ciclo for è terminato senza break")
```

## Liste e principali metodi

Le liste sono come gli array in altri linguaggi. Sono 0 based.

```python
# Creazione di una lista
numeri = [1, 2, 3, 4, 5]
nomi = ["Alice", "Bob", "Charlie"]

nomi[0]  # Accesso al primo elemento della lista
nomi[-1]  # Accesso al primo elemento della lista dalla fine
nomi[1:2]  # slice (dove l'ultimo elemento non lo prende)
nomi[:]  # copia della lista originaria

# Aggiunta di elementi
numeri.append(6)        # Aggiunge 6 alla fine
numeri.insert(2, 10)    # Inserisce 10 in posizione 2

# Rimozione di elementi
numeri.remove(3)        # Rimuove il primo valore 3 trovato
ultimo = numeri.pop()   # Rimuove e restituisce l'ultimo elemento
primo = numeri.pop(0)   # Rimuove e restituisce il primo elemento

trovato = 2 in numeri   # Controlla se 2 è presente nella lista

# Ordinamento e inversione
numeri.sort()           # Ordina la lista in ordine crescente
numeri.reverse()        # Inverte l'ordine della lista oppure .sort(reverse=True)
sorted(numeri)          # Restituisce una nuova lista ordinata senza modificare l'originale

lunghezza = len(numeri) # Ottenere la lunghezza della lista

del numeri[2]           # cancella un elemento
```

Per iterare sugli elementi di una lista si usa il `for in` loop oppure il metodo enumerate (che permette di recuperare l'indice)

```python
# Iterazione su una lista
for num in numeri:
    print(num)

# oppure si usa enumerate
for i, num in enumerate(numeri):
    print('Index{} in nueri is:{}'.format(str(i),num))

# per loop su più liste (aventi pari dimensioni) allo stesso stesso momento
name=['Lore', 'Luce', 'Chiara']
age=[52,56,12]
for n, a in zip(name, age):
        print('{} is {} year old'.format(n,a))
```

## Tuple

I Tuple, come le stringhe, sono immutabili, cioè non possono essere cambiati

```python
# Creazione di una tupla
coordinate = (10, 20)
nomi = ("Alice", "Bob", "Charlie")

l = len(coordinate)                         # Lunghezza della tupla
list_coordinata = list(coordinate)          # Converte la tupla in lista
tuple_from_list = tuple(list_coordinata)    # Converte la lista di nuovo in tupla

# Accesso agli elementi
a = coordinate[0]
```

## Range

```python
# Range
range_obj = range(5)  # Crea un intervallo da 0 a 4
range_list = list(range(1, 10, 2))  # Converte in lista con step di 2
```

## Dizionari e principali metodi

I dizionari sono simili agli oggetti in altri linguaggi di programmazione, dove le chiavi rappresentano gli attributi e i valori rappresentano i dati associati.

```python
# Creazione di un dizionario
dati = {"nome": "Alice", "età": 25, "città": "Roma"}

# Accesso ai valori
nome = dati["nome"]
eta = dati.get("età") # ha due parametri la chiave ed il default value (opzionale)

# Aggiunta/Modifica valori
dati["professione"] = "Ingegnere"
dati.update({"nazionalità": "Italiana"})
dati.setdefault("nazionalità", "Italiana")  # Aggiunge la chiave solo se non esiste

# Rimozione di elementi
del dati["città"]
età = dati.pop("età")

tutte_le_chiavi = dati.keys()       # Ottiene le chiavi
tutti_i_valori = dati.values()      # Ottiene i valori
tutti_gli_elementi = dati.items()   # Ottiene coppie chiave-valore

# Iterazione su un dizionario
for chiave, valore in dati.items():
    print(f"{chiave}: {valore}")


# vedere che una chiave esiste nel dizionario
if "nome" in dati.keys(): # keys() si può anche omettere, basta scrivere if "nome" in dati:
    print("La chiave 'nome' esiste nel dizionario")
if "professione" not in dati:
```

Per mergiare due oggetti si usa la sintassi:

```python
dati1 = {"nome": "Alice", "età": 25}
dati2 = {"città": "Roma", "professione": "Ingegnere"}

dati_merged = {**dati1, **dati2}
print(dati_merged)
```

## Insiemi e principali metodi

Gli insieme o sets sono collezioni non ordinate di elementi unici. Ed essendo non ordinate, non supportano l'indicizzazione (quindi non si può accedere agli elementi tramite un indice).

```python
# Creazione di un insieme
numeri = {1, 2, 3, 4, 5}
numeri = set([1, 2, 3, 4, 5])  # Converte una lista in insieme

# Aggiunta e rimozione di elementi
numeri.add(6)
numeri.remove(3)  # Genera un errore se l'elemento non esiste
numeri.discard(10)  # Non genera errore se l'elemento non esiste

# Operazioni sugli insiemi
A = {1, 2, 3}
B = {3, 4, 5}

unione = A | B                 # {1, 2, 3, 4, 5} oppure: A.union(B)
intersezione = A & B           # {3}  oppure: A.intersection(B)
differenza = A - B             # {1, 2} oppure: A.difference(B)
differenza_simmetrica = A ^ B  # {1, 2, 4, 5} oppure: A.symmetric_difference(B)
```

## One line

Data una lista, set o dictionary, si può modificare con una linea:

```python
numeri = [1, 2, 3, 4, 5]
quadrati = [x**2 for x in numeri]  # [1, 4, 9, 16, 25]

b= {"abc","def"}
c = {x.upper() for x in b}  # {'ABC', 'DEF'}

# per dizionario
c= {'nome':"Lore", 'age':5}
d = {k.upper(): v for k, v in c.items()}  # {'NOME': 'Lore', 'AGE': 5}
```

## Funzioni

```python
# Definizione di una funzione
def saluta(nome):
    return f"Ciao, {nome}!"

# Chiamata di una funzione
print(saluta("Alice"))

# Funzioni con parametri di default
def potenza(base, esponente=2):
    return base ** esponente

print(potenza(3))  # 9
print(potenza(3, 3))  # 27

# Funzioni con argomenti variabili
def somma(*numeri):
    return sum(numeri)

print(somma(1, 2, 3, 4))  # 10

# Funzioni con parametri ARBITRARI chiave-valore
# indicati con `**` o `**kwargs`
def descrivi_persona(nome, **dettagli):
    print(f"Nome: {nome}")
    for chiave, valore in dettagli.items():
        print(f"{chiave}: {valore}")

descrivi_persona("Alice", età=25, città="Roma")
```
`kwargs` è un dizionario che contiene tutti i parametri chiave-valore passati alla funzione che non sono stati definiti esplicitamente.


Quando si usa delle variabili all'interno della funzione si può solo usarla all'interno della funzione.  Con la keyword `global` si indica che la variabile è globale e può essere modificata all'interno della funzione.

```python
x = 10

def modifica():
    global x
    x = 20

modifica()
print(x)  # 20
```

### Lamba function

Le **lambda function** sono funzioni anonime, definite con la parola chiave `lambda`. Possono avere qualsiasi numero di argomenti, ma solo un'espressione.

```python
# Funzione normale
def quadrato(x):
    return x ** 2

# Lambda function equivalente
quadrato_lambda = lambda x: x ** 2

print(quadrato(5))         # 25
print(quadrato_lambda(5))  # 25
```
Da notare che una funzione può ritornare anche una lambda function, proprio come in js:
```python
def make_adder(n):
    return lambda x: x+ n

plus_3 = make_adder(3)
print(plus_3(10))  # 13
```

## Gestione delle eccezioni

Si usa `try/except` per gestire le eccezioni. Il blocco `try` contiene il codice che potrebbe generare un'eccezione, mentre il blocco `except` gestisce l'errore. Opzionalmente, si possono usare i blocchi `else` e `finally` (che viene sempre eseguito indipendentemente dal fatto che si sia verificata un'eccezione o meno).

```python
try:
    x = 10 / 0  # Genera un errore
except ZeroDivisionError:
    print("Errore: divisione per zero")
except Exception as e:
    print(f"Errore generico: {e}")
    # oppure print(f"Errore generico: {}".format(e))
else:
    print("Nessun errore")
finally:
    print("Blocco eseguito sempre")
```

E' possibile usare `raise` per generare eccezioni specifiche e fornire un comportamento personalizzato per ciascuna di esse. Ad esempio:

```python
def divisione(a, b):
    if b == 0:
        raise ValueError("Il divisore non può essere zero")
    return a / b

try:
    risultato = divisione(10, 0)
except ValueError as e:
    print(f"Errore: {e}")
```

### Assertion

L'istruzione `assert` viene utilizzata per verificare che una condizione sia vera. Se la condizione è falsa, viene generata un'eccezione `AssertionError`.

```python
x = 10
assert x > 0, "x deve essere positivo"
assert x < 5, "x deve essere minore di 5"  # Genera AssertionError
```

## Programmazione Orientata agli Oggetti (OOP)

```python
# Definizione di una classe
class Persona:
    def __init__(self, nome, eta):
        self.nome = nome
        self.eta = eta

    def saluta(self):
        return f"Ciao, mi chiamo {self.nome} e ho {self.eta} anni."

# Creazione di un oggetto
p1 = Persona("Alice", 25)
print(p1.saluta())

# Ereditarietà
class Studente(Persona):
    def __init__(self, nome, eta, corso):
        super().__init__(nome, eta)
        self.corso = corso

    def info_corso(self):
        return f"Studio {self.corso}."

s1 = Studente("Bob", 22, "Informatica")
print(s1.saluta())
print(s1.info_corso())

# Metodi speciali
class Contatore:
    def __init__(self, valore=0):
        self.valore = valore

    def __str__(self):
        return f"Valore attuale: {self.valore}"

    def __add__(self, altro):
        return Contatore(self.valore + altro.valore)

c1 = Contatore(10)
c2 = Contatore(5)
c3 = c1 + c2  # Usa il metodo __add__
print(c3)  # "Valore attuale: 15"
```

Il costrutto `if __name__ == "__main__":` viene utilizzato per eseguire del codice solo quando il file viene eseguito direttamente, e non quando viene importato come modulo. Ad esempio:

```python
def main():
    print("Esecuzione del file principale")

if __name__ == "__main__":
    main()
```

__main__ è il nome speciale del modulo principale in Python. Quando un file viene eseguito direttamente, il suo nome modulo è impostato su `__main__`. Questo permette di distinguere tra l'esecuzione diretta di un file e l'importazione come modulo.

## Moduli

I moduli permettono di organizzare il codice in file separati e riutilizzabili. **Attenzione che il nome del file corrisponde al nome del modulo da importare**.

```python
# Creazione di un modulo (file: mio_modulo.py)
def saluta(nome):
    return f"Ciao, {nome}!"

PI = 3.14159
```

Per importare un modulo:

```python
# importa il modulo mio_modulo
# per importare tutto si usa from mio_modulo import *
import mio_modulo

# oppure solo alcune funzioni
from mio_modulo import saluta, PI

# utilizzo
print(mio_modulo.saluta("Alice"))
print(mio_modulo.PI)

# utilizzo con alias
import mio_modulo as mm

print(mm.saluta("Charlie"))
```

### Moduli Built-in

```python
import math
print(math.sqrt(16))  # 4.0

import random
print(random.randint(1, 10))  # Numero casuale tra 1 e 10

import datetime
print(datetime.datetime.now())  # Data e ora attuale

import os
print(os.getcwd())  # Directory corrente
```

Il **modulo di logging** è molto usato e permette di essere configurato:

```python
import logging

# Configurazione di base del logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

logging.debug("Messaggio di debug")
logging.info("Messaggio informativo")
logging.warning("Messaggio di avviso")
logging.error("Messaggio di errore")
logging.critical("Messaggio critico")
```

## Python Shell

Quando si vede 3 simboli `>>>` significa che si è nella Python Shell interattiva, dove è possibile eseguire comandi Python uno alla volta e vedere immediatamente i risultati.

```python
>>> print("Ciao, mondo!")
Ciao, mondo!
```

## Uscire da Python

Per uscire da python si usa il modulo `sys` con la funzione `sys.exit()`.

```python
import sys
sys.exit()
```


# Links
- [Video All Python Syntax in 25 Minutes](https://www.youtube.com/watch?v=PNSIWjWAA7o)
- [Python cheatsheet](https://labex.io/pythoncheatsheet/)
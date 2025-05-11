## Indice

1. [Introduzione](#sql)
2. [Select data](#select-data)
3. [Conditions](#conditions)
4. [Condizioni complesse](#condizioni-complesse)
   - [Numerical filtering](#numerical-filtering)
   - [Condizione NON è uguale](#per-la-condizione-non-è-uguale)
   - [Partial matches NOT & LIKE](#partial-matches-not--like)
5. [AND operator](#and-operator)
6. [OR operator](#or-operator)
7. [IN operator](#in-operator)
8. [ORDER BY](#order-by)
9. [LIMIT](#limit)
10. [Aggregations e operatore AS](#aggregations-e-operatore-as)
11. [GROUP BY](#group-by)
12. [HAVING](#having)
13. [Insert](#insert)
14. [Update](#update)
15. [Delete](#delete)
16. [Links](#links)

# SQL

Il linguaggio `Structured Query Language` è un linguaggio che permette di
inserire, accedere e manipolare i dati all'interno di DB relazionali. E'
possibile eseguire `comandi`(simili all'Inglese, quindi facilmente leggibili) o
`query` per eseguire azioni su tabelle all'interno di DB. E' famosissimo
nell'industria tanto che è solo dietro a JS `1)JS 2)SQL 3) HTML&CSS )`.

Nei database relazioni i dati sono contenuti in tabelle aventi varie colonne che
possono contenere dati di tipi diversi ed ogni riga rappresenta un `record`
avente un `field` id che è un numero incrementale. I DB relazioni sono risorse
dimamiche dove possiamo aggiungere, modiifcare e cancellare i dati. Queste
operazioni sono conosciute come Data Manipulation Language (DLM) o CRUD
Operations (Create, Read, Update, Delete).

Esistono varie `flavours` ed utilizzano tutti la stessa sintassi (MySQL,
PostgreSQL, Micosoft SQL, Oracle SQL)

## Select data

Per selezionare tutte le colonne di una tabella (finendo con `;` per indicare la
fine)

```SQL
SELECT * FROM <nome_tabella>;
/*
	Select the various columns SEPARATE DA virgola 
*/
SELECT brand, model, condition, year FROM cars;
```

Da notare che i comandi sono generalmente maiuscoli `per convenzione` mentre in
minuscolo sono i nomi delle colonne e della tabella, ma si potrebbe scrivere i
comandi tutto in minuscolo. Si può usare anche SPACING and indentation in quanto
SQL non lo considera.

## Conditions

Per filtrare i risultati si usa la WHERE clause **dopo la tabella**:

```SQL
SELECT brand, model, color, price FROM cars
	WHERE color = 'black';
```

## Condizioni complesse

1. numerical filtering

```SQL
SELECT brand, model, condition, price FROM cars
	WHERE condition >= 3;
```

2. Per la condizione NON è uguale (eccetto un valore)

```SQL
SELECT brand, model, year, price FROM cars
	WHERE year != 1965;
```

3. partial matches NOT & LIKE. L'operatore `%` ogni numero di ogni carattere ,
   mntre l'operatore `_` uno di ogni carattere

```SQL
/*
	Select the brand, model, color and year
		find any car where the color includes 'green'
*/
SELECT brand, model, color, year FROM cars
	WHERE color LIKE '%green%';
```

oppure che non contengono la parola green

```SQL
SELECT brand, model, color, year FROM cars
    WHERE color NOT LIKE '%green%';
```

Oppure che un valore inizia con una lettera

```SQL
/*
	Select the brand, model, color and year for cars
		where the model is 'DB' followed by any other single character
*/

SELECT brand, model, color, year FROM cars
	WHERE model LIKE 'DB_';
```

## AND operator

Per combinare delle condizioni si usa l'AND operator

```SQL
SELECT brand, model, color, year FROM cars
    WHERE color NOT LIKE '%green%'
        AND year >= 1965
        AND model LIKE 'DB_';
```

Per indicare un intervallo (ad esempio le macchine tra il 1980 e il 1989)
potremmo fare:

```SQL
SELECT brand, model, year, price FROM cars
	WHERE year >= 1980
	AND year <= 1989;
```

Ma è più elegante usare l'operatore BETWEEN

```SQL
SELECT brand, model, year, price FROM cars
    WHERE year BETWEEN 1980 AND 1989;
```

Un altro esempio:

```SQL
/*
	Select brand, model, condition, color and price from cars
		where the price is between $20,000 and $60,000
		and the condition is between 1 and 3
		and the color contains red
*/

SELECT brand, model, condition, color, price FROM cars
	WHERE price BETWEEN 20000 AND 60000
	AND condition BETWEEN 1 AND 3
	AND color LIKE '%red%';
```

## OR operator

Per combinare le condizioni dove o una o le altre sono true si può usare
l'operatore OR

```SQL
/*
	Find the brand, model, condition and price of cars
		where the price is less than $250,000
		or the brand is Porsche
*/

SELECT brand, model, condition, price FROM cars
	WHERE price < 250000
	OR brand = 'Porsche';

/*
	Find the brand, model, condition and price of cars
		where the price is less than $250,000
		or the brand is Porsche,
		only show cars with condition > 3
*/

SELECT brand, model, condition, price FROM cars
	WHERE (price < 250000
	OR brand = 'Porsche')
	AND condition > 3;
```

Notare l'uso delle parentesi per indicare l'ordine di esecuzione delle
condizioni.

```SQL
SELECT brand, model, condition, price FROM cars
    WHERE color LIKE '%red%'
    OR year BETWEEN 1980 AND 1990;

/*
	Search for columns: brand, model, color, year, price, sold
		from the table cars
		where the color is a shade of red
		or the year is between 1960 and 1969
		and sold is false
*/

SELECT brand, model, color, year, price, sold FROM cars
	WHERE (color LIKE '%red%'
	OR year BETWEEN 1960 AND 1969)
	AND sold IS FALSE;
```

Da notare che per i booleani si usa `IS` e non `=` (come per i numeri e
stringhe). Per i valori nulli si usa `IS NULL` e `IS NOT NULL`. Ad esempio:

```SQL
SELECT brand, model, color, year, price, sold FROM cars
	WHERE (color LIKE '%red%'
	OR year BETWEEN 1960 AND 1969)
	AND sold IS FALSE;
```

## IN operator

Per selezionare più valori in una colonna si può usare l'operatore `IN` che
permette di specificare una lista di valori possibili. Ad esempio:

```SQL
/*
	Select the brand, model, price and sold columns from cars
		the brand can be 'Ford', 'Chevrolet' or 'Ferrari'
		sold must be false
*/

SELECT brand, model, price, sold FROM cars
	WHERE brand IN ('Ford', 'Chevrolet', 'Ferrari')
	AND sold IS FALSE;
```

Oppure per selezionare più colonne

```SQL
/*
	Select the brand, model, condition and year from cars
		Where the year is 1961, 1963, 1965, 1967 or 1969
		and the condition is 3 or higher
		and sold is false
*/

SELECT brand, model, condition, year FROM cars
	WHERE year IN (1961, 1963, 1965, 1967, 1969)
	AND condition >= 3
	AND sold IS FALSE;

    /*
	Select brand, model, price and sold from cars
		filter out any cars which are sold
		show cars where the brand is none of ('Ford', 'Triumph', 'Chevrolet', 'Dodge')
		or the price is less than $50000
*/

SELECT brand, model, price, sold FROM cars
	WHERE (
		brand NOT IN ('Ford', 'Triumph', 'Chevrolet', 'Dodge')
		OR price < 50000
	) AND sold IS FALSE;
```

Un altro esempio:

```SQL
/*
	Select brand, model, year, sold from cars
		where the brand is 'Dodge' and year is in the 60s
		or the brand is either 'Ford' or 'Triumph' and the car is from the 70s
		only select cars where sold is not true
*/
SELECT brand, model, year, sold FROM cars
  WHERE ((brand = 'Dodge' AND year BETWEEN 1960 AND 1969)
  OR (brand IN ('Ford', 'Triumph') AND year BETWEEN 1970 AND 1979))
  AND SOLD IS NOT TRUE;
```

## ORDER BY

Per ordinare i risultati si usa l'operatore `ORDER BY` seguito dal nome della
colonna da ordinare. Per default l'ordinamento è crescente (ASC) ma si può usare
anche `DESC` per l'ordinamento decrescente. Ad esempio:

```SQL
/*
    Select the brand, model, condition and price from cars
    where the car is not sold
    and the condition is not 5
    order the table by condition in descending order
    and by price in ascending order
*/
SELECT brand, model, condition, price FROM cars
	WHERE sold IS FALSE
	AND condition != 5
	ORDER BY condition DESC, price;
```

## LIMIT

Per limitare il numero di risultati si usa l'operatore `LIMIT` seguito dal
numero di risultati desiderati. Ad esempio:

```SQL
/*
	Select the brand, model, color and price from cars
		where the color is a shade of 'red'
		and sold is false
		order by price
		limit the results to 5
*/

SELECT brand, model, color, price FROM cars
	WHERE color LIKE '%red%'
	AND sold IS FALSE
	ORDER BY price
	LIMIT 5;
```

## Aggregations e operatore AS

Permettono di eseguire operazioni su più righe e restituire un singolo valore
(può essere utile per contare il numero di record o per fare somme). Ad esempio
mettendo il risultato in una colonna con nome personalizzato (alias) usando
l'operatore `AS`:

```SQL
/*
	Count the number of cars
		where sold is true tornando il risultato come total_sold
*/
SELECT COUNT(*) AS total_sold FROM cars
	WHERE sold IS TRUE;
```

Gli operatori di aggregazione sono:

- `COUNT(*)` per contare il numero di righe
- `SUM(column_name)` per sommare i valori di una colonna
- `AVG(column_name)` per calcolare la media dei valori di una colonna
- `MIN(column_name)` per calcolare il valore minimo di una colonna
- `MAX(column_name)` per calcolare il valore massimo di una colonna
- `HAVING` per filtrare i risultati dopo l'aggregazione
- CEIL() per arrotondare un numero al valore intero più vicino

```SQL
SELECT COUNT(*) FROM cars WHERE sold IS FALSE;
SELECT SUM(price) FROM cars WHERE sold IS FALSE;
SELECT AVG(price) FROM cars WHERE sold IS FALSE;
SELECT MIN(price) FROM cars WHERE sold IS FALSE;
SELECT MAX(price) FROM cars WHERE sold IS FALSE;

/*
	Select the average, minimum and maximum price from cars
		where sold is true
	Round the average up to the nearest whole number
		and use 'avg' as the alias for that result	
*/

SELECT
	CEIL(AVG(price)) AS avg,
	MIN(price),
	MAX(price)
FROM cars
	WHERE sold IS TRUE;
```

NOtare che si mette all'interno di tali operatori il **nome della colonna su cui
si volgiono fare tali aggregazioni**!!!.

## GROUP BY

Per raggruppare i risultati in base a una colonna si usa l'operatore `GROUP BY`
che permette di aggregare i risultati in base a una colonna specifica. Ad
esempio:

```SQL
/*
	Select the brand, and a count of the brand from cars
		alias the count as brand_count
		group by the brand column
*/

SELECT brand, count(brand) AS brand_count FROM cars
	GROUP BY brand;


/* oppure */
SELECT
	CEIL(AVG(price)) AS avg,
	MIN(price) AS min,
	MAX(price) AS max
FROM cars
	WHERE sold IS TRUE
	GROUP BY brand;
```

In questo caso i risultati sono raggruppati per marca e per ogni marca viene
restituito il prezzo medio, minimo e massimo delle macchine vendute.

Altro esempio:

```SQL
/*
	Select:
		* the brand
		* a count of the brand
		* and an average of the price for each brand
		* round the average down to the nearest number
		* alias the average as 'AVG' in your output
	From cars where
		the car has not been sold
	Group the table by brand.
*/

SELECT brand, count(brand), FLOOR(AVG(price)) AS AVG
	FROM cars
	WHERE sold IS FALSE
	GROUP BY brand;
```

## HAVING

Per filtrare i risultati dopo l'aggregazione si usa l'operatore `HAVING` che
permette di specificare una condizione sui risultati aggregati. Ad esempio:

```SQL
/*
	Select:
		* the brand
		* a count of the brand
		* and an average of the price for each brand
		* round the average down to the nearest number
		* alias the average as 'AVG' in your output
	From cars where
		the car has not been sold
	Group the table by brand.
	
	Show results where the count is > 1
*/

SELECT brand, count(brand), FLOOR(AVG(price)) AS AVG
	FROM cars
	WHERE sold IS FALSE
	GROUP BY brand
	HAVING count(brand) > 1;
```

Altro esempio:

```SQL
/*
	Select:
		* year
		* a count of cars from that year, aliased as car_count
		* the maximum price
		* the minimum price
	from the table cars
		where the car has been sold
	group by year
		only show years where more than one car has been sold from that year
	order the result by car_count
*/

SELECT year,
	count(year) AS car_count,
	MAX(price),
	MIN(price)
FROM cars
WHERE sold IS TRUE
GROUP BY year
HAVING count(year) > 1
ORDER BY car_count;
```

NOtare che quando si vuole avere i risultati "più bassi" o "più alti" in base ad
una colonna di date si devono ordinarli:

```SQL
/*
	Select brand, model, and year from cars
		only show the oldest 5 cars in the database
		show cars which haven't been sold
*/

SELECT brand, model, year FROM cars
	WHERE sold IS FALSE
	ORDER BY year
	LIMIT 5;
```

Oppure altro esempio:

```SQL
/*
	Select color and count how many cars have each color
		find cars which have not been sold
		order by count in descending order
		only show results where the count is greater than 2
*/
SELECT color, count(color) FROM cars
    WHERE sold IS FALSE
    GROUP BY color
    HAVING count(color) > 2
    ORDER BY count(color) DESC;
```

## Insert
Si utilizza l'operatore `INSERT INTO` seguito dal nome della tabella e dalle
colonne in cui si vogliono inserire i dati. Ad esempio:

```SQL
INSERT INTO cars (
	brand, model, year, price, color, condition, sold
) VALUES (
	'Ford', 'Escort RS2000', 1978, 39000, 'blue', 4, FALSE
), (
	'Aston Martin', 'V8 Vantage', 1977, 145000, 'dark green', 5, FALSE
);
```

## Update
Si utilizza l'operatore `UPDATE` seguito dal nome della tabella e dalla
colonna da aggiornare. Ad esempio:

```SQL
/*
	Set the sold column to true for the Ford Escort RS2000
*/
UPDATE cars SET sold = TRUE
    WHERE brand = 'Ford'
        AND model = 'Escort RS2000';
/*
	Update the record for the Aston Martin DB4 with ID 14
		set the condition to 5
		and the price to 465000
*/

UPDATE cars SET
	condition = 5,
	price = 465000
WHERE 
	id = 14;
```

## Delete

Per eliminare i record da una tabella si usa l'operatore `DELETE` seguito dal
nome della tabella e dalla condizione per eliminare i record. Ad esempio:

```SQL
DELETE FROM cars
WHERE id = 14;
```

## Links

- [corso Scrimba](https://scrimba.com/intro-to-sql-c0aviq0aha/). Nel corso è
  usata la libreria PGLite per far girare un DB PostgreSQL. Nello scipt si crea
  un DB, si inseriscono i dati e si eseguono le query.

```js
import { PGlite } from "@electric-sql/pglite";
import fs from "fs";

(async () => {
    const db = new PGlite();
    await db.exec(`
        CREATE TABLE IF NOT EXISTS cars (
            id SERIAL PRIMARY KEY,
            brand TEXT,
            model TEXT,
            year INTEGER,
            price INTEGER,
            color TEXT,
            condition INTEGER,
            sold BOOLEAN
        );
        INSERT INTO cars (brand, model, year, price, color, condition, sold
        ) VALUES 
          ('Ford', 'Mustang', 1965, 45000, 'white', 4, false),
          ('Chevrolet', 'Camaro', 1970, 48000, 'red', 2, false),
          ('Dodge', 'Charger', 1969, 58000, 'black', 4, true),
          ('Porsche', '911', 1985, 85000, 'silver', 5, false),
          ('Jaguar', 'E-Type', 1967, 56000, 'green', 2, true),
          ('Jaguar', 'S-Type', 1963, 100000, 'dark green', 3, true),
          ('Jaguar', 'X-Type', 2001, 10000, 'black', 3, true),
          ('BMW', 'M3', 1990, 35000, 'green-yellow', 1, true),
          ('Ferrari', 'F355', 1997, 150000, 'red', 5, false),
          ('Ford', 'Mustang', 1967, 15000, 'dark blue', 0, false),
          ('Aston Martin', 'DB5', 1964, 595000, 'silver', 5, false),
          ('Aston Martin', 'DB4', 1960, 465000, 'light green', 5, false),
          ('Aston Martin', 'DBS', 1969, 99000, 'red', 2, false),
          ('Aston Martin', 'DB4', 1960, 425000, 'green', 3, false),
          ('Aston Martin', 'DB5', 1965, 649000, 'dark red', 5, false),
          ('Toyota', 'Supra', 1994, 68000, 'black', 4, true),
          ('Nissan', 'Skyline GT-R', 1999, 95000, 'blue', 5, false),
          ('Volkswagen', 'Beetle', 1963, 25000, 'yellow', 3, true),
          ('Lamborghini', 'Countach', 1989, 320000, 'red', 5, false),
          ('Rolls-Royce', 'Silver Shadow', 1975, 55000, 'white', 2, true),
          ('Bentley', 'Continental GT', 2005, 85000, 'black', 5, false),
          ('Maserati', 'GranTurismo', 2010, 75000, 'blue', 4, true),
          ('Alfa Romeo', 'Spider', 1986, 28000, 'red', 3, true),
          ('Ford', 'Mustang', 1965, 20000, 'dark red', 1, true),
          ('Lotus', 'Esprit', 1993, 62000, 'light yellow', 4, false),
          ('Triumph', 'Herald', 1965, 12500, 'cream', 3, true),
          ('Ford', 'Capri', 1983, 22000, 'blue', 2, false),
          ('Ford', 'Granada', 1977, 18000, 'black', 1, false),
          ('Volkswagen', 'Golf GTI', 1991, 12500, 'light green', 1, true),
          ('Chevrolet', 'Camaro', 1969, 54000, 'mint green', 5, true),
          ('Chevrolet', 'Corvette', 1967, 88000, 'red', 5, true),
          ('Chevrolet', 'Corvette C5', 2001, 32000, 'yellow', 4, true),
          ('Ferrari', 'Testarossa', 1988, 195000, 'red', 5, true),
          ('Ferrari', '360 Modena', 2003, 125000, 'silver', 5, true),
          ('Bentley', 'Arnage', 2001, 45000, 'black', 4, false),
          ('Bentley', 'Continental R', 1999, 68000, 'blue', 5, false),
          ('Jaguar', 'XJ220', 1994, 450000, 'silver', 5, false),
          ('Porsche', '911 Carrera', 1985, 85000, 'red', 5, false),
          ('Porsche', '911 Turbo', 1995, 12000, 'black', 1, false),
          ('Porsche', '944 Turbo', 1986, 48000, 'white', 4, true),
          ('Porsche', '356B', 1960, 265000, 'silver', 4, false),
          ('Bentley', 'T2', 1978, 52000, 'silver', 4, false);
`);

    // Load the SQL file
    const query = fs.readFileSync("query.sql", "utf8");

    // Executing simple queries for sections 1 - 3
    const response = await db.query(query);

    console.clear();
    console.table(response.rows);
})();
```

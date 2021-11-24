[Back to index](../README.md)

# [Json Server](https://github.com/typicode/json-server)

## Installazione
```bash
# Istallazione globale
$ npm install -g json-server

# Istallazione in un progetto
$ npm install json-server --save-dev

# senza istallazione
npx json-server mocks/db.json
```
Creare una cartella per il db: 
```json
// mocks/db.json
{
  "blogs": [
    {
      "title": "title example",
      "content": "content example",
      "author": "Your favorite author",
      "id": 1
    },
    {
      "title": "another title example",
      "content": "more content example",
      "author": "Your friend's favorite author",
      "id": 2
    },
    {
      "title": "title example number 3",
      "content": "even better content",
      "author": "me",
      "id": 3
    }
  ]
}
```
Eseguire lo script per avere il server online su `http://localhost:3000`:
```bash
$ json-server --watch mocks/db.json  --port 3004 --delay 500 --no-cors # -w -p -d
```

E' possibile mappare delle rotte tramite il [routing](https://github.com/typicode/json-server#add-custom-routes)
``` json
{
  "/api/*": "/$1",
  "/:resource/:id/show": "/:resource/:id",
  "/posts/:category": "/posts?category=:category",
  "/articles\\?id=:id": "/posts/:id"
}
// Start JSON Server with --routes option.
// 
// json-server db.json --routes routes.json
// Now you can access resources using additional routes.
//  /api/posts # → /posts
//  /api/posts/1  # → /posts/1
//  /posts/1/show # → /posts/1
//  /posts/javascript # → /posts?category=javascript
//  /articles?id=1 # → /posts/1
```

E' possibile puntare anche ad un file js:
```javascript
// index.js
const faker = require('faker');

module.exports = () => {
    faker.locale = "it"; // (Italian)

    const data = {
        customers: [],
        addresses: [],
    }

    for (let i = 1; i <= 100; i++) {
        let customer = {};
        let address = {};

        customer.id = i;
        let name = {};
        name.first = faker.name.firstName();
        name.last = faker.name.lastName();
        customer.name = name;
        data.customers.push(customer);

        address.id = i;
        address.address = faker.address.streetAddress();
        address.state = faker.address.stateAbbr();
        data.addresses.push(address);
    }

    return data;
}

// Si starta con: json-server index.js
// si avranno endpoint:
// http://localhost:3000/customers
// http://localhost:3000/addresses
``` 
## Links
- [file db multipli](https://billyyyyy3320.com/en/2019/07/21/create-json-server-with-multiple-files/) 
- [file db multipli 2](https://stackoverflow.com/questions/36836424/cant-watch-multiple-files-with-json-server)
- [proxy in angular](https://kaustubhtalathi.medium.com/mock-data-for-angular-5-applications-with-json-server-part-1-d377eced223b) OTTIMO! ***
- [angular e file multipli](https://kaustubhtalathi.medium.com/mock-data-for-angular-5-applications-with-json-server-part-2-final-427bd68005bb) OTTIMO! ***
- [server with json-server](https://dev.to/vcpablo/js-mocking-a-rest-api-with-json-server-368)
- [general strategies](https://dev.to/kettanaito/api-mocking-strategies-for-javascript-applications-48kl)
- [relazioni nei dati di json-server](https://keyholesoftware.com/2020/03/16/mock-restful-server-fast-with-json-server/)
- [json-server and JWT](https://itnext.io/building-a-fake-and-jwt-protected-rest-api-with-json-server-d7668ad36ee4)

## [Json generator](https://www.json-generator.com/)

# Generazione JSON

## [Faker](https://github.com/Marak/faker.js)
- [some example](https://zetcode.com/javascript/fakerjs/)
- [json-server & faker](https://spin.atomicobject.com/2018/10/08/mock-api-json-server/)
- [json-server & faker 2](https://itnext.io/how-to-generate-mock-data-using-faker-js-and-json-server-1d17007a08e4)
- [json-server & faker 3](https://hackernoon.com/back-end-data-and-api-prototyping-with-fakerjs-and-json-server-n5t36uw)

## [json-skema-faker](https://github.com/json-schema-faker/json-schema-faker)
- [articolo](https://medium.com/@housecor/rapid-development-via-mock-apis-e559087be066#.93d7w8oro)
- [FROM json to json schema](https://www.liquid-technologies.com/online-json-to-schema-converter)
- [FROM json schema TO json](https://www.liquid-technologies.com/online-schema-to-json-converter)
- [FROM json schema TO json](https://json-schema-faker.js.org/)
- [json-schema-faker](https://codesource.io/building-mock-apis-using-json-schema-faker-and-json-server/)
- [esempio generazione con json-skema-faker 1](https://www.freecodecamp.org/news/rapid-development-via-mock-apis-e559087be066/)
- [esempio generazione con json-skema-faker 1](https://www.carlserver.com/blog/post/create-fake-data-using-json-schema-faker)


L'idea è quella di generare i mock con tool online che permettono di avere velocemente dei json e poi generare il server mock con gli articoli *** (che permettono di avere la necessaria flessibilità)
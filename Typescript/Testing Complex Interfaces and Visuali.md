# Testing Complex Interfaces and Visualizations

E' un corso tenuto da "Pluralsight partner"

- [course](https://app.pluralsight.com/library/courses/js-friends-session-20/table-of-contents)

- Cypress è free ma ha un servizio di dashboard a pagamento che abilita la parallelizzazione dei test (passa da 16min a 4min): cypress carica i dati

- [happo](https://happo.io/) screenshot testing tool (a pagamento): visual diff is the tool..., visual regression test e verifica che i dati siano formattati propriamente

Come si testa la data visualisation

in cypress si finisce la catena dei comandi con `.happoScreenshot()`

In cypress si possono scatenare degli eventi su elementi tramite la funzione `.trigger('eventname')`
:

![events](docs/events.JPG)
![interactions](docs/interactions.JPG)
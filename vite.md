# VITE

Per creare un server statico basta aggiungere vite ad un progetto creato con `npm init -Y`:

```bash
> npm install --save-dev vite
```

all'interno del `package.json` si ha:
```json
{
    "scripts":{
        "dev": "vite dev
    }
}
```

Per avere la possibilità di importare codice js tramite la sintassi `import` all'interno di `main.js` si deve specificare l'attributo `type="module"`:
```html
<h1>Hello world</h1>
<script type="module" src="main.js"></script>
```
Per il css si usa o il normale link per lo stile globale
```html
<link rel="stylesheet" href="style.css">
```
Oppure per CSS modules si usa:
```html
<script type="module">
    import './main.js";
    import classes from './style.module.css'; // come CSS MODULES

    // si possono aggiungere le classi del CSS Modules tramite js
    document.querySelector("h1").className = classes.title
</script>
```
# Templates
Per creare un progetto tramite i template di vite si usa il comando:
```bash
> npm create vite
> npm install
> npm run dev

```

# [Static asset](https://vitejs.dev/guide/assets.html)


# [watch public directory for hot relaod](https://stackoverflow.com/questions/69626090/how-to-watch-public-directory-in-vite-project-for-hot-reload)
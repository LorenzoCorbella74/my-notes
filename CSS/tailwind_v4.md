# Tailwind CSS V4

E' un framework che permette di creare interfacce utente in modo rapido e semplice utilizzando `utility classi` predefinite dove ognuna definisce una sola regola css.

E' possibile usarlo in due modi:
1. mettendo nel file html inell'header lo <script src="https://cdn.tailwindcss.com"></script>
2. installandolo nel progetto tramite npm e installando la `Tailwind CSS Intellisense Extension` per VS Code e Prettier che analizza tutto il codice html e lo organizza per ogni tag con `npm install --save-dev prettier-plugin-tailwindcss` e poi creando il file `.prettierc` con il seguente contenuto:
```json
{
  "plugins": ["prettier-plugin-tailwindcss"],
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
}
```
Ed impostare nel formatter di VS Code Prettier come `Code formatter` predefinito.


## Spacing: Width, Height, Padding, Margin
Ricordando il Box model che prevede che se non sono impostate l'height e il width di un elemento, esso si adatterà al contenuto, e che il padding e il margin sono spaziature interne ed esterne all'elemento, 

L'idea è scrivere le regole partendo dal top left (perchè il width espande da sinistra a destra e l'height dall'alto in basso) e inserendo le proprietà in base al box model dall'interno all'esterno (quindi prima width & height poi padding, poi bordo e poi margini).

Per tutte le classi `-0` è `0px`, `-1` è `4px`, `-2` `8px`, `-7` è 7 volte `-1` (ossia 28px) e così via (con anche numeri non interi come -3.5 partendo da 0 fino a 96).

Vediamo alcune classi utili per gestire questi aspetti:
- `w-{size}`: imposta la larghezza (es. `w-[200px]` oppure `w-2/3` `w-full` per il 100% del contenitore)
- `h-{size}`: imposta l'altezza (es. `h-[100px]` per un'altezza di 100px, `h-screen` per l'altezza dell'intera finestra)
- max-w-{size}, min-w-{size}, max-h-{size}, min-h-{size}: imposta la larghezza/altezza massima/minima
- `p-{size}`: imposta il padding su tutti i lati (es. `p-4` per un padding di 1rem)
- `pt-{size}`, `pr-{size}`, `pb-{size}`, `pl-{size}`: imposta il padding su un lato specifico (top, right, bottom, left) oppure puoi usare `px-{size}` per il padding orizzontale (left e right) e `py-{size}` per il padding verticale (top e bottom)
- `m-{size}`: imposta il margin su tutti i lati (es. `m-4` per un margin di 1rem), 
- `mt-{size}`, `mr-{size}`, `mb-{size}`, `ml-{size}`:

## Borders e Background colors
- `border border-{size}`: imposta lo spessore del bordo (es. `border-2` per un bordo di 2px)
- `rounded-{size}`: imposta gli angoli arrotondati (es. `rounded-lg` per angoli grandi)
- `border border-{color}`: imposta il colore del bordo (es. `border-red-500` per un bordo rosso con il numero che va da 50, 100, 20,... fino a 900 per indicare la tonalità)
- `bg-{color}`: imposta il colore di sfondo (es. `bg-blue-200` per uno sfondo azzurro chiaro oppure `bg-[#ff5733]` per un colore personalizzato)

## Display
- `flex`: imposta il display flex
- `grid`: imposta il display grid
- `block`: imposta il display block
- `inline-block`: imposta il display inline-block
- `hidden`: nasconde l'elemento (display none)

## Typography
- `text-{size}`: imposta la dimensione del testo (es. `text-lg` per un testo grande, xs (12px), sm(14px), base (16px), lg (18px), xl (20px), 2xl(24px), ..9xl(128px))
- `font-{weight}`: imposta il peso del font (es. `font-bold` per un testo in grassetto -thin(100), -extralight(200) light(300), normal(400), medium(500), semibold(600), bold(700), extrabold(800), black (900))
- `text-{color}`: imposta il colore del testo (es. `text-gray-700` per un testo grigio scuro)
- `text-center`, `text-left`, `text-right`: allinea il testo (centrato, a sinistra, a destra)
- `italic`,  `underline` `line-through`, `uppercase`, `lowercase`, `capitalize`: stili del testo

## Layout & Positioning
- relative, absolute, fixed, sticky: imposta il posizionamento dell'elemento
- top-{size}, right-{size}, bottom-{size}, left-{size}: imposta la posizione dell'elemento rispetto al suo contenitore (es. `top-0` per posizionarlo in alto). Nota che un elemento con posizione `absolute` e `left-0` e `right-0` sarà posizionato al centro.
- `z-{number}`: imposta l'ordine di sovrapposizione degli elementi o z-index (es. `z-10` per portare l'elemento in primo piano).

## Flexbox 
- `flex-row`, `flex-col`: imposta la direzione degli elementi in un contenitore flex (orizzontale o verticale)
- `basis-{size}`: imposta la dimensione base di un elemento flex (es. `basis-1/3` per un terzo della larghezza del contenitore)
- `flex-grow`, flex-shrink: imposta la crescita e la riduzione degli elementi flex
- `flex-{number}`: imposta la crescita e la riduzione degli elementi flex (es. `flex-1` per far crescere l'elemento per occupare lo spazio disponibile)
- `justify-{alignment}`: allinea gli elementi lungo l'asse principale (es. `justify-center`, `justify-between`, `justify-around`)
- `items-{alignment}`: allinea gli elementi lungo l'asse trasversale (es. `items-center`, `items-start`, `items-end`)

## Grid
- `gap-{size}`: imposta lo spazio tra gli elementi in un contenitore flex o grid (es. `gap-4` per uno spazio di 1rem)
- `grid-cols-{number}`: imposta il numero di colonne in un contenitore grid (es. `grid-cols-3` per 3 colonne)
- `grid-rows-{number}`: imposta il numero di righe in un contenitore grid (es. `grid-rows-2` per 2 righe)   

## Hover & Focus States
- `hover:{class}`: applica una classe quando l'elemento è in stato di hover (es. `hover:bg-blue-500` per cambiare il colore di sfondo al passaggio del mouse)
- `focus:{class}`: applica una classe quando l'elemento è in stato di focus (es. `focus:outline-none` per rimuovere il contorno al focus), `focus:ring` per aggiungere un anello di focus  e `focus:ring-{color}` per impostare il colore dell'anello di focus.

## Animation
- `transition`: abilita le transizioni per le proprietà CSS
- `duration-{time}`: imposta la durata della transizione (es. `duration-300` per 300ms)

## Responsive design
- `sm:` corrisponde a min-width:640px, 
- `md:` corrisponde a min-width:768px,
- `lg:` corrisponde a min-width:1024px,
- `xl:` corrisponde a min-width:1280px,
- `2xl:` corrisponde a min-width:1536px
Applica le classi a specifiche larghezze di schermo (es. `md:text-lg` per un testo grande su schermi medi e più grandi). Ad esempio sul body si può mettere `class="bg-white sm:bg-gray-900"` per avere uno sfondo bianco finchè non si arriva a una larghezza di 640px, dopodiché lo sfondo diventa grigio scuro.


## Mobile first design
Tailwind CSS utilizza un approccio mobile-first per il design responsivo. Ciò significa che le classi senza prefissi di breakpoint si applicano a tutti i dispositivi, mentre le classi con prefissi di breakpoint (come `sm:`, `md:`, `lg:`, ecc.) si applicano solo a schermi di dimensioni specifiche e più grandi. Questo approccio consente di progettare prima per dispositivi mobili e poi adattare il layout per schermi più grandi.Quindi riassumendo:
- le classi di default si applicano a dispositivi mobile. Ad esempio rendiamo una immagine invisibile sui dispositivi mobili con `class="hidden sm:block "` Oppure per sezioni: `flex flex-col space-x-0 space-y-4 lg:flex-row lg:space-x-4 lg:space-x-0 max-w-[1024px]`
- aggiungere breakpoint per adattare il design a schermi più grandi (quando la larghezza cresce) 


## Dark mode
Si abilita con le classi:
- `dark:{class}`: applica una classe quando la modalità scura è attiva (es. `dark:bg-gray-800` per cambiare il colore di sfondo in grigio scuro in modalità scura)


# Links
- [corso PLurasight Tailwind CSS Foundations](https://app.pluralsight.com/library/courses/tailwind-css-foundations/)
- [Tailwind V4 in 1 ora](https://www.youtube.com/watch?v=6biMWgD6_JY)
- [Cheatsheet V3 / V4 ](https://kombai.com/tailwind/cheat-sheet/)
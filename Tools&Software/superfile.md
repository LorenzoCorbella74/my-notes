[Back to index](../README.md)

# Superfile
[superfile](https://superfile.dev/overview/) is a modern terminal file manager crafted with a strong focus on user interface, functionality, and ease of use.

In un terminale  digitare:

```bash
> spf

# per vedere tutti i path locations dei superfile files,
> spf pl
```

Per uscire `esc` o `q`.

## Navigation
Sono presenti più pannelli:
- sidebar `s`
- file tree che è il focus view di default
- proessesi `p`
- metadata `m`
- `:` per aprire la command execution bar (esc o crtl + c per tornare al focus view precedente)
- f per aprire o nascondere la preview windows (control + f per fare il toggle del footer)
- le frecce permettono di scorrere tra gli elementi selezionabili e di scendere nella gerarchia dei file (`right arrow` o `enter`) o di risalire (`left arrow` o `backspace`)
-   `p` to pin/unpin a folder or file
- `o` per sortare i file (name, date, size, type)


## Panels
- `n` per creare un panel 
- `tab` o `l` per spostarsi tra i panel a DX e  o `h` per spostarsi a SX
- `w`  per chiudere il panel corrente

# Selection and File operations
- `v` per selezionare un file o una cartella
- `ctrl + n` per creare un file o cartella se l'ultimo carattere è `/`
- `ctrl + r` rename cartella/file
- `ctrl + c` copy
- `ctrl + x` cut
- `ctrl + v` paste
- `ctrl + d` delete
- `ctrl + a` compress zip
- `ctrl + e` extract zip
- `e` open file with default app


## Links
- [Documentation](https://superfile.dev/getting-started/tutorial/)
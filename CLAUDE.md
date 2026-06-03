# DOMO-A Editorial — Notes per a Claude

## Què és aquest repo

Build compilat de Gatsby (no el codi font) desplegat a `editorial.domo-a.com`.
**No hi ha `src/`** — els canvis es fan directament sobre els fitxers compilats:
- `index.html` — HTML estàtic (SSR)
- `path---index-ad73836a3e08efd572fa.js` — dades dels projectes (GraphQL result)
- `component---src-pages-index-js-1678f5de000ff9cfcca4.js` — component React principal
- `static/` — imatges (jpg + webp)

## Arquitectura d'imatges

Cada imatge existeix en 5 mides: 275, 550, 1100, 1650, 2200px.
Formats: `.jpg` (fallback) + `.webp` (servit via `<picture><source type="image/webp">`).

Dos tipus de fitxers al `static/`:
- **Nom llegible** (`lalaboral-domoa-01-1100.webp`) — afegits manualment, comprimits a q75
- **Hash** (`3e9b64d...-93141.webp`) — processats per Gatsby des de Prismic, NO recomprimir

## Canvis implementats

- `index.html` + component React: imatges servides via `<picture>` + WebP (140 imatges)
- 85 WebP de projectes nombrats recomprimits: q82 → q75 (~27% menys), borisryzhy a q65
- `<title>` i `<meta description>` dinàmics per projecte via IntersectionObserver (script inline al final de `index.html`)
- Cada secció de projecte té `id` = slug, per tant `/#vida` funciona

## Com actualitzar metadescripcions

Les descripcions viuen a dos llocs que cal sincronitzar:

1. **`path---index-ad73836a3e08efd572fa.js`** — dades React:
   ```
   description:[{type:"paragraph",text:"TEXT AQUÍ"}]
   ```
2. **Script inline a `index.html`** — objecte `P` al final del body:
   ```js
   "slug":{t:"Títol",d:"Descripció aquí"}
   ```

## Projectes — títols i metadescripcions

| Slug | Títol | Metadescripció |
|------|-------|----------------|
| `lalaboral` | La Laboral | A newspaper about La Laboral de Tarragona, featuring photographers from the Km 0 collective. May 2024. |
| `borisryzhy` | Boris Ryzhy | Tribute to the poet Boris Ryzhy (Chelyabinsk 1974 - Yekaterinburg 2001). November 2023. |
| `insanealgorithm` | Insane Algorithm | A poster series denouncing the alienating bias of A.I. October 2023. |
| `bibliotecaclandestina` | Biblioteca Clandestina | *(pendent)* |
| `sandragrossfotografia` | Sandra Gross Fotografia | *(pendent)* |
| `fusa` | Fusa | *(pendent)* |
| `adamcurtisprogramme` | Adam Curtis Programme | Program and poster proposal for a cycle of projections around the figure of Adam Curtis. November 2019. |
| `vida` | Vida | Book written by Aleix Cort and illustrated by Jordi Abelló. October 2018. |
| `retratsbypepescoda` | Retrats, by Pep Escoda | Catalogue for Retrats, an exhibition about some of the best portrait photographers in the world. January 2018. |
| `xcentricposterseries3` | Xcèntric Poster Series 3 | Serie of posters designed for promote the film screenings of Xcèntric. January 2018. |
| `fulldominical` | Full Dominical | Design for the artist Jordi Abelló. December 2017. |
| `xcentricarxiupostcards` | Xcèntric Arxiu Postcards | Serie of postcards designed for promote the film screenings of Xcèntric Arxiu. October 2017. |
| `desvioposterserie` | Desvío Poster Serie | Serie of posters promoting Pep Escoda's photobook. October 2016. |
| `desviobypepescoda` | Desvío, by Pep Escoda | Photobook by Pep Escoda. October 2016. |
| `poligonsphotographyseries` | Polígons Photography Series | Photography project by DOMO—A |
| `poligon` | Polígon | *(pendent)* |
| `xcentricposterseries2` | Xcèntric Poster Series 2 | Serie of posters designed for promote the film screenings of Xcèntric. September 2017. |
| `xcentricposterseries1` | Xcèntric Poster Series 1 | Serie of posters designed for promote the film screenings of Xcèntric. September 2016. |
| `havanabike` | Havana Bike | Photobook for the photographer Pep Escoda. June 2016. |
| `museupicassobarcelona` | Museu Picasso Barcelona | Guide poster designed for the Master conferences Jaume Sabartés of Museu Picasso Barcelona. April 2016. |
| `posterarchetype` | Poster Archetype | *(pendent)* |
| `festadelgrafisme2015` | Festa del Grafisme 2015 | Proposal for Festa del Grafisme of Portbou. September 2015. |
| `blancfest` | Blanc Fest | Self promotion for Blanc Festival. December 2014. |
| `vinyesdedeu` | Vinyes de Deu | Book for Clos de l'Obac. April 2013. |
| `santalluciaposter` | Santa Llúcia Poster | Proposal for Premis Literaris 2012 Tarragona. December 2012. |
| `segellsportbou` | Segells Portbou | Proposal for Festa de Grafisme. September 2012. |
| `domoaposter` | DOMO–A poster | *(pendent)* |

## Com afegir un projecte nou

1. Afegir imatges jpg+webp a `static/` (5 mides: 275, 550, 1100, 1650, 2200px)
2. Editar `index.html`: afegir el bloc HTML del projecte (copiar patró existent)
3. Editar `path---index-ad73836a3e08efd572fa.js`: afegir les dades del projecte
4. Editar l'script inline de `index.html`: afegir entrada a l'objecte `P`

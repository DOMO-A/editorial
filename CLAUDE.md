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
- **Nom llegible** (`lalaboral-domoa-01-1100.webp`) — afegits manualment, comprimits a q75 (borisryzhy a q65)
- **Hash** (`3e9b64d...-93141.webp`) — processats per Gatsby des de Prismic, **NO recomprimir**

## Canvis implementats

- `index.html` + component React: imatges servides via `<picture>` + WebP (140 imatges)
- 85 WebP de projectes nombrats recomprimits: q82 → q75 (~27% menys), borisryzhy a q65
- `<title>` i `<meta description>` dinàmics per projecte via IntersectionObserver (script inline al final de `index.html`)
- Cada secció de projecte té `id` = slug → `/#vida` funciona
- Nav corregida: 27 projectes numerats 01–27 (els 4 nous estaven absents)
- OG tags i Schema.org JSON-LD afegits al `<head>` de `index.html`

## SEO — `<head>`

### OG tags
- `og:image` → `https://editorial.domo-a.com/static/lalaboral-domoa-01-1100.jpg` (1100×1555px)
- `fb:app_id` → `890599044371389`

### Schema.org JSON-LD
Dos blocs dins `<script type="application/ld+json">`:
1. **Organization / ProfessionalService** — dades de DOMO-A (adreça, telèfon, priceRange, logo, image)
2. **CollectionPage** — 27 entrades `VisualArtwork`, una per projecte

Per actualitzar dades de l'organització (adreça, telèfon, etc.) editar directament el JSON-LD al `<head>` de `index.html`.

## Com actualitzar metadescripcions

Les descripcions viuen a **dos llocs** que cal sincronitzar:

1. **`path---index-ad73836a3e08efd572fa.js`** — dades React:
   ```
   description:[{type:"paragraph",text:"TEXT AQUÍ"}]
   ```
   ⚠️ Compte amb cometes escapades dins el text JSON (`\"`)

2. **Script inline a `index.html`** — objecte `P` al final del `<body>`:
   ```js
   "slug":{t:"Títol",d:"Descripció aquí"}
   ```

3. **JSON-LD al `<head>` de `index.html`** — camp `"description"` de cada `VisualArtwork`

## Projectes — títols i metadescripcions

| Slug | Títol | Metadescripció |
|------|-------|----------------|
| `lalaboral` | La Laboral | A newspaper about La Laboral de Tarragona, featuring photographers from the Km 0 collective. 410×290mm, Cyclus Offset 80gr. May 2024. |
| `borisryzhy` | Boris Ryzhy | Tribute poster to the poet Boris Ryzhy (Chelyabinsk 1974 – Yekaterinburg 2001). 297×420mm, recycled newsprint, limited to 10 copies. November 2023. |
| `insanealgorithm` | Insane Algorithm | A poster series denouncing the alienating bias of A.I. algorithms. 297×420mm, Munken Pure Rough 120gr. October 2023. |
| `bibliotecaclandestina` | Biblioteca Clandestina | Cover design for the Biblioteca Clandestina collection by Clandestina Editorial. A unified visual system across a growing series of book covers. 2021 – Present. |
| `sandragrossfotografia` | Sandra Gross Fotografia | Poster series for photographer Sandra Gross, documenting sculptural and artistic objects through photography. 297×420mm, Arcodesign 180gr. |
| `fusa` | Fusa | Poster design for Fusa, an exhibition of concert photography by Carles Llop. 297×420mm, Arcodesign 180gr. |
| `adamcurtisprogramme` | Adam Curtis Programme | Program and poster proposal for a cycle of film projections around the figure of Adam Curtis. 840×152mm, Munken Lynx Rough 120gr. November 2019. |
| `vida` | Vida | Book design for Vida, 101 poems at the intersection of science and poetry by Aleix Cort, illustrated by Jordi Abelló. Limited edition of 101 copies. October 2018. |
| `retratsbypepescoda` | Retrats, by Pep Escoda | Catalogue design for Retrats, an exhibition of portrait photography by Pep Escoda. 52 pages on Artic Volume White 170gr. January 2018. |
| `xcentricposterseries3` | Xcèntric Poster Series 3 | Third poster series for the Xcèntric film screenings at CCCB Barcelona. Large format 524×841mm, Arcodesign 180gr. January 2018. |
| `fulldominical` | Full Dominical | Newspaper design for artist Jordi Abelló. 500 copies on Cyclus Offset 115gr, typeset in Canela and Basetica. December 2017. |
| `xcentricarxiupostcards` | Xcèntric Arxiu Postcards | Postcard series for the Xcèntric Arxiu film screenings at CCCB Barcelona. 120×180mm, Arcodesign 350gr. October 2017. |
| `desvioposterserie` | Desvío Poster Serie | Poster series promoting Desvío, the photobook by Pep Escoda. 500×700mm, Conqueror Wove High White 180gr. October 2016. |
| `desviobypepescoda` | Desvío, by Pep Escoda | Book design for Desvío, a photobook by Pep Escoda. 96 pages, limited edition of 150 copies, Fedrigoni papers. October 2016. |
| `poligonsphotographyseries` | Polígons Photography Series | Fine art print series by DOMO–A. 250 copies on Hahnemühle Photo Silk Baryta 310gr, 180×180mm. |
| `poligon` | Polígon | Large-format newspaper combining Arabic, Chinese and Latin typefaces. 780×578mm, recycled newsprint 52gr, 400 copies. October 2016. |
| `xcentricposterseries2` | Xcèntric Poster Series 2 | Second poster series for the Xcèntric film screenings at CCCB Barcelona. 420×594mm, Arcodesign 180gr. September 2016. |
| `xcentricposterseries1` | Xcèntric Poster Series 1 | First poster series for the Xcèntric film screenings at CCCB Barcelona. 420×594mm, Arcodesign 180gr. September 2016. |
| `havanabike` | Havana Bike | Book design for Havana Bike, a photobook by Pep Escoda. 84 pages, limited edition of 150 copies, Novatech Ultimatt and Keyacolour papers. June 2016. |
| `museupicassobarcelona` | Museu Picasso Barcelona | Poster design for the Jaume Sabartés master conferences at Museu Picasso Barcelona. 420×594mm, Cyclus Offset 140gr. April 2016. |
| `posterarchetype` | Poster Archetype | Poster for OFFF Barcelona 2016. A visual exploration of the archetype of creation — the algorithmic genesis of the universe where light reflects in darkness. 445×594mm, Monotype Grotesque. |
| `festadelgrafisme2015` | Festa del Grafisme 2015 | Poster proposal for the Festa del Grafisme of Portbou. 594×841mm, Arcodesign 180gr, Akzidenz Grotesk. September 2015. |
| `blancfest` | Blanc Fest | Self-promotion newspaper for Blanc Festival. Large format 750×520mm, 400 copies on recycled newsprint 52gr. December 2014. |
| `vinyesdedeu` | Vinyes de Deu | Book design for Vinyes de Deu, a 184-page volume for winery Clos de l'Obac. Typeset in Minion Pro and DIN 1451. April 2013. |
| `santalluciaposter` | Santa Llúcia Poster | Poster proposal for the Premis Literaris Santa Llúcia 2012 of Tarragona. 420×297mm, Arco Design 180gr, Futura BT. December 2012. |
| `segellsportbou` | Segells Portbou | Stamp design proposal for the Festa del Grafisme of Portbou. 33×76mm, Futura BT. September 2012. |
| `domoaposter` | DOMO–A poster | A graphic experiment tied to DOMO–A's founding metaphor: the Antarctic expedition as the blank page, the origin of everything. 595×841mm, Bauer Bodoni. June 2012. |

## Com afegir un projecte nou

1. Afegir imatges jpg+webp a `static/` (5 mides: 275, 550, 1100, 1650, 2200px), comprimir webp a q75
2. Editar `index.html`: afegir el bloc HTML del projecte i l'ítem de nav (copiar patró existent, renumerar)
3. Editar `path---index-ad73836a3e08efd572fa.js`: afegir node a `allPrismicDocument.edges` i a `allImageSharp.edges`
4. Editar l'script `P` al final del `<body>` de `index.html`: afegir `"slug":{t:"Títol",d:"Desc"}`
5. Editar el JSON-LD al `<head>`: afegir entrada `VisualArtwork` a `hasPart`

## Advertències

- **Mai usar `sed` per substituir text amb caràcters especials** (×, –, é, etc.) al path JS — trenca la sintaxi JSON. Usar Python amb `str.replace()` i `json.dumps(..., ensure_ascii=False)`.
- **Mai usar regex amb `re.subn(repl_string)` quan el text de reemplaçament conté `\u`** — Python ho interpreta com escape. Usar lambda: `re.subn(pattern, lambda m: nou_text, s)`.
- El path JS és un fitxer JavaScript minificat d'una sola línia. Qualsevol error de sintaxi trenca el slider i el sticky nav.

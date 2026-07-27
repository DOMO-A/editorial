# DOMO-A Editorial — Notes per a Claude

## Què és aquest repo

HTML estàtic pla desplegat a `editorial.domo-a.com`. **Ja NO és Gatsby** — no hi ha cap build ni framework actiu, `main` és directament el codi desplegat.
Els noms de classe (`src-components-Project----Project-module---wrap---h6rD-`, etc.) són residus de quan es generava amb Gatsby, però no signifiquen que Gatsby corri enlloc.
**No hi ha `src/`** — els canvis es fan directament sobre aquests fitxers:
- `index.html` — HTML estàtic (SSR) + tota la interactivitat en vanilla JS inline
- `static/` — imatges (jpg + webp)

**08-07-2026 (commit `214cc84`): runtime React/Gatsby eliminat.** La hidratació React reconstruïa l'arbre d'imatges al muntar i trencava `loading="lazy"` natiu (les ~140 imatges es carregaven totes de cop). S'han tret els 5 `<script src>` de bundles + webpack manifest + neteja de Service Worker. L'HTML SSR ja era correcte i ara es queda intacte.

Tota la interacció (dropdown de nav, panell "About", slider, títol/descripció dinàmics) és **vanilla JS inline dins `index.html`** — no hi ha component React actiu.

**Fitxers `.js` orfes al root** (`app-*.js`, `commons-*.js`, `component---*.js`, `path---*.js`, inclòs `path---index-ad73836a3e08efd572fa.js`) — **ja no es carreguen enlloc**. Continuen físicament al repo però no els edita ningú: qualsevol canvi allà no té efecte al lloc en producció.

## Arquitectura d'imatges

Cada imatge existeix en 5 mides: 275, 550, 1100, 1650, 2200px.
Formats: `.jpg` (fallback) + `.webp` (servit via `<picture><source type="image/webp">`).

Dos tipus de fitxers al `static/`:
- **Nom llegible** (`lalaboral-domoa-01-1100.webp`) — afegits manualment, comprimits a q75 (borisryzhy a q65)
- **Hash** (`3e9b64d...-93141.webp`) — processats per Gatsby des de Prismic, **NO recomprimir**

## Canvis implementats

- `index.html`: imatges servides via `<picture>` + WebP (140 imatges)
- 85 WebP de projectes nombrats recomprimits: q82 → q75 (~27% menys), borisryzhy a q65
- `<title>` i `<meta description>` dinàmics per projecte via IntersectionObserver (script inline al final de `index.html`)
- **27-07-2026:** sèrie de fixes del bug "es queda enganxat a `#sandragrossfotografia`" (títol i hash), 3 causes independents:
  1. L'observer disparava a l'estat inicial sense scroll de l'usuari → flag `userScrolled` fins al primer `scroll` real.
  2. Nav 03/04 (Biblioteca Clandestina / Insane Algorithm) amb el text bescanviat respecte l'ordre real de seccions al DOM → els `href` (assignats per índex) apuntaven creuats.
  3. El clic al nav feia `upd()` bé, però l'`IntersectionObserver` seguia actiu durant l'scroll animat (`scroll-behavior:smooth`) i el sobreescrivia si de pas travessava la banda d'una altra secció → "nav guard" de 1.2s que bloqueja l'observer durant la transició del clic, cancel·lat a l'instant si l'usuari interromp amb wheel/touch ([index.html:622-650](index.html)).
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

Les descripcions viuen a **dos llocs dins `index.html`** que cal sincronitzar (des del 08-07-2026 ja NO cal tocar `path---index-ad73836a3e08efd572fa.js` — orfe, sense efecte):

1. **Script inline a `index.html`** — objecte `P` al final del `<body>`:
   ```js
   "slug":{t:"Títol",d:"Descripció aquí"}
   ```

2. **JSON-LD al `<head>` de `index.html`** — camp `"description"` de cada `VisualArtwork`

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
3. Editar l'script `P` al final del `<body>` de `index.html`: afegir `"slug":{t:"Títol",d:"Desc"}`
4. Editar el JSON-LD al `<head>`: afegir entrada `VisualArtwork` a `hasPart`

## Advertències

- **Mai usar `sed` per substituir text amb caràcters especials** (×, –, é, etc.) dins l'objecte `P` o el JSON-LD a `index.html` — trenca la sintaxi JSON/JS. Usar Python amb `str.replace()` i `json.dumps(..., ensure_ascii=False)`.
- **Mai usar regex amb `re.subn(repl_string)` quan el text de reemplaçament conté `\u`** — Python ho interpreta com escape. Usar lambda: `re.subn(pattern, lambda m: nou_text, s)`.
- Slider i sticky nav ara són vanilla JS inline a `index.html` (no depenen de cap fitxer `.js` extern). Qualsevol error de sintaxi als scripts inline trenca'ls igualment — provar sempre en local abans de desplegar.

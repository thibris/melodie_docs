# Przepisy (Recipes)

Two parallel implementations of a recipe browser exist side by side, linked from [header.php](../header.php) as "v1" and "v2".

## v1 — PDF-based

Files: [przepisy.php](../przepisy.php), [przepisy_view.php](../przepisy_view.php)

- [przepisy.php](../przepisy.php) lists every `*.pdf` file under `przepisy/` (not checked into the repo) and links each to `przepisy_view.php?file=<name>`.
- [przepisy_view.php](../przepisy_view.php) streams a single PDF inline:
  - Reads `file` from the query string, URL-decodes it, and strips any path via `basename()`.
  - Validates the result against `/^[^\/\\]+\.pdf$/iu` to reject anything that isn't a plain `.pdf` filename (defends against directory traversal).
  - Serves it with `Content-Type: application/pdf` and `Content-Disposition: inline`.

## v2 — Markdown-based

Files: [przepisy2.php](../przepisy2.php), [przepisy_parser.php](../przepisy_parser.php), `przepisy2/*`

- [przepisy2.php](../przepisy2.php) lists subfolders of `przepisy2/` as selectable recipes in a dropdown (`?katalog=<folder name>`), with a sort option (`?sort=az|za|new|old` by name or folder modification time).
- For the selected recipe folder it renders:
  - `lista.md` (ingredient list) in a left sidebar.
  - `przepis.md` (preparation steps) in the main pane.
  - Any `jpg/jpeg/png/gif/webp` files in the folder as a photo gallery below the steps.
- Markdown is converted to HTML using [przepisy_parser.php](../przepisy_parser.php), a vendored copy of the third-party [Parsedown](http://parsedown.org) library (v1.8.0, MIT-style license — unmodified, do not edit directly).
- Layout is a fixed-height two-pane view (list + content) with independent scrolling and a responsive fallback to a stacked layout under 800px.

### Recipe folder format

Each subfolder of [przepisy2/](../przepisy2/) is one recipe, named in Polish (may contain spaces/diacritics), containing:

```
przepisy2/<recipe name>/
  lista.md     # Markdown bullet list of ingredients
  przepis.md   # Markdown bullet list of preparation steps
  *.jpg|png... # optional photos (shown in a gallery)
```

Example — [przepisy2/fuczki/lista.md](../przepisy2/fuczki/lista.md) and [przepisy2/fuczki/przepis.md](../przepisy2/fuczki/przepis.md) — are plain `-`-prefixed Markdown lists with no other structure (no front matter, no headings required).

Current recipes in the repo: chaczapuri adżarskie, fuczki, gofry jabłkowo-marchewkowe, lahmacun, pierniki Norymberskie, pikantna zupa-krem z ziemniaków, polewka jurajska, risotto z dynią, tarta dyniowa z ricottą i pomarańczą, tarta spód, zupa cebulowa, zupa Minestrone z młodą kapustą.

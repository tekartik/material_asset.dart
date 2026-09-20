---
name: tekartik-material-asset-fonts
description: >-
  Use when a legacy Dart web app links fonts through the deprecated,
  asset-only tekartik_material_asset package: the material-icons.css /
  MaterialIcons-Regular.woff icon font, the roboto, roboto-slab and
  roboto-condensed web fonts, and the packages/tekartik_material_asset/font/...
  stylesheet urls. It has no Dart API - do not import material_asset.dart.
---

# Material web font assets (tekartik_material_asset)

`tekartik_material_asset` is **deprecated** and ships **assets only**: a few
woff web fonts and their css under `lib/font/`, meant to be linked from the
`index.html` of a Dart web app. `lib/material_asset.dart` is an empty
placeholder library (`library;` and a comment), so there is nothing to import
and no Dart code to write.

## Guidelines

* Do not use it in new code. For a new web app link the fonts from Google Fonts
  (`https://fonts.googleapis.com/icon?family=Material+Icons`) or vendor the
  files into `web/`; in Flutter, declare fonts in `pubspec.yaml` instead (this
  package only ships `.woff`, which Flutter does not load).
* Never `import 'package:tekartik_material_asset/material_asset.dart'`: the
  library is a placeholder and exports nothing. The package is a
  dev/asset dependency:
  ```yaml
  dev_dependencies:
    tekartik_material_asset:
      git:
        url: https://github.com/tekartik/material_asset.dart
  ```
  (a `dependencies:` entry works the same; the repo README shows that form).
* Everything under `lib/` is served by `build_web_compilers` / `webdev` at
  `packages/tekartik_material_asset/<path under lib>`, so the stylesheet urls
  in `web/index.html` are:
  * `packages/tekartik_material_asset/font/material-icons/material-icons.css`
  * `packages/tekartik_material_asset/font/roboto/roboto-fonts.css`
  * `packages/tekartik_material_asset/font/roboto-slab/roboto-slab-fonts.css`
  * `packages/tekartik_material_asset/font/roboto-condensed/roboto-condensed-fonts.css`
  Each css `@font-face` points at the `.woff` next to it, so keep the whole
  folder, never copy the css alone.
* Font families and weights actually shipped:
  * `'Material Icons'` (400) - `MaterialIcons-Regular.woff`, plus the
    `.material-icons` class (24px inline-block glyphs, `font-feature-settings:
    'liga'` so icon names like `face` render as ligatures).
  * `'Roboto'` normal + bold - `Roboto-Regular-webfont.woff`,
    `Roboto-Bold-webfont.woff`.
  * `'Roboto Slab'` 400 + 700, `'Roboto Condensed'` 400 + 700 (latin subset).
  No italic face is shipped (the italic `@font-face` rules are commented out
  in `roboto-fonts.css`), so italic text is synthesised by the browser.
* The `.md-dark`, `.md-inactive` helper classes used in `example/` come from
  the upstream Google css and are **not** defined here: define them yourself
  (`color`/`opacity`) or drop them.
* The woff files are vendored from the git submodules listed in
  `doc/material_asset-info.txt` (google/material-design-icons and
  zhifeichen/RobotoWebfonts); do not regenerate them by hand.
* Adding the dependency alone does nothing: nothing is injected, there is no
  loader. The `<link rel="stylesheet">` in the page is the whole integration.

## Examples

### Icon font from the package

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>icons</title>
  <link rel="stylesheet"
        href="packages/tekartik_material_asset/font/material-icons/material-icons.css">
</head>
<body>
<i class="material-icons">face</i>
<i class="material-icons">&#xE87C;</i>
<script defer src="main.dart.js"></script>
</body>
</html>
```

### Roboto Slab as the page font

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>text</title>
  <link rel="stylesheet"
        href="packages/tekartik_material_asset/font/roboto-slab/roboto-slab-fonts.css">
  <style>
    body { font-family: "Roboto Slab", serif; }
    h1 { font-weight: 700; }
  </style>
</head>
<body>
<h1>Title</h1>
<p>Lorem ipsum</p>
</body>
</html>
```

### The non deprecated alternative: Google Fonts

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>icons</title>
  <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
</head>
<body>
<i class="material-icons">face</i>
</body>
</html>
```

### Serving a page that uses the assets

```bash
# from the web app package, with build_runner/build_web_compilers
dart pub global activate webdev
webdev serve            # packages/tekartik_material_asset/font/... is served from lib/
```

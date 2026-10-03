# GOV.UK frontend — per-component mirror (retrieval corpus)

A scoped, shape-uniform mirror of [alphagov/govuk-frontend](https://github.com/alphagov/govuk-frontend),
one consolidated file per component, built so that the component **options data**
(parameters, nullability, defaults) surfaces in retrieval top-hits instead of being
outranked by template partials and SCSS (the failure mode the Corpus Hub G7 gate
measured on the unshaped repo).

## Provenance

- Upstream: `alphagov/govuk-frontend` (the monorepo), package
  `packages/govuk-frontend`, version **6.5.1**
- Pinned SHA: **`283cc58ead97f3e3379199976709713914e00b05`**
  (commit dated 2026-10-01, "Merge pull request #7442 from alphagov/dependabot/npm_and_yarn/rollup-d20baa139f")
- Source path: `packages/govuk-frontend/src/govuk/components/<name>/`
- Components mirrored: **39** (this is the true directory count at the pinned
  SHA; a bare `ls | wc -l` on the components dir reports 43 because it also
  counts 4 non-directory entries: `_index.import.scss`, `_index.scss`,
  `components.template.test.js`, `components.unit.test.js`)

## What each file is

`components/<name>.txt` is a verbatim concatenation, in this fixed order:

```
GOV.UK Design System — component <name>
===== README =====
<contents of the component's README.md>
===== OPTIONS (name.yaml) =====
<contents of the component's <name>.yaml — the macro options/params data>
===== MACRO (macro.njk) =====
<contents of the component's macro.njk>
```

- All contents are byte-verbatim from upstream; whitespace unchanged.
- Every file has all three separator lines. If a component lacks one of the
  files upstream, the section reads `<absent in upstream>` instead of being
  skipped, so the shape is uniform across the set. (At the pinned SHA exactly
  one section is absent: `password-input` has no README.md.)
- Excluded by design: SCSS, `*.test.js` fixtures, and everything else in the
  monorepo (docs/, dist/, other packages, .github/, app files).

## Licence

Upstream is MIT — `LICENSE.txt` here is a verbatim copy of the upstream repo
root file ("The MIT License (MIT)", Copyright (C) 2017 Crown Copyright
(Government Digital Service)). MIT is corpus-compatible (approach and copy).

## Regenerating

Against a local clone of govuk-frontend at the pinned SHA:

```sh
SRC=/path/to/govuk-frontend
COMP="$SRC/packages/govuk-frontend/src/govuk/components"
OUT=/tmp/mirror-govuk && mkdir -p "$OUT/components" && cd "$COMP"
for d in */; do
  d=${d%/}
  {
    printf 'GOV.UK Design System — component %s\n' "$d"
    printf '===== README =====\n'
    if [ -f "$d/README.md" ]; then cat "$d/README.md"; else printf '<absent in upstream>\n'; fi
    printf '===== OPTIONS (%s.yaml) =====\n' "$d"
    if [ -f "$d/$d.yaml" ]; then cat "$d/$d.yaml"; else printf '<absent in upstream>\n'; fi
    printf '===== MACRO (macro.njk) =====\n'
    if [ -f "$d/macro.njk" ]; then cat "$d/macro.njk"; else printf '<absent in upstream>\n'; fi
  } > "$OUT/components/$d.txt"
done
cp "$SRC/LICENSE.txt" "$OUT/LICENSE.txt"
```

To re-pin: `git -C "$SRC" checkout <new-sha>`, rerun the loop, update the SHA
above, and update the component count if it changed.

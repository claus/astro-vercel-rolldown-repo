# @astrojs/vercel 11.0.13: functions crash with "Cannot find native binding" (rolldown)

Minimal reproduction: Astro 7.3.8 + `@astrojs/vercel` 11.0.13, one prerendered page and one
on-demand page (`src/pages/ssr.astro`, `prerender = false`).

## On Vercel

Deploy this repository. `/` works; `/ssr` returns `500 FUNCTION_INVOCATION_FAILED` and the
function log shows `Error: Cannot find native binding` from `rolldown`.

## Locally (no Vercel needed)

```sh
npm install
npm run build
grep -n 'import "rolldown"' .vercel/output/functions/_render.func/.vercel/output/server/entry.mjs
cp -R .vercel/output/functions/_render.func /tmp/fn
cd /tmp/fn && node --input-type=module -e "await import('./.vercel/output/server/entry.mjs')"
```

The packaged function imports `rolldown`, but its platform binding is not traced into the function
(`node_modules/@rolldown/` only contains `pluginutils`), so importing the entry throws
`Cannot find native binding`.

With `@astrojs/vercel@11.0.12` the entry does not import `rolldown` and loads fine.

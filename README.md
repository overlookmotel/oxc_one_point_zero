# Oxc 1.0 - 5 item wish list

A [Slidev](https://sli.dev) presentation about my personal "wish list" for before Oxc reaches 1.0.

## Usage

```sh
pnpm install
pnpm dev
```

## Export as static site

```sh
pnpm build
```

Output is written to `dist/`. To preview it locally:

```sh
pnpm dlx serve dist
```

If hosting under a sub-path, set the `BASE_PATH` environment variable:

```sh
BASE_PATH=/oxc_one_point_zero/ pnpm build
```

## Export as PDF

```sh
pnpm export
```

Output is written to `slides-export.pdf`. Requires `playwright-chromium`
(already in `devDependencies`).

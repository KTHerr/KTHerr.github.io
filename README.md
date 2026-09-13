# kathryn-biology.com

Source for Kathryn Herr's academic website, built with [Jekyll](https://jekyllrb.com/).
All site source lives in the [`Website/`](Website) directory.

## Requirements

- Ruby (3.x recommended) and [Bundler](https://bundler.io/) (`gem install bundler`)
- A UTF-8 locale (the default on virtually every system) — the bibliography parser needs
  it to read accented characters correctly. If you ever see an
  `invalid byte sequence in US-ASCII` error, prefix commands with
  `LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8`.

## Local development

```sh
cd Website
bundle install       # install Ruby gems (first time, or after editing the Gemfile)
bundle exec jekyll serve
```

Then open [http://localhost:4000](http://localhost:4000). The server watches for file
changes and rebuilds automatically; refresh the browser to see updates.

## Building

```sh
cd Website
bundle exec jekyll build
```

This outputs the static site to `Website/_site/`, which is not committed to the repo
(see `.gitignore`) — it's regenerated on every build/deploy.

## Deployment

The site is hosted on **GitHub Pages**, built and published by
[`.github/workflows/website.yml`](.github/workflows/website.yml) on every push to
`main` (or manually, via *Run workflow* in the Actions tab). The workflow builds with
Jekyll in `Website/` and uploads `Website/_site` as the Pages artifact; nothing is
committed back to the repo.

For this to work, **Settings → Pages → Build and deployment → Source** must be set to
**GitHub Actions** (not "Deploy from a branch").

### Custom domain

The site serves from `kathryn-biology.com`. Two things keep that working, and both
matter:

1. [`Website/CNAME`](Website/CNAME) contains the bare domain. Jekyll copies it into
   `_site`, which is what tells Pages the custom domain. **If this file is deleted the
   domain stops resolving to the site on the next deploy.**
2. DNS at the registrar needs, for the apex domain, four `A` records pointing at
   `185.199.108.153`, `185.199.109.153`, `185.199.110.153` and `185.199.111.153`
   (and/or the equivalent `AAAA` records), plus a `CNAME` record for `www` pointing at
   `ktherr.github.io`. Once DNS resolves, tick **Enforce HTTPS** in Settings → Pages.

`url:` in `Website/_config.yml` must match the domain — it is what social-share and
canonical links are built from.

### Gemfile.lock platforms

The workflow runs on `ubuntu-latest`, and `bundler-cache: true` installs from the
lockfile in frozen mode. `Gemfile.lock` therefore has to list `x86_64-linux` under
`PLATFORMS`, or the build fails with *"your bundle only supports platforms …"*. If you
ever regenerate the lockfile on a Mac, re-add it:

```sh
cd Website
bundle lock --add-platform x86_64-linux
```

### Netlify

[`Website/netlify.toml`](Website/netlify.toml) is left over from a previous Netlify
setup and is not used by the GitHub Pages deploy. If no Netlify site is still connected
to this repo, the file can be deleted.

## Content

The site is a single scrolling page (`Website/_layouts/home.html`, rendered by
`Website/index.md`) with nine sections, in order: Academic path, Research, Teaching,
Skills & Tools, Publications, Funding & Awards, Talks & Posters, Outreach, and Other
Notable Research & Lab Experience. The six tips of the circular phylogeny in the hero,
and the top-nav links, jump into those sections.

Most of the content lives in `Website/_data/*.yml`:

| File | Section |
| --- | --- |
| `research.yml` | Research areas |
| `teaching.yml` | Courses taught |
| `teaching_training.yml` | Teaching training |
| `awards.yml` | Funding & awards |
| `presentations.yml` | Talks & posters |
| `outreach.yml` | Outreach |
| `experience.yml` | Other research & lab experience |
| `education.yml` | Academic path timeline |
| `nav.yml` | Top-nav links |

Publications come from `Website/_bibliography/papers.bib`, rendered via
[jekyll-scholar](https://github.com/inukshuk/jekyll-scholar) through
`Website/_layouts/bib.html`. The bio paragraph under the name is in
`Website/index.md`, and the headline credentials beneath it are in
`Website/_layouts/home.html`.

The study-systems cladogram is `Website/_includes/cladogram.html` — the branch SVG and
the tip list are separate, so if you add or remove a tip you have to adjust both (the
comment at the top of that file explains the geometry).

## Theme

The site ships light and dark themes. With no stored preference it follows the reader's
OS setting (`prefers-color-scheme`); the circular button in the top-right of the nav
pins the opposite of whatever is showing and remembers it in `localStorage`. A short
script in `Website/_includes/head.html` applies a stored choice before first paint so
the page never flashes the wrong theme.

Colours are defined once, as custom properties, at the top of `Website/css/main.css`:
a raw palette, then a block of semantic roles (`--heading`, `--link`, `--accent`,
`--surface-dark`, `--badge-bg`, and so on). **The rest of the sheet only ever references
the semantic roles** — nothing below that block should hard-code a colour. The dark
theme restates just those roles, in two identical blocks (one for the media query, one
for the `data-theme="dark"` attribute); if you edit one, edit the other. Printing always
forces the light values, whatever the screen is set to.

## Email address

The address is never written into the built HTML as a single string. `_config.yml`
holds it split in two (`email_user` / `email_domain`);
`Website/_includes/email-link.html` emits each half reversed in a data attribute, and a
short script in `Website/_layouts/default.html` reassembles the `mailto:` link in the
browser. Nothing in the page source matches the `user@host` pattern harvesters scan
for. To change the address, edit the two `_config.yml` keys.

## CV

The CV linked in the nav is `Website/assets/pdf/CV_KathrynHerr_2026.pdf` — replace that
file (and update the link in `Website/_data/nav.yml` and the hero button in
`Website/_layouts/home.html` if the filename changes) to update it.

## Credits

The site began as the [Dumbarton](https://github.com/tcbutler320/Dumbarton) Jekyll
theme by **tcbutler320** and has since been heavily modified — the layouts, includes,
stylesheet, illustrations and content are largely rewritten. The original theme was
released under the MIT licence:

> The MIT License (MIT) — Copyright (c) 2020 tcbutler320
>
> Permission is hereby granted, free of charge, to any person obtaining a copy of this
> software and associated documentation files (the "Software"), to deal in the Software
> without restriction, including without limitation the rights to use, copy, modify,
> merge, publish, distribute, sublicense, and/or sell copies of the Software, and to
> permit persons to whom the Software is furnished to do so, subject to the following
> conditions:
>
> The above copyright notice and this permission notice shall be included in all copies
> or substantial portions of the Software.
>
> THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED,
> INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A
> PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
> HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF
> CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE
> OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

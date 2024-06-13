+++
title = "Generating This Site"
date = 2024-06-12
description = "An overview of how this website is generated and published."
+++

Currently this site is generated with Zola, styled with Pico CSS, and published
on GitHub Pages. It replaces a previous half-hearted attempt at creating a site
using Jekyll.

The source for the site can be found on [GitHub](https://github.com/dlittleton/dlittleton.github.io).

## Resources

- [Zola](https://www.getzola.org/)
- [Pico CSS](https://picocss.com/)

## Why Zola?

My primary complaint with Jekyll was that I was never quite sure that my local
setup matched what was being used on GitHub. As someone who doesn't use Ruby
often I found it cumbersome to keep Jekyll configured and updated on Windows.

While searching for alternatives I mostly prioritized ease of setup. My
customization needs are pretty low and I'm fairly certain any of the popular
static site generators would easily meet my requirements. Browsing this [list of
generators](https://jamstack.org/generators/) I ended up with Hugo and Zola on
my short list. Both are easily installed as a single application without much
fuss. The tiebreaker for my decision was that Zola is written in Rust and I've
been playing around with learning Rust lately.

## Styles

I chose Pico CSS after a brief overview of classless CSS options. It was
downloaded and installed manually to the `static` directory of the site source
as suggested by the [documentation](https://picocss.com/docs#install-manually).

## Publishing

The site is published using a GitHub Actions workflow. I used the Zola [default
workflow](https://www.getzola.org/documentation/deployment/github-pages/) as a
starting point. However, the default workflow publishes the site by committing
to the `gh-pages` branch which didn't quite work as I wanted for this
repository. The source for this site is located in my user page repository, so
by default it expected to find the published site in `main`. Although I could
change the expected branch in the settings, it felt awkward to have commits to
`main` produce a second commit to `gh-pages`.

Instead I opted to set the `BUILD_ONLY` flag in the Zola step and move publishing to separate steps using Pages specific actions. Putting it all together I ended up with the following.

```yaml
name: Deploy to Pages

on:
  push:
    branches:
      - main
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build_and_publish:
    name: Publish site
    runs-on: ubuntu-latest
    steps:
      - name: Checkout main
        uses: actions/checkout@v4
      - name: Zola build
        uses: shalzz/zola-deploy-action@v0.18.0
        env:
          BUILD_ONLY: true
      - name: Configure Pages
        uses: actions/configure-pages@v5
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: "public"
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

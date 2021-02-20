---
title: The creation of this blog
date: 2021-02-17 16:25:29
tags:
---

# What is this post about?

I was looking for an easy way to document my research or practical experience, or even just my thoughts in form of a blog. Its primarily purpose would be to serve as a quick recap for my future self. Also a git integration would be great.

So the following criteria should be met:
- Write&Format using markdown.
- Quick deploy on [github pages](https://pages.github.com/).

Based on that it was obvious, that I need some .md to .html generator. There are different tools for this. The one that github uses by default is _jekyll_.

But I ended up using [hexo](https://hexo.io/). Why? Mainly because I liked [this theme](https://github.com/probberechts/hexo-theme-cactus). It was love form the first sight, and apart from that, hexo meets my criteria above.

# Prelude

## What do you need to set it up?

- GitHub repository with github pages activated.
- A _main_ branch for deployment.
- A _hexo_ branch for source.

__Importatnt:__ please change the default branch in github settings form _main_ to _hexo_.

## Component installation

I assume you have already git installed. Then get node, and then hexo.

## (Optional) Theme

If you want a custom theme, the best way is to fork an existing one, or just write one yourself. Either way you'll want then to add it as a submodule to your repo (in the _hexo_ branch).

### Adding a submodule

`git submodule add git@github.com:probberechts/hexo-theme-cactus.git themes/cactus`

And then the usuall

`git add . && git commit && git push`

# Action

So you've installed hexo and a theme, configured it by modifying `_config.yml`, and now you are ready to write posts. Congratulations you are a blogger.

## Deployment

There is this functionality `hexo deploy` already built in hexo. But I thought to myself, if I am going to push to my repo one the _hexo_ branch anyways, it would be better if the deployment happens automatically. Therefore I use github workflows.

In your root directory create this file:

```
mkdir -p .github/workflows
vim .github/workflows/pages.yml
```

```yaml
name: Pages

on:
  push:
    branches:
      - hexo

jobs:
  pages:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
            submodules: true
      - name: Use Node.js 12.x
        uses: actions/setup-node@v1
        with:
          node-version: '12.x'
      - name: Cache NPM dependencies
        uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
          publish_branch: main
```

The only things that you may want to alter are the branches. I firstly had the issue that all the generated .html files were empty. To fix that, I had to add this line `submodules: true`.

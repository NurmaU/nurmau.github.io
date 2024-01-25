+++
title = 'How to create blog on github.io'
date = 2024-01-14T07:07:07+01:00
+++
## Introduction

This is my first blogpost. Here as tutorial about how I created this blog.

## Create github.io page

You only can page in format [username].github.io where [username] is your name in github account. To have GitHub page just create new repo called [username].github.io and make it public. Clone it locally

```
git clone https://github.com/username/username.github.io

cd username.github.io
echo "Hello World" > index.html

git add --all
git commit -m "Initial commit"
git push -u origin main
```
Go to the Actions page on repo page. Wait until blocks turns green. Then go to your GitHub Page `https://[username].github.io`.


## Choose Hugo theme

Choose template you like from [Hugo themes](https://themes.gohugo.io/). Most themes have demos to experience website and related github repo. For example, I choose theme called [Paper](https://themes.gohugo.io/themes/hugo-paper/). I added repo as submodule to my [username].github.io repo

```bash
git submodule add https://github.com/nanxiaobei/hugo-paper themes/paper
```
and created file `config.toml` with content

```toml
theme = "paper"
baseURL = 'https://nurmau.github.io/'
languageCode = 'en-us'
title = 'My New Hugo Site'
```

## Install Hugo
Hugo is static site generator, framework which handles everything related to your simple website,  
To install it on macOS use

```bash
brew install hugo
```


Add worflow yaml file to repo
```bash
.github/workflows/hugo.yaml
```
with content 

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.121.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb          
      - name: Install Dart Sass
        run: sudo snap install dart-sass
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"          
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v3
```

Go to your repo [username].github.io, open `Settings` -> `Pages` -> `Build and deployment`. As source choose `GitHub Actions`. 
 

## Reference
* https://pages.github.com/
* https://github.com/nanxiaobei/hugo-paper/tree/main
* https://gohugo.io/getting-started/quick-start/


+++
title = 'How to create blog on github.io with Hugo'
date = 2024-01-24T07:07:07+01:00
+++
## Introduction

In this tutorial you will learn how to create personal website on github.io domain using Hugo. Hugo is a static site generator written in Go. It allows users to create websites quickly and easily. Hugo takes a directory with content and templates and renders them into a full HTML website. It is often used for creating blogs, documentation, and other types of static websites. For simple blogging website you only need to write content in `.md` files.

## Create github.io page

You can create personal page in format `[username].github.io` where [username] is your github account name. To have GitHub page just create new repo called `[username].github.io` and make it public. Clone it locally.

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

Choose template you like from [Hugo themes](https://themes.gohugo.io/). Most themes have demos to experience website and related github repo with instructions how to use it. For example, I chose theme called [Paper](https://themes.gohugo.io/themes/hugo-paper/). I added repo as submodule to my [username].github.io repo

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

## Prepare Hugo
First install it (on macOS)

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

Go to your repo `[username].github.io`, open `Settings` -> `Pages` -> `Build and deployment` and as source choose `GitHub Actions`. 

In repo directory to build website files run just
```bash
hugo
```
It will create folder `public` which has all files necessary to build pretty empty website. Commit changes and look at result `[username].github.io`. At this stage you should see basic template on your website.
So this stage your repo directory should look like this

```bash
├── .github
│   └── workflows
│       └── hugo.yaml
├── .gitmodules
├── .hugo_build.lock
├── config.toml
├── public
│   ├── ...
└── themes
    └── paper
        ├── ...
```


## Create blogpost
To create blogpost in root directory first create folder ```content```
then run this
```bash
hugo new content posts/my-first-post.md
```
It creates folder `posts` with file my-first-post.md with content

```
---
title: "My First Post"
date: 2024-01-24T20:45:51-05:00
draft: true
---
```

By default all new changes considered as drafts. To visualise them locally need to run  
```bash
hugo server --buildDrafts
```
To apply changes in final version, change flag `draft: false` in `.md` file, delete folder `public` and recreate it with command `hugo` as above, so change will migrate from draft to `public`. Be careful if you have some unique files in public, backup them to safe place. Also if you met trouble not see website even if you published, probably the problem is the `date` in `.md`. Hugo don't publish immediately content with future dates. Just set date from past.

In future if you want to change theme you only need to copy `.md` files.




## Reference
* https://pages.github.com/
* https://github.com/nanxiaobei/hugo-paper/tree/main
* https://gohugo.io/getting-started/quick-start/


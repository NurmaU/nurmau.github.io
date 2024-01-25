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

```
git submodule add https://github.com/nanxiaobei/hugo-paper themes/paper
```
and created file `config.toml` with content

```
theme = "paper"
baseURL = 'https://nurmau.github.io/'
languageCode = 'en-us'
title = 'My New Hugo Site'
```

## Install Hugo
Hugo is static site generator, framework which handles everything related to your simple website,  
To install it on macOS use

```
brew install hugo
```


## Reference
* https://pages.github.com/
* https://github.com/nanxiaobei/hugo-paper/tree/main
* https://gohugo.io/getting-started/quick-start/


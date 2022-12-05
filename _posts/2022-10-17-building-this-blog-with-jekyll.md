---
layout: page
title: Building this blog with Jekyll
date: 2022-10-17 09:00:00 +0200
categories: jekyll blog
---

We follow the official
[Jekyll documentation](https://jekyllrb.com/docs/)
to build this blog on an Ubuntu machine using the default
[minima theme](https://github.com/jekyll/minima).

## Installing dependencies

```{bash}
sudo apt update
sudo apt install -y ruby-full build-essential zlib1g-dev
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
gem install jekyll bundler
```

## Installing Jekyll

```{bash}
gem install jekyll bundler
```

## Installing `jekyll-remote-theme` plugin

```{bash}
gem install jekyll-remote-theme
```

## Creating the site project

```{bash}
jekyll new sandbd
cd sandbd
```

## Adding the `webrick` bundle

> Note: this step is due to an [open Jekyll issue](https://github.com/github/pages-gem/issues/752).

```{bash}
bundle add webrick
```

## Adding the `jekyll-remote-theme` plugin

```{bash}
bundle add -g jekyll_plugins jekyll-remote-theme
```

## Running a local server

```{bash}
bundle exec jekyll serve --livereload
```

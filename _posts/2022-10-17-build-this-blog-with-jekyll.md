---
layout: post
title: Build this blog with Jekyll
categories: jekyll
---

We follow the [Jekyll documentation](https://jekyllrb.com/docs/)
to build this blog on an Ubuntu machine using the default
[minima theme](https://github.com/jekyll/minima).

## Install Ruby dependencies

```{bash}
sudo apt update
sudo apt install -y ruby-full build-essential zlib1g-dev
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

## Install Jekyll and Bundler

```{bash}
gem install jekyll bundler
```

## Install blog dependencies

```{bash}
bundle install
```

## Run the local server

```{bash}
bundle exec jekyll serve --livereload
```

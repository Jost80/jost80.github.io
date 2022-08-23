---
title: Setup Jekyll in WSL
date: 2022-08-04 19:03:00 +/-TTTT
categories: [Windows]
tags: [wsl, jekyll]     # TAG names should always be lowercase
---


This attempt at a blog is done with Jekyll and the [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) theme. 

After following the guide for chirpy and downloading a copy of the repository I used WSL for the local jekyll installation.

```bash
sudo apt-get install -y ruby-full build-essential zlib1g-dev
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

gem install bundler
gem install jekyll --version 3.9.2

cd repositoryfolder
bundle exec jekyll serve
```

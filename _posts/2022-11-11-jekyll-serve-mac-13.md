---
layout: post
title: Installing Ruby/Jekyll on MacOS 13 on M1
subtitle:
author: K. E. Claytor
tags: macos 13, osx 13, m1, mac air, ventura, jekyll, ruby
hero_image: /assets/images/misc/jekyll-serve-reload.png
---

Some notes on updating ruby-on-rails on my M1 Mac Air running OSX 13:

1. Trying to install ruby via the [jekyll instructions](https://jekyllrb.com/docs/installation/macos/) did not work
    - I kept having issues with `ruby -v` and `which ruby` returning the pre-installed system ruby (2.6.x)
    - `ruby-install ruby` did not complete, instead erroring out with `symbol(s) not found for architecture arm64`
1. However, they linked to [Don't use sudo gem install](https://www.moncefbelyamani.com/why-you-shouldn-t-use-the-system-ruby-to-install-gems-on-a-mac/).
Which led me to the [full install instructions for mac](https://www.moncefbelyamani.com/how-to-install-xcode-homebrew-git-rvm-ruby-on-mac/#start-here-if-you-choose-the-long-and-manual-route)
    - The key was; `ruby-install ruby -- --enable-shared`
1. Once that worked, the rest proceeded smoothly until it came time to serve the site.
1. I needed to add two additional gems (the install instructions only mention the first)
    - `bundle add webrick`
    - `bundle add rexml`
    - You may need `bundle update` and `bundle install`
1. Then jekyll serve worked; `bundle exec jekyll serve --livereload`

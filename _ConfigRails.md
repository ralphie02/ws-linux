---
tags: [rails, rails/conf]
---
```bash
#!/bin/bash

# curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add - && \
#     echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list && \
#     sudo apt-get install -y --no-install-recommends yarn

echo -e '-------------------- RAILS: (START) Set corepack/yarn --------------------\n'
#source ~/.asdf/asdf.sh
#~/.asdf/shims/corepack enable && \
  #~/.asdf/shims/yarn set version stable
  #~/.asdf/shims/yarn install
echo -e '-------------------- RAILS: (END) Set corepack/yarn --------------------\n'

echo -e '-------------------- RAILS: (START) asdf/shims/gem install bundler --------------------\n'
~/.asdf/shims/gem install bundler
echo -e '-------------------- RAILS: (END) asdf/shims/gem install bundler --------------------\n'

echo -e '-------------------- RAILS: (START) asdf/shims/gem install rails --------------------\n'
~/.asdf/shims/gem list -ra rails | grep "^rails\ " > /tmp/rails-versions && \
  [ "$RAILS_VER" != "" ] && \
  [ $(grep -o $RAILS_VER /tmp/rails-versions | wc -l) == 1 ] && \
  ~/.asdf/shims/gem install rails -v $RAILS_VER || \
  ~/.asdf/shims/gem install rails
echo -e '-------------------- RAILS: (START) asdf/shims/gem install rails --------------------\n'
```

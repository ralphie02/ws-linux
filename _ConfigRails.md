---
tags: [rails, rails/conf]
---

```bash
#!/bin/bash

# curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add - && \
#     echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list && \
#     sudo apt-get install -y --no-install-recommends yarn

corepack enable
yarn set version stable
yarn install

~/.asdf/shims/gem install bundler
gem list -ra rails | grep "^rails\ " > /tmp/rails-versions
RAILS_VER=$(grep -o $RAILS_VER /tmp/rails-versions || cut -d' ' -f-2 /tmp/rails-versions | grep -Eo [0-9]\.[0-9]+\.[0-9]+)
gem install rails -v $RAILS_VER
```

```bash
#!/bin/bash

sudo apt-get install -y --no-install-recommends gcc g++ make zlib1g-dev libssl-dev libreadline-dev libyaml-dev libffi-dev && \
    git -C ~/.rbenv pull || \
        git clone --depth 1 https://github.com/rbenv/rbenv.git ~/.rbenv && \
    git -C ~/.rbenv/plugins/ruby-build pull || \
        git clone --depth 1 https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build && \
    echo 'gem: --no-document' > ~/.gemrc && \

RBENV_PATH='export PATH=$HOME/.rbenv/bin:$PATH'
grep -qxF "$RBENV_PATH" ~/.bashrc || echo "$RBENV_PATH" >> ~/.bashrc
RBENV_INIT='eval "$(rbenv init -)"'
grep -qxF "$RBENV_INIT" ~/.bashrc || echo "$RBENV_INIT" >> ~/.bashrc
~/.rbenv/bin/rbenv install -l > /tmp/ruby-versions
RUBY_VER=$(grep $RUBY_VER /tmp/ruby-versions || egrep ^[0-9]\.[0-9]+\.[0-9]+ /tmp/ruby-versions | tail -1)

~/.rbenv/bin/rbenv versions | grep $RUBY_VER || \
    ~/.rbenv/bin/rbenv install $RUBY_VER && ~/.rbenv/bin/rbenv global $RUBY_VER && \
    ~/.rbenv/shims/gem install awesome_print

update_irbrc() {

# --------------- IRBRC BEGIN BLOCK --------------- #
read -rd '' IRBRC << 'EOF'
require "awesome_print"

IRB.conf[:EVAL_HISTORY] = 1000000
IRB.conf[:SAVE_HISTORY] = 1000000
IRB.conf[:USE_MULTILINE] = false

AwesomePrint.irb! if defined?(AwesomePrint)
EOF
# --------------- IRBRC END BLOCK --------------- #

${BLOCK_SCRIPT_PATH} ~/.irbrc "$IRBRC"
}

~/.rbenv/bin/rbenv versions && update_irbrc
```
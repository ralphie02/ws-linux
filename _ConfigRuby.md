---
tags: [ruby, ruby/conf]
---
```bash
#!/bin/bash

echo -e '-------------------- RUBY: (START) Pkg install --------------------\n'
sudo apt-get install -y --no-install-recommends gcc g++ make zlib1g-dev libssl-dev libreadline-dev libyaml-dev libffi-dev
echo -e '-------------------- RUBY: (END) Pkg install --------------------\n'

echo -e '-------------------- RUBY: (START) Install via asdf + gemrc --------------------\n'
#source ~/.asdf/asdf.sh
asdf plugin add ruby
[ $(asdf list all ruby | grep "$RUBY_VER" | wc -l) == 1 ] && \
  asdf install ruby $RUBY_VER && asdf set ruby $RUBY_VER || \
  (asdf install ruby latest && asdf set ruby latest) && \
  echo 'gem: --no-document' > ~/.gemrc
echo -e '-------------------- RUBY: (END) Install via asdf + gemrc --------------------\n'

echo -e '-------------------- RUBY: (START) Insert block to ~/.irbrc --------------------\n'
# --------------- IRBRC BEGIN BLOCK --------------- #
read -rd '' IRBRC << 'EOF'
begin
  require "awesome_print"
  AwesomePrint.irb!

  if %w[true t yes y 1].include? ENV["DOCKER_ENV"]
    puts "(DOCKER_ENV) Loading guard against ap() being called without args"
    module Kernel
      alias_method :ap_without_guard, :ap
      def ap(obj = nil, options = {})
        return ap_without_guard(obj, options) unless obj.nil?
        nil
      end
    end
  end
rescue LoadError
  # awesome_print not installed, skip integration
end

IRB.conf[:EVAL_HISTORY]  = 1_000_000
IRB.conf[:SAVE_HISTORY]  = 1_000_000
# IRB.conf[:USE_MULTILINE] = false # uncomment to disable multiline autocomplete selection

class Object
  def interesting_methods
    case self.class
    when Class
      self.public_methods.sort - Object.public_methods
    when Module
      self.public_methods.sort - Module.public_methods
    else
      self.public_methods.sort - Object.new.public_methods
    end
  end
end

module Kernel
  def rah_guid(s)
    s.scan(/[a-f0-9-]{36}/).first
  end
end
EOF
# --------------- IRBRC END BLOCK --------------- #

$BLOCK_SCRIPT_PATH ~/.irbrc "$IRBRC" $CFG_BASENAME
echo -e '-------------------- RUBY: (END) Insert block to ~/.irbrc --------------------\n'

echo -e '-------------------- RUBY: (START) Insert block to ~/.inputrc --------------------\n'
# --------------- INPUTRC BEGIN BLOCK --------------- #
read -rd '' INPUTRC << 'EOF'
# Show completion candidates immediately and keep the menu visible
set show-all-if-ambiguous on
set menu-complete-display-prefix on

# Ctrl-N / Ctrl-P cycle forward/backward through completion candidates
"\C-n": menu-complete
"\C-p": menu-complete-backward
EOF
# --------------- INPUTRC END BLOCK --------------- #
$BLOCK_SCRIPT_PATH ~/.inputrc "$INPUTRC" $CFG_BASENAME
echo -e '-------------------- RUBY: (END) Insert block to ~/.inputrc --------------------\n'
```

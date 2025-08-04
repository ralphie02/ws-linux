```bash
#!/bin/bash

BASE_URL=https://raw.githubusercontent.com/ralphie02/ws-linux/master

# --------------- ADD BLOCK CREATOR SCRIPT --------------- #
BLOCK_SCRIPT_PATH=/tmp/rahtomation_block_script.sh
wget -qO- $BASE_URL/_BlockScript.md | sed '$ d' | sed '0,/```bash/d' > "$BLOCK_SCRIPT_PATH" && chmod +x $BLOCK_SCRIPT_PATH

run_config() {
    wget -qO- $BASE_URL/$1 | sed '$ d' | sed '0,/```bash/d' | sed "/\#\!.*bash\s*$/a \\\nCFG_BASENAME=$1 $2" | bash
}

# --------------- CONFIG GIT --------------- #
run_config _ConfigGit.md "GIT_CONF_EMAIL=$GIT_CONF_EMAIL GIT_CONF_FNAME=$GIT_CONF_FNAME GIT_CONF_LNAME=$GIT_CONF_LNAME"

# --------------- CONFIG SSH FZF BASHRC VIMRC INPUTRC WSL_CONF NVIM ASDF TMUX --------------- #
CONFIGS=(ConfigSshKey ConfigSshConf ConfigFzf ConfigBashrc ConfigVimrc ConfigInputrc ConfigWslConf ConfigNvim ConfigAsdf ConfigTmux)
for cfg in ${CONFIGS[@]}; do run_config _$cfg.md "BLOCK_SCRIPT_PATH=$BLOCK_SCRIPT_PATH"; done

# --------------- CONFIG NODE --------------- #
run_config _ConfigNode.md

# --------------- CONFIG RUBY --------------- #
run_config _ConfigRuby.md "BLOCK_SCRIPT_PATH=$BLOCK_SCRIPT_PATH RUBY_VER=$RUBY_VER"

# --------------- CONFIG RAILS --------------- #
run_config _ConfigRails.md "RAILS_VER=$RAILS_VER"

# --------------- CONFIG PSQL --------------- #
run_config _ConfigPsql.md "BLOCK_SCRIPT_PATH=$BLOCK_SCRIPT_PATH PSQL_VER=$PSQL_VER"

echo "Finito"
```

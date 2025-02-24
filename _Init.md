```bash
#!/bin/bash

BASE_URL=https://raw.githubusercontent.com/ralphie02/ws-linux/master

# --------------- ADD BLOCK CREATOR SCRIPT --------------- #
BLOCK_SCRIPT_PATH=/tmp/rahtomation_block_script.sh
wget -qO- $BASE_URL/_BlockScript.md | sed '$ d' | sed '/```bash\|```shell\|```sh/d' > "$BLOCK_SCRIPT_PATH" && chmod +x $BLOCK_SCRIPT_PATH

run_config() {
    wget -qO- $1 | sed '$ d' | sed '/```bash\|```shell\|```sh/d' | sed "/\#\!.*bash\s*$/a \\\nCFG_BASENAME=$(basename $1) $2" | bash
}

# --------------- CONFIG SSH GIT FZF BASHRC VIMRC INPUTRC WSL_CONF --------------- #
CONFIGS=(ConfigSshKey ConfigSshConf ConfigGit ConfigFzf ConfigBashrc ConfigVimrc ConfigInputrc ConfigWslConf ConfigObsidian ConfigNvim ConfigAsdf)
CFG_VARS="BLOCK_SCRIPT_PATH=$BLOCK_SCRIPT_PATH GIT_CONF_EMAIL=$GIT_CONF_EMAIL GIT_CONF_NAME=$GIT_CONF_NAME"
for cfg in ${CONFIGS[@]}; do run_config $BASE_URL/_$cfg.md "$CFG_VARS"; done

# --------------- CONFIG NODE --------------- #
run_config $BASE_URL/_ConfigNode.md

# --------------- CONFIG RUBY --------------- #
run_config $BASE_URL/_ConfigRuby.md "BLOCK_SCRIPT_PATH=$BLOCK_SCRIPT_PATH RUBY_VER=$RUBY_VER"

# --------------- CONFIG RAILS --------------- #
run_config $BASE_URL/_ConfigRails.md "RAILS_VER=$RAILS_VER"

# --------------- CONFIG PSQL --------------- #
run_config $BASE_URL/_ConfigPsql.md "BLOCK_SCRIPT_PATH=$BLOCK_SCRIPT_PATH PSQL_VER=$PSQL_VER"

echo "Finito"
```

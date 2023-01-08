```bash

BASE_URL=https://raw.githubusercontent.com/ralphie02/ws-linux/master

sudo apt update && sudo apt install -y --no-install-recommends \
    mlocate keychain rsync curl wget man-db net-tools software-properties-common telnet

# --------------- ADD BLOCK CREATOR SCRIPT --------------- #
BLOCK_SCRIPT_PATH=/tmp/rahtomation_block_script.sh
wget -O- $BASE_URL/_BlockScript.md | sed '$ d' | sed '1,1d' > "$BLOCK_SCRIPT_PATH" && chmod +x $BLOCK_SCRIPT_PATH

# --------------- GENERATE SSH --------------- #
wget -O- $BASE_URL/_ConfigSsh.md | sed '$ d' | sed '1,1d' | \
    sed "/\#\!.*bash\s*$/a \\\BLOCK_SCRIPT_PATH=$BLOCK_SCRIPT_PATH GIT_CONF_EMAIL=$GIT_CONF_EMAIL" | bash

# --------------- GIT INSTALL/UPDATE --------------- #
wget -O- $BASE_URL/_ConfigGit.md | sed '$ d' | sed '1,1d' | \
    sed "/\#\!.*bash\s*$/a \\\nGIT_CONF_EMAIL=$GIT_CONF_EMAIL GIT_CONF_NAME=$GIT_CONF_NAME" | bash

# --------------- FZF INSTALL/UPDATE --------------- #
wget -O- $BASE_URL/_ConfigFzf.md | sed '$ d' | sed '1,1d' | bash

# --------------- CONFIG FZF BASHRC INPUTRC WSL_CONF --------------- #
BLOCK_CFGS=(ConfigFzf ConfigBashrc ConfigInputrc ConfigWslConf)
for cfg in ${BLOCK_CFGS[@]}; do 
    wget -O- $BASE_URL/_$cfg.md | sed '$ d' | sed '1,1d' | \
        sed "/\#\!.*bash\s*$/a \\\nBLOCK_SCRIPT_PATH=$BLOCK_SCRIPT_PATH" | bash
done

# --------------- CONFIG RUBY --------------- #
[ "$RAILS_VER" != "" ] || [ "$RUBY_VER" != "" ] && \
    wget -O- $BASE_URL/_ConfigRuby.md | sed '$ d' | sed '1,1d' | \
    sed "/\#\!.*bash\s*$/a \\\nBLOCK_SCRIPT_PATH=$BLOCK_SCRIPT_PATH RUBY_VER=$RUBY_VER" | bash

# --------------- CONFIG RAILS --------------- #
[ "$RAILS_VER" != "" ] && 
    wget -O- $BASE_URL/_ConfigRails.md | sed '$ d' | sed '1,1d' | \
    sed "/\#\!.*bash\s*$/a \\\nRAILS_VER=$RAILS_VER" | bash

# --------------- CONFIG NODE --------------- #
[ "$NODE_VER" != "" ] && 
    wget -O-$BASE_URL/_ConfigNode.md | sed '$ d' | sed '1,1d' | \
    sed "/\#\!.*bash\s*$/a \\\nNODE_VER=$NODE_VER" | bash
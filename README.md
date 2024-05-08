- copypasta
    ```bash
    sudo apt update && sudo apt install -y --no-install-recommends wget ca-certificates \
        mlocate keychain rsync curl man-db net-tools software-properties-common telnet
    # VARS='GIT_CONFIG_EMAIL=<email@mail.com> GIT_CONF_NAME="FirstLast" RAILS_VER=(latest|x.x.x|<unset to skip>) RUBY_VER=(latest|x.x.x|<unset to skip>)'
    VARS='GIT_CONF_EMAIL=edit@me.com GIT_CONF_NAME="<EditMe>" RAILS_VER=latest RUBY_VER=latest NODE_VER=lts' && wget -qO- https://raw.githubusercontent.com/ralphie02/ws-linux/master/_Init.md | sed '$ d' | sed '1,1d' | sed "/\#\!.*bash$/a \\\n$VARS" | bash
        # wget -O- <url> | sed '$ d' | sed '1,1d' | sed "s/\/bin\/bash/\/bin\/bash\n\n$VARS/" | bash
        # wget -O- <url> | awk -v vars="$VARS" 'NR>2 {print last; if(NR == 4) print vars} {last=$0}'  | bash

temp notes:
https://github.com/obsidianmd/obsidian-releases/releases/latest/download/obsidian-1.5.12-arm64.tar.gz
    ```

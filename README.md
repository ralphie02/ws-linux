- One-liner
    ```bash
    # VARS='GIT_CONFIG_EMAIL=<email@mail.com> GIT_CONF_NAME="Your Name" RAILS_VER=(latest|x.x.x|<unset to skip>) RUBY_VER=(latest|x.x.x|<unset to skip>)'
    VARS='GIT_CONF_EMAIL=edit@me.com GIT_CONF_NAME="<edit me>" RAILS=latest RUBY=latest NODE_VER=lts' && \
        wget -O- https://raw.githubusercontent.com/ralphie02/ws-linux/master/_Init.md | \
        sed '$ d' | sed '1,1d' | sed "/\#\!.*bash$/a \\\n$VARS" | bash
        # wget -O- <url> | sed '$ d' | sed '1,1d' | sed "s/\/bin\/bash/\/bin\/bash\n\n$VARS/" | bash
        # wget -O- <url> | awk -v vars="$VARS" 'NR>2 {print last; if(NR == 4) print vars} {last=$0}'  | bash
    ```
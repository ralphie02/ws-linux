- Pre-Linux setup (Windows)
    - navigate to `%userprofile%` then create `shared`
    - copy old `.ssh` to `%userprofile%`
    - install wsl2 with distro
        ```powershell
        wsl --install -d debian
        ```
    - reboot
- Linux setup
    ```bash
    sudo apt update && sudo apt install -y --no-install-recommends wget ca-certificates \
        mlocate keychain rsync curl man-db net-tools software-properties-common telnet
    # VARS='GIT_CONFIG_EMAIL=<email@mail.com> GIT_CONF_NAME="FirstLast" RAILS_VER=x.x.x RUBY_VER=x.x.x'
    wget -qO- https://raw.githubusercontent.com/ralphie02/ws-linux/master/_Init.md | sed '$ d' | sed '0,/```bash/d' | sed "/\#\!.*bash$/a \\\nGIT_CONF_EMAIL=edit@me\nGIT_CONF_FNAME=Edit\nGIT_CONF_LNAME=me\nRAILS_VER=7.1.5.1\nRUBY_VER=3.3.7" | bash
        # wget -O- <url> | sed '$ d' | sed '1,1d' | sed "s/\/bin\/bash/\/bin\/bash\n\n$VARS/" | bash
        # wget -O- <url> | awk -v vars="$VARS" 'NR>2 {print last; if(NR == 4) print vars} {last=$0}'  | bash
    ```
- Android
    ```bash
    curl -s https://raw.githubusercontent.com/ralphie02/ws-linux/master/_ConfigObsidianOnAndroid.md | sed '$ d' | sed '0,/```bash/d' | bash
    ```

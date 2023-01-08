```bash
sudo apt install -y --no-install-recommends git && \
    git config user.name || git config --global user.name "$GIT_CONF_NAME" && \    
    ## git config ... name || while do; ; done && git config ... name && \
    # while [ "$GIT_CONF_NAME" == "" ]; do 
    #     read -p "Enter name for Git (ie. FirstName LastName): " GIT_CONF_NAME
    # done && \

    git config user.email || git config --global user.email "$GIT_CONF_EMAIL" && \
    ## git config ... mail || while do; ; done && git config ... mail && \
    # while [ "$GIT_CONF_EMAIL" == "" ]; do 
    #     read -p "Enter email for Git (ie. test@email.com): " GIT_CONF_EMAIL
    # done && \

    git config color.ui || git config --global color.ui true && \
    git config push.default || git config --global push.default current && \
    git config branch.autoSetupMerge || git config --global branch.autoSetupMerge always && \
    git config -l
```
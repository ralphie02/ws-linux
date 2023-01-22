```bash
#!/bin/bash

sudo apt install -y --no-install-recommends openssh-client

if grep -qi microsoft /proc/version; then 
    SHARED_DIRNAME=shared
    WIN_USER=$(/mnt/c/windows/system32/cmd.exe /c 'echo %username%' | sed -e 's/\r//g')
    if [ ! -L "$SHARED_DIRNAME" ]; then ln -s /mnt/c/Users/$WIN_USER/shared ~/; fi
    
    NEW_SSH_KEY=true
    if [ ! -f ~/.ssh/id_rsa -a -f /mnt/c/Users/$WIN_USER/.ssh/id_rsa ]; then
        cp -r /mnt/c/Users/$WIN_USER/.ssh ~/ && chmod 400 ~/.ssh/id_rsa*
    elif [ -f ~/.ssh/id_rsa -a ! -f /mnt/c/Users/$WIN_USER/.ssh/id_rsa ]; then
        cp -r ~/.ssh /mnt/c/Users/$WIN_USER/
    elif ! [ -f ~/.ssh/id_rsa -o -f /mnt/c/Users/$WIN_USER/.ssh/id_rsa ]; then 
        # read -p "Git email: " GIT_CONF_EMAIL \
        ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "$GIT_CONF_EMAIL"
    else
        NEW_SSH_KEY=false
    fi
    GREEN="\033[0;32m"
    NO_COLOR="\033[0m"
    COPY_MESSAGE="\n${GREEN}Copy pub key below to Github SSH keys! \n${NO_COLOR}"
    if $NEW_SSH_KEY; then echo -e "$COPY_MESSAGE" && cat ~/.ssh/id_rsa.pub; fi
elif [ ! -f ~/.ssh/id_rsa ]; then 
    # read -p "Git email: " GIT_CONF_EMAIL \
    ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "$GIT_CONF_EMAIL"
fi

# --------------- SSH_CFG END BLOCK --------------- #
read -rd '' SSH_CFG << 'EOF'
Host *
    StrictHostKeyChecking no
EOF
# --------------- SSH_CFG END BLOCK --------------- #

${BLOCK_SCRIPT_PATH} ~/.ssh/config "$SSH_CFG"
```
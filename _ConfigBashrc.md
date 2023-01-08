```bash
#!/bin/bash

# requires add_cf_block script
# https://superuser.com/a/976712 - line regex replace

# Update HISTSIZE to 200000 & HISTFILESIZE to 300000
sed -i '/^HISTSIZE=/{h;s/=.*/=200000/};${x;/^$/{s//HISTSIZE=200000/;H};x}' ~/.bashrc
sed -i '/^HISTFILESIZE=/{h;s/=.*/=300000/};${x;/^$/{s//HISTFILESIZE=300000/;H};x}' ~/.bashrc

# --------------- BASHRC BEGIN BLOCK --------------- #
read -rd '' BASHRC << 'EOF'
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/\1/'
}
git_l_bracket() { if [ "$(parse_git_branch)" != '' ]; then echo '('; fi }
git_r_bracket() { if [ "$(parse_git_branch)" != '' ]; then echo ')'; fi }
git_changed() {
    IS_GIT_CLEAN='clean'
    STATUS=`git status 2> /dev/null | grep "$IS_GIT_CLEAN"`
    if [[ "$STATUS" != *"$IS_GIT_CLEAN"* ]] && [ -e ./.git ]; then echo ' ✗'; fi
}
PS1='\[\e[96m\]\w\[\e[94m\]$(git_l_bracket)\[\e[91m\]$(parse_git_branch)\[\e[94m\]$(git_r_bracket)\[\e[93m\]$(git_changed) \[\e[00m\]'

export EDITOR=vim
export LESS="$LESS -R -Q" # disable beep in LESS for Linux on Win10
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0
export TERM=xterm-256color
EOF
# --------------- BASHRC END BLOCK --------------- #

${ADD_CFG_BLOCK_PATH} ~/.bashrc "$BASHRC"
```
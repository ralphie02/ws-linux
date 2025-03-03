---
tags: [android, obsidian, script, linux]
---

```bash
#!/bin/bash
  
pkg install -y openssh git cronie

# Configure ssh key & config file
[ ! -f $HOME/.ssh/id_rsa ] &&
  ssh-keygen -t rsa -C $(/system/bin/getprop ro.product.model | tr '[:upper:]' '[:lower:]' | sed 's/ /-/g') -f $HOME/.ssh/id_rsa -N ''
[ ! -f $HOME/.ssh/config ] && echo -e "Host github.com\n  StrictHostKeyChecking no" > $HOME/.ssh/config

# Configure storage (~/storage)
[ ! -d $HOME/storage ] && termux-setup-storage 

# Generate obsidian dir
mkdir -p $HOME/storage/shared/Documents/repos 

# -- CRON -- #

# Generate termux boot dir
mkdir -p $HOME/.termux/boot 

# Configure cron
[ ! -f $HOME/.termux/boot/start-crond ] && 
  echo -e "#!/data/data/com.termux/files/usr/bin/sh\ntermux-wake-lock\ncrond" > $HOME/.termux/boot/start-crond && 
  chmod +x $HOME/.termux/boot/start-crond

# -- CRON -- #

# -- GIT -- #

# Configure git
git config --global --add safe.directory '*'
git config --global user.name "Ralph - Android"
git config --global user.email ralphie02@live.com

# Configure git script to sync repo + submodules
read -rd '' SYNC_OBSIDIAN << EOF
#!/data/data/com.termux/files/usr/bin/bash

# Step 0: Change dir
cd $HOME/storage/shared/Documents/repos/obsidian # or /storage/emulated/0/Documents/repos/obsidian
    
# Step 1: Run git pull and handle conflicts for the main repository
git fetch --all
if ! git pull origin \$(git rev-parse --abbrev-ref HEAD); then
    echo "Conflict detected in the main repository. Discarding local changes."
    git reset --hard origin/\$(git rev-parse --abbrev-ref HEAD)
fi

# Step 2: Update submodules and handle conflicts separately
git submodule update --init --recursive
git submodule foreach --recursive '
    if ! git pull origin \$(git rev-parse --abbrev-ref HEAD); then
        echo "Conflict detected in submodule $name. Discarding local changes."
        git reset --hard origin/\$(git rev-parse --abbrev-ref HEAD)
    fi
'

# Step 3: Add and commit changes in the main repository
git add .
git commit -m "Committing changes after pull"

# Step 4: Push changes in the main repository
git push origin \$(git rev-parse --abbrev-ref HEAD)

# Step 5: Push changes in submodules
git submodule foreach --recursive git push origin \$(git rev-parse --abbrev-ref HEAD)

date
EOF

mkdir -p $HOME/scripts $HOME/logs
echo -e "$SYNC_OBSIDIAN" > $HOME/scripts/sync-obsidian.sh
chmod +x $HOME/scripts/sync-obsidian.sh
crontab -l | grep sync-obsidian ||
  (crontab -l; echo "*/30 * * * * $HOME/scripts/sync-obsidian.sh > $HOME/logs/sync-obsidian-log") | crontab -
  
# -- GIT -- #

# -- INPUTRC -- #

read -rd '' INPUTRC << EOF
set show-all-if-ambiguous on
set show-all-if-unmodified on
set menu-complete-display-prefix on
set completion-ignore-case on
"\e[1;5C": forward-word
"\e[1;5D": backward-word
"\e[A": history-search-backward
"\e[B": history-search-forward
EOF

echo -e "$INPUTRC" > $HOME/.inputrc

# -- INPUTRC -- #

# -- MANUAL -- #

# Nothing will work unless the ssh key has access to ralphie02/obsidian
GREEN="\033[0;32m"
YELLOW="\033[0;33m"
NO_COLOR="\033[0m"
SSH_PUB=$(cat $HOME/.ssh/id_rsa.pub)
echo -e "$GREEN\n$SSH_PUB"
echo -e $YELLOW"\nAdd the ssh pub key above into github and then run the commands below:\n"$NO_COLOR
cat << EOF
cd $HOME/storage/shared/Documents/repos
git clone git@github.com:ralphie02/obsidian.git 
cd obsidian
git submodule update --init
git submodule foreach 'git checkout master'
EOF
echo -e $YELLOW"\nOnce complete, reboot phone and run 'pidof crond' to ensure crond is running.\n"$NO_COLOR

# -- MANUAL -- #
```

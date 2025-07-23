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
echo "🔄 Step 0: Change the working dir"
cd $HOME/storage/shared/Documents/repos/obsidian # or /storage/emulated/0/Documents/repos/obsidian
    
# Step 0.1: Get current branch
branch=$(git rev-parse --abbrev-ref HEAD)

echo "🔄 Step 1: Ensure submodules are initialized and pointing to proper branches"
git submodule update --init --recursive

# Step 1.1: Check for uncommitted changes in submodules before resets
echo "🔍 Checking for uncommitted changes in submodules..."
git submodule foreach --recursive '
    if ! git diff --quiet || ! git diff --cached --quiet; then
        echo "⚠️  Warning: Uncommitted changes in submodule $name"
    fi
'

# Step 1.2: Sync, checkout branch, and pull latest submodule content
git submodule foreach --recursive '
    echo "📦 Handling submodule: $name"

    # Detect default branch name (could be main or master)
    default_branch=$(git remote show origin | sed -n "/HEAD branch/s/.*: //p")

    # If in detached HEAD, switch to the right branch
    current_branch=$(git rev-parse --abbrev-ref HEAD)
    if [ "$current_branch" = "HEAD" ]; then
        echo "⛑️  $name is in detached HEAD — checking out $default_branch"
        git checkout -B "$default_branch" "origin/$default_branch"
    fi

    b=$(git rev-parse --abbrev-ref HEAD)
    git fetch origin $b
    if ! git merge --ff-only origin/$b; then
        echo "⚠️  Conflict in submodule $name. Resetting to remote."
        git reset --hard origin/$b
    fi
'

echo "⬇️ Step 2: Pull latest changes in main repo"
git fetch origin "$branch"
if ! git merge --ff-only origin/"$branch"; then
    echo "⚠️  Conflict in main repo. Resetting to remote."
    git reset --hard origin/"$branch"
fi

echo "🔍 Step 3: Git FSCK check (integrity check)"
if ! git fsck --no-reflogs --full; then
    echo "❌ Repository is corrupt. Aborting."
    exit 1
fi

echo "💾 Step 4: Commit and push submodules first"
git submodule foreach --recursive '
    b=$(git rev-parse --abbrev-ref HEAD)

    if ! git diff --quiet || ! git diff --cached --quiet; then
        echo "💡 Changes detected in $name. Committing..."
        git add .
        git commit -m "Update in submodule $name" || echo "✅ No changes in submodule $name"
    fi

    echo "📤 Pushing submodule $name"
    git push origin $b
'

echo "🧩 Step 5: Commit submodule SHA updates in main repo"

git add .
git commit -m "Update submodule SHA changes" || echo "✅ No submodule SHA changes"

echo "📤 Step 6: Push main repo"
git push origin "$branch"

echo "✅ Done! Submodules and main repo are synced and pushed."

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

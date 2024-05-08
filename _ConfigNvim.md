---
tags: bash, fzf, sed
---

```bash
#!/bin/bash

wget -P /tmp https://github.com/ralphie02/nvim-build/releases/latest/download/nvim.tar.gz \
  && sudo mkdir -p /opt/bin
  

FPATH=$(wget -qO- https://api.github.com/repos/obsidianmd/obsidian-releases/releases/latest | grep browser_download_url | cut -d\" -f4 | grep 'arm64.tar.gz')
VERSION=$(echo $FPATH | rev | cut -d\/ -f2 | rev | cut -c2-)
# TAR_FILE=$(echo $FPATH | rev | cut -d\/ -f1 | rev)
# UNTARRED_DIR=$(echo ${TAR_FILE%.*.*})

rm -rf /tmp/obsidian
mkdir -p /tmp/obsidian
wget --no-check-certificate -O /tmp/obsidian.tar.gz $FPATH
tar -xvf /tmp/obsidian.tar.gz -C /tmp/obsidian/
sudo mkdir -p /opt/bin
sudo rm -rf /opt/obsidian && sudo mv /tmp/obsidian/** /opt/obsidian
sudo ln -sf /opt/obsidian/obsidian /opt/bin/obsidian
rm -rf /tmp/obsidian*

# --------------- obsidian.desktop BEGIN BLOCK --------------- #
read -rd '' OBSIDIAN_DESKTOP << EOF
[Desktop Entry]
Name=obsidian
Icon=obsidian
Comment=obsidian
Exec="/opt/bin/obsidian"
Version=$VERSION
Type=Application
Terminal=false
StartupNotify=true
EOF
# --------------- obsidian.desktop END BLOCK --------------- #

sudo ${BLOCK_SCRIPT_PATH} /usr/share/applications/obsidian.desktop "$OBSIDIAN_DESKTOP"
```

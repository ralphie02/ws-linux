---
tags: bash, fzf, sed
---

```bash
#!/bin/bash

echo -e '-------------------- OBSIDIAN: (START) Git pull --------------------\n'
git -C ~/obsidian pull || git clone git@github.com:ralphie02/obsidian.git ~/obsidian && \
  git -C ~/obsidian submodule update --init &&
  git -C ~/obsidian submodule foreach 'git checkout master'
echo -e '-------------------- OBSIDIAN: (END) Git pull --------------------\n'

echo -e '-------------------- OBSIDIAN: (START) Set env vars --------------------\n'
[ $(dpkg --print-architecture) = amd64 ] && FILE_ARCH=amd64.deb || FILE_ARCH=arm64.tar.gz
FPATH=$(wget -qO- https://api.github.com/repos/obsidianmd/obsidian-releases/releases/latest | grep browser_download_url | cut -d\" -f4 | grep $FILE_ARCH)
VERSION=$(echo $FPATH | rev | cut -d\/ -f2 | rev | cut -c2-)
# TAR_FILE=$(echo $FPATH | rev | cut -d\/ -f1 | rev)
# UNTARRED_DIR=$(echo ${TAR_FILE%.*.*})
echo -e '-------------------- OBSIDIAN: (END) Set env vars --------------------\n'

echo -e '-------------------- OBSIDIAN: (START) Download/Extract --------------------\n'
if [ $FILE_ARCH = amd64.deb ]; then
  wget -O /tmp/obsidian.deb $FPATH && sudo dpkg -i /tmp/obsidian.deb
elif [ $FILE_ARCH = arm64.tar.gz ]; then
  sudo rm -rf /tmp/obsidian* && \
    mkdir -p /tmp/obsidian
  wget -O /tmp/obsidian.tar.gz $FPATH && \
    tar -xvf /tmp/obsidian.tar.gz -C /tmp/obsidian
  sudo mkdir -p /opt/bin && \
    sudo rm -rf /opt/obsidian && \
    sudo mv /tmp/obsidian/** /opt/obsidian && \
    sudo ln -sf /opt/obsidian/obsidian /opt/bin/obsidian
fi
echo -e '-------------------- OBSIDIAN: (END) Download/Extract --------------------\n'

echo -e '-------------------- OBSIDIAN: (START) Insert block to obsidian.desktop --------------------\n'
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

sudo $BLOCK_SCRIPT_PATH /usr/share/applications/obsidian.desktop "$OBSIDIAN_DESKTOP" $CFG_BASENAME
echo -e '-------------------- OBSIDIAN: (END) Insert block to obsidian.desktop --------------------\n'
```

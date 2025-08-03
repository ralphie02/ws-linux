---
tags: bash, fzf, sed
---
```bash
#!/bin/bash

echo -e '-------------------- NVIM: (START) Install fd|ripgrep|xclip --------------------\n'
sudo apt install  -y --no-install-recommends fd-find ripgrep xclip
echo -e '-------------------- NVIM: (END) Install fd|ripgrep|xclip --------------------\n'

echo -e '-------------------- NVIM: (START) Lazygit env vars --------------------\n'
[ $(dpkg --print-architecture) = amd64 ] && FILE_ARCH=x86_64 || FILE_ARCH=arm64
NVIM_FPATH=$(wget -qO- https://api.github.com/repos/neovim/neovim/releases/latest | grep browser_download_url | cut -d\" -f4 | grep linux | grep $FILE_ARCH.tar.gz)
LAZY_FPATH=$(wget -qO- https://api.github.com/repos/jesseduffield/lazygit/releases/latest | grep browser_download_url | cut -d\" -f4 | grep $FILE_ARCH | grep Linux)
# VERSION=$(echo $LAZY_FPATH | rev | cut -d\/ -f2 | rev | cut -c2-)
# TAR_FILE=$(echo $LAZY_FPATH | rev | cut -d\/ -f1 | rev)
# UNTARRED_DIR=$(echo ${TAR_FILE%.*.*})
echo -e '-------------------- NVIM: (END) Lazygit env vars --------------------\n'

echo -e '-------------------- NVIM: (START) Download/Extract + LazyVimRah config --------------------\n'
sudo rm -rf /tmp/nvim* && \
  mkdir -p /tmp/nvim
# I no longer have to compile my own nvim for arm64 - commenting out if/else
# if [ $FILE_ARCH = x86_64 ]; then
wget -O /tmp/nvim.tar.gz $NVIM_FPATH
# elif [ $FILE_ARCH = arm64 ]; then
#  wget -P /tmp https://github.com/ralphie02/nvim-build/releases/latest/download/nvim.tar.gz
# fi
tar -xvf /tmp/nvim.tar.gz -C /tmp/nvim && \
  sudo mkdir -p /opt/bin && \
  sudo rm -rf /opt/nvim && \
  sudo mv /tmp/nvim/** /opt/nvim && \
  sudo ln -sf /opt/nvim/bin/nvim /opt/bin/nvim

git -C ~/.config/nvim pull || git clone git@github.com:ralphie02/LazyVimRah.git ~/.config/nvim
echo -e '-------------------- NVIM: (END) Download/Extract + LazyVimRah config --------------------\n'

echo -e '-------------------- NVIM: (START) Lazygit download/extract --------------------\n'
sudo rm -rf /tmp/lazygit* && \
  mkdir -p /tmp/lazygit && \
  wget -O /tmp/lazygit.tar.gz $LAZY_FPATH && \
  tar -xvf /tmp/lazygit.tar.gz -C /tmp/lazygit && \
  sudo mkdir -p /opt/bin && \
  sudo rm -rf /opt/lazygit && \
  sudo mv /tmp/lazygit /opt/lazygit && \
  sudo ln -sf /opt/lazygit/lazygit /opt/bin/lazygit
echo -e '-------------------- NVIM: (END) Lazygit download/extract --------------------\n'
```

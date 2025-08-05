---
tags: bash, fzf, sed
---
```bash
#!/bin/bash

echo -e '-------------------- NVIM: (START) Install fd|ripgrep|xclip --------------------\n'
sudo apt install  -y --no-install-recommends fd-find ripgrep xclip
echo -e '-------------------- NVIM: (END) Install fd|ripgrep|xclip --------------------\n'

echo -e '-------------------- NVIM: (START) Set env vars --------------------\n'
[ $(dpkg --print-architecture) = amd64 ] && FILE_ARCH=x86_64 || FILE_ARCH=arm64
NVIM_URL=$(wget -qO- https://api.github.com/repos/neovim/neovim/releases/latest | grep browser_download_url | cut -d\" -f4 | grep linux | grep $FILE_ARCH.tar.gz)
NVIM_VER=$(echo $NVIM_URL | cut -d\/ -f8)
echo -e '-------------------- NVIM: (END) Set env vars --------------------\n'

echo -e '-------------------- NVIM: (START) Download/Extract/Install --------------------\n'
if [ $(nvim --version 2>/dev/null | head -n 1 | cut -d' ' -f2) = $NVIM_VER ]; then
  echo -e "nvim: $NVIM_VER already installed"
else
  echo -e "nvim: installing $NVIM_VER"
  sudo rm -rf /tmp/nvim* && \
    mkdir -p /tmp/nvim
  wget -O /tmp/nvim.tar.gz $NVIM_URL
  tar -xvf /tmp/nvim.tar.gz -C /tmp/nvim > /dev/null
  #sudo mkdir -p /opt/bin && \
  sudo rm -rf /usr/local/nvim && \
    sudo mv /tmp/nvim/** /usr/local/nvim && \
    sudo ln -sf /usr/local/nvim/bin/nvim /usr/local/bin/nvim
fi
echo -e '-------------------- NVIM: (END) Download/Extract/Install --------------------\n'

echo -e '-------------------- NVIM: (START) LazyVimRah config --------------------\n'
git -C ~/.config/nvim pull || git clone git@github.com:ralphie02/LazyVimRah.git ~/.config/nvim
echo -e '-------------------- NVIM: (END) LazyVimRah config --------------------\n'

echo -e '-------------------- NVIM: (START LAZYGIT) Set env vars --------------------\n'
LAZY_FPATH=$(wget -qO- https://api.github.com/repos/jesseduffield/lazygit/releases/latest | grep browser_download_url | cut -d\" -f4 | grep $FILE_ARCH | grep linux)
# VERSION=$(echo $LAZY_FPATH | rev | cut -d\/ -f2 | rev | cut -c2-)
# TAR_FILE=$(echo $LAZY_FPATH | rev | cut -d\/ -f1 | rev)
# UNTARRED_DIR=$(echo ${TAR_FILE%.*.*})
echo -e '-------------------- NVIM: (END LAZYGIT) Set env vars --------------------\n'

echo -e '-------------------- NVIM: (START) Lazygit download/extract --------------------\n'
sudo rm -rf /tmp/lazygit* && \
  mkdir -p /tmp/lazygit
wget -O /tmp/lazygit.tar.gz $LAZY_FPATH
tar -xvf /tmp/lazygit.tar.gz -C /tmp/lazygit
#sudo mkdir -p /opt/bin && \
sudo rm -rf /usr/local/lazygit && \
  sudo mv /tmp/lazygit /usr/local/lazygit && \
  sudo ln -sf /usr/local/lazygit/lazygit /usr/local/bin/lazygit
echo -e '-------------------- NVIM: (END) Lazygit download/extract --------------------\n'
```

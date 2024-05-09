---
tags: bash, fzf, sed
---

```bash
#!/bin/bash

sudo rm -rf /tmp/nvim* \
  && mkdir -p /tmp/nvim \
  && wget -P /tmp https://github.com/ralphie02/nvim-build/releases/latest/download/nvim.tar.gz \
  && tar xvf /tmp/nvim.tar.gz -C /tmp/nvim \
  && sudo mkdir -p /opt/bin \
  && sudo rm -rf /opt/nvim \
  && sudo mv /tmp/nvim/** /opt/nvim \
  && sudo ln -sf /opt/nvim/bin/nvim /opt/bin/nvim \
  && git -C ~/.config/nvim pull || git clone git@github.com:ralphie02/LazyVimRah.git ~/.config/nvim

FPATH=$(wget -qO- https://api.github.com/repos/jesseduffield/lazygit/releases/latest | grep browser_download_url | cut -d\" -f4 | grep 'arm64.tar.gz' | grep 'Linux')
#VERSION=$(echo $FPATH | rev | cut -d\/ -f2 | rev | cut -c2-)
# TAR_FILE=$(echo $FPATH | rev | cut -d\/ -f1 | rev)
# UNTARRED_DIR=$(echo ${TAR_FILE%.*.*})

sudo rm -rf /tmp/lazygit* \
  && mkdir -p /tmp/lazygit \
  && wget -O /tmp/lazygit.tar.gz $FPATH \
  && tar -xvf /tmp/lazygit.tar.gz -C /tmp/lazygit \
  && sudo mkdir -p /opt/bin \
  && sudo rm -rf /opt/lazygit \
  && sudo mv /tmp/lazygit /opt/lazygit \
  && sudo ln -sf /opt/lazygit/lazygit /opt/bin/lazygit
```

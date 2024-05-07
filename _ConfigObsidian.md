---
tags: bash, fzf, sed
---

```bash
#!/bin/bash
# TO BE REPLACED/UPDATED

FPATH=$(wget -qO- https://api.github.com/repos/obsidianmd/obsidian-releases/releases/latest | grep browser_download_url | cut -d\" -f4 | grep 'arm64.tar.gz')
TAR_FILE=$(echo $FPATH | rev | cut -d\/ -f1 | rev)
UNTARRED_DIR=$(echo ${TAR_FILE%.*.*})

rm -rf /tmp/obsidian
mkdir -p /tmp/obsidian
wget --no-check-certificate -O /tmp/obsidian.tar.gz $FPATH
tar -xvf /tmp/obsidian.tar.gz -C /tmp/obsidian/
sudo mkdir -p /opt/bin
sudo rm -rf /opt/obsidian && sudo mv /tmp/obsidian/** /opt/obsidian
sudo ln -s /opt/obsidian/obsidian /opt/bin/obsidian
rm -rf /tmp/obsidian*


git -C ~/.fzf pull || \
    git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && ~/.fzf/install && \
    SRC_FZF='[ -f ~/.fzf.bash ] && source ~/.fzf.bash' && \
    # Add .fzf.bash sourcing in ~/.profile \
    grep -qxF "$SRC_FZF" ~/.profile || echo "$SRC_FZF" >> ~/.profile && \
    # Remove sourcing in ~/.bashrc \
    sed -i "/$(echo $SRC_FZF | sed 's/\./\\./g' | sed 's/\//\\\//g')/d" ~/.bashrc
    # (when $SRC_FZF is hardcoded) sed -i /'[ -f ~/.fzf.bash ] && source ~\/\.fzf\.bash'/d
```

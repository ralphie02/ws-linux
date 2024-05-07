---
tags: bash, fzf, sed
---

```bash
#!/bin/bash
# TO BE REPLACED/UPDATED

FILE=$(wget -qO- https://api.github.com/repos/obsidianmd/obsidian-releases/releases/latest | grep browser_download_url | cut -d\" -f4 | grep 'arm64.tar.gz')

git -C ~/.fzf pull || \
    git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && ~/.fzf/install && \
    SRC_FZF='[ -f ~/.fzf.bash ] && source ~/.fzf.bash' && \
    # Add .fzf.bash sourcing in ~/.profile \
    grep -qxF "$SRC_FZF" ~/.profile || echo "$SRC_FZF" >> ~/.profile && \
    # Remove sourcing in ~/.bashrc \
    sed -i "/$(echo $SRC_FZF | sed 's/\./\\./g' | sed 's/\//\\\//g')/d" ~/.bashrc
    # (when $SRC_FZF is hardcoded) sed -i /'[ -f ~/.fzf.bash ] && source ~\/\.fzf\.bash'/d
```

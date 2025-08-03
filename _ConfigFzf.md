---
tags: bash, fzf, sed
---
```bash
#!/bin/bash

echo -e '-------------------- FZF: (START) --------------------\n'
git -C ~/.fzf pull || \
  git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && \
  ~/.fzf/install && \
  
  # Add .fzf.bash sourcing in ~/.profile \
  SRC_FZF='[ -f ~/.fzf.bash ] && source ~/.fzf.bash' && \
  grep -qxF "$SRC_FZF" ~/.profile || \
  echo "$SRC_FZF" >> ~/.profile && \
  
  # Remove sourcing in ~/.bashrc \
  sed -i "/$(echo $SRC_FZF | sed 's/\./\\./g' | sed 's/\//\\\//g')/d" ~/.bashrc
  # (when $SRC_FZF is hardcoded) sed -i /'[ -f ~/.fzf.bash ] && source ~\/\.fzf\.bash'/d
echo -e '-------------------- FZF: (END) --------------------\n'
```

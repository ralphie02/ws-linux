---
tags: [git, git/conf]
---

```bash
#!/bin/bash

echo -e '-------------------- GIT: (START) Install git --------------------\n'
sudo apt install -y --no-install-recommends git 
echo -e '-------------------- GIT: (END) Install git --------------------\n'
    
echo -e '-------------------- GIT: (START) Configs --------------------\n'
[ "$(git config user.email)" != "" ] || git config --global user.email "$GIT_CONF_EMAIL" && \
  echo "git email" && \
  [ "$(git config user.name)" != "" ] || git config --global user.name "$GIT_CONF_NAME" && \    
  echo "git name" && \
  git config color.ui || git config --global color.ui true && \
  echo "git color" && \
  git config pull.rebase || git config --global pull.rebase false && \
  git config push.default || git config --global push.default current && \
  git config push.autoSetupRemote || git config --global push.autoSetupRemote true && \
  git config branch.autoSetupMerge || git config --global branch.autoSetupMerge always && \
    
  GREEN="\033[0;32m" && \
  NO_COLOR="\033[0m" && \
  echo -e "\n${GREEN}$(git config -l)${NO_COLOR}\n"
echo -e '-------------------- GIT: (END) Configs --------------------\n'
```

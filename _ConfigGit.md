---
tags: [git, git/conf]
---
```bash
#!/bin/bash

echo -e '-------------------- GIT: (START) Install git --------------------\n'
sudo apt install -y --no-install-recommends git 
echo -e '-------------------- GIT: (END) Install git --------------------\n'
    
echo -e '-------------------- GIT: (START) Configs --------------------\n'
git config user.email || echo -e "Setting git user.email to $GIT_CONF_EMAIL" && git config --global user.email $GIT_CONF_EMAIL
git config user.name || echo -e "Setting git user.name to $GIT_CONF_FNAME$GIT_CONF_LNAME" && git config --global user.name "$GIT_CONF_FNAME $GIT_CONF_LNAME" && 
  git config color.ui || git config --global color.ui true && \
  git config pull.rebase || git config --global pull.rebase false && \
  git config push.default || git config --global push.default current && \
  git config push.autoSetupRemote || git config --global push.autoSetupRemote true && \
  git config branch.autoSetupMerge || git config --global branch.autoSetupMerge always && \
    
  GREEN="\033[0;32m" && \
  NO_COLOR="\033[0m" && \
  echo -e "\n${GREEN}$(git config -l)${NO_COLOR}\n"
echo -e '-------------------- GIT: (END) Configs --------------------\n'
```

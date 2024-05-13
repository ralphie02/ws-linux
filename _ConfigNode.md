---
tags: [node, node/conf]
---

```bash
#!/bin/bash

~/.asdf/bin/asdf plugin add nodejs
[ $(~/.asdf/bin/asdf list all nodejs | grep $NODE_VER | ws -l) == 1 ] && \
  ~/.asdf/bin/asdf install nodejs $NODE_VER || \
  ~/.asdf/bin/asdf install nodejs latest

# https://stackoverflow.com/questions/18088372/how-to-npm-install-global-not-as-root
npm config set prefix '~/.local/'

# curl -sSL https://deb.nodesource.com/setup_$NODE_VER.x | sudo -E bash - || \
#     curl -sSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - && \
#     sudo apt-get install -y --no-install-recommends nodejs && \
```

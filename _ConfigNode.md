---
tags: [node, node/conf]
---

```bash
#!/bin/bash

echo -e '-------------------- NODE: (START) Install via asdf --------------------\n'
source ~/.asdf/asdf.sh
~/.asdf/bin/asdf plugin add nodejs
[ $(~/.asdf/bin/asdf list all nodejs | grep "$NODE_VER" | wc -l) == 1 ] && \
  ~/.asdf/bin/asdf install nodejs $NODE_VER && ~/.asdf/bin/asdf global nodejs $NODE_VER || \
  (~/.asdf/bin/asdf install nodejs latest && ~/.asdf/bin/asdf global nodejs latest)
echo -e '-------------------- NODE: (END) Install via asdf --------------------\n'

echo -e '-------------------- NODE: (START) npm config ~/.local --------------------\n'
# https://stackoverflow.com/questions/18088372/how-to-npm-install-global-not-as-root
npm config set prefix '~/.local/'
echo -e '-------------------- NODE: (END) npm config ~/.local --------------------\n'

# curl -sSL https://deb.nodesource.com/setup_$NODE_VER.x | sudo -E bash - || \
#     curl -sSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - && \
#     sudo apt-get install -y --no-install-recommends nodejs && \
```

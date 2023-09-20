---
tags: [node, node/conf]
---

```bash
#!/bin/bash

curl -sSL https://deb.nodesource.com/setup_$NODE_VER.x | sudo -E bash - || \
    curl -sSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - && \
    sudo apt-get install -y --no-install-recommends nodejs
```

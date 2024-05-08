---
tags: bash, fzf, sed
---

```bash
#!/bin/bash

rm -rf /tmp/nvim* \
  && mkdir -p /tmp/nvim \
  && wget -P /tmp https://github.com/ralphie02/nvim-build/releases/latest/download/nvim.tar.gz \
  && tar xvf /tmp/nvim.tar.gz -C /tmp/nvim \
  && sudo mkdir -p /opt/bin \
  && sudo rm -rf /opt/nvim \
  && sudo mv /tmp/nvim/** /opt/nvim \
  && sudo ln -sf /opt/nvim/bin/nvim /opt/bin/nvim
```

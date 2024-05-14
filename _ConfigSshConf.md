---
tags: [ssh, ssh/conf]
---

```bash
#!/bin/bash

echo -e '-------------------- SSH CONF: (START) --------------------\n'

# --------------- SSH_CFG END BLOCK --------------- #
read -rd '' SSH_CFG << 'EOF'
Host *
    StrictHostKeyChecking no
EOF
# --------------- SSH_CFG END BLOCK --------------- #

${BLOCK_SCRIPT_PATH} ~/.ssh/config "$SSH_CFG"
echo -e '-------------------- SSH CONF: (END) --------------------\n'
```

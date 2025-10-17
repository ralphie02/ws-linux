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

    # For Mac?
    # AddKeysToAgent yes
    # UseKeychain yes
    # bash> ssh-add -D
    # bash> ssh-add -K ~/.ssh/*
EOF
# --------------- SSH_CFG END BLOCK --------------- #

$BLOCK_SCRIPT_PATH ~/.ssh/config "$SSH_CFG" $CFG_BASENAME
echo -e '-------------------- SSH CONF: (END) --------------------\n'
```

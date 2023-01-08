```bash
!#/bin/bash

# --------------- SSH_CFG END BLOCK --------------- #
read -rd '' SSH_CFG << 'EOF'
Host *
    StrictHostKeyChecking no
EOF
# --------------- SSH_CFG END BLOCK --------------- #

${ADD_CFG_BLOCK_PATH} ~/.ssh/config "$SSH_CFG"
```
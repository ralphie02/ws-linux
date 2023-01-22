```bash
#!/bin/bash

# --------------- SSH_CFG END BLOCK --------------- #
read -rd '' SSH_CFG << 'EOF'
Host *
    StrictHostKeyChecking no
EOF
# --------------- SSH_CFG END BLOCK --------------- #

${BLOCK_SCRIPT_PATH} ~/.ssh/config "$SSH_CFG"
```
---
tags: [psql, psql/conf]
---

```bash
#!/bin/bash

sudo apt install -y libpq-dev && \
    sudo apt install -y postgresql-$PSQL || sudo apt install -y postgresql && \
    sudo service postgresql start && \
    sudo -u postgres createuser $USER -s

# --------------- PSQL_BASHRC BEGIN BLOCK --------------- #
read -rd '' PSQL_BASHRC << 'EOF'
if ! pgrep -x "postgres" >/dev/null; then sudo service postgresql start;  fi
EOF
# --------------- PSQL_BASHRC END BLOCK --------------- #

${BLOCK_SCRIPT_PATH} ~/.bashrc "$PSQL_BASHRC" '##------ BEGIN PSQL BLOCK' '##------ END PSQL BLOCK'
```

---
tags: [psql, psql/conf]
---

```bash
#!/bin/bash

echo -e '-------------------- PSQL: (START) Pkg install --------------------\n'
sudo apt install -y libpq-dev && \
  [ "$PSQL" != "" ] && LANG=en_US.UTF-8 LC_ALL=C sudo apt install -y postgresql-$PSQL || \
  LANG=en_US.UTF-8 LC_ALL=C sudo apt install -y postgresql
echo -e '-------------------- PSQL: (END) Pkg install --------------------\n'

echo -e '-------------------- PSQL: (START) Service start --------------------\n'
sudo service postgresql start
echo -e '-------------------- PSQL: (END) Service start --------------------\n'

echo -e '-------------------- PSQL: (START) Create $USER as postgres --------------------\n'
sudo -u postgres createuser $USER -s
echo -e '-------------------- PSQL: (END) Create $USER as postgres --------------------\n'
    #createdb -p 5432

echo -e '-------------------- PSQL: (START) Insert block to ~/.bashrc --------------------\n'
# --------------- PSQL_BASHRC BEGIN BLOCK --------------- #
read -rd '' PSQL_BASHRC << 'EOF'
if ! pgrep -x "postgres" >/dev/null; then sudo service postgresql start;  fi
EOF
# --------------- PSQL_BASHRC END BLOCK --------------- #

${BLOCK_SCRIPT_PATH} ~/.bashrc "$PSQL_BASHRC" '##------ BEGIN PSQL BLOCK' '##------ END PSQL BLOCK'
echo -e '-------------------- PSQL: (END) Insert block to ~/.bashrc --------------------\n'
```

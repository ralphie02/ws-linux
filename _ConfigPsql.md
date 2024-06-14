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

echo -e '-------------------- PSQL: (START) Update psql encoding --------------------\n'
if cat /etc/*os-release | grep -q "ID=debian"; then
  sudo -u postgres psql -c "update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'postgres';"
  sudo -u postgres psql -c "update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'template0';"
  sudo -u postgres psql -c "update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'template1';"
fi
echo -e '-------------------- PSQL: (END) Update psql encoding --------------------\n'
#sudo -u postgres createuser $USER -s
createdb -p 5432

echo -e '-------------------- PSQL: (START) Insert block to ~/.bashrc --------------------\n'
# --------------- PSQL_BASHRC BEGIN BLOCK --------------- #
read -rd '' PSQL_BASHRC << 'EOF'
if ! pgrep -x "postgres" >/dev/null; then sudo service postgresql start;  fi
EOF
# --------------- PSQL_BASHRC END BLOCK --------------- #

$BLOCK_SCRIPT_PATH ~/.bashrc "$PSQL_BASHRC" $CFG_BASENAME
echo -e '-------------------- PSQL: (END) Insert block to ~/.bashrc --------------------\n'
```

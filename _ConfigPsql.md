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

echo -e '-------------------- PSQL: (START) Update conf --------------------\n'
PSQL_VERSION=$(psql --version | cut -d' ' -f3 | cut -d. -f1)
sed -i.bak -E "s/^(host\s+all\s+)(all)(\s+127\.0\.0\.1\/32\s+)(.+$)/# \1\2\3\4\n\1$USER\3trust/; s/^(host\s+all\s+)(all)(\s+::1\/128\s+)(.+$)/# \1\2\3\4\n\1$USER\3trust/" /etc/postgresql/$PSQL_VERSION/main/pg_hba.conf
echo -e '-------------------- PSQL: (END) Update conf --------------------\n'

echo -e '-------------------- PSQL: (START) Service start ------o--------------\n'
sudo service postgresql start
echo -e '-------------------- PSQL: (END) Service start --------------------\n'

echo -e '-------------------- PSQL: (START) Update psql encoding --------------------\n'
if cat /etc/*os-release | grep -q "ID=debian"; then
  sudo -u postgres psql -c "update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'postgres';"
  sudo -u postgres psql -c "update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'template0';"
  sudo -u postgres psql -c "update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'template1';"
fi
echo -e '-------------------- PSQL: (END) Update psql encoding --------------------\n'

# Create the PostgreSQL user with superuser privileges
# sudo -u postgres createuser $USER -s

# # Optional: Set a password for the new user
# (not run; added for info) sudo -u postgres psql -c "ALTER USER $USERNAME WITH PASSWORD 'your_password';"
# (commented out for now) createdb -p 5432

echo -e '-------------------- PSQL: (START) Insert block to ~/.bashrc --------------------\n'
# --------------- PSQL_BASHRC BEGIN BLOCK --------------- #
read -rd '' PSQL_BASHRC << 'EOF'
if ! pgrep -x "postgres" >/dev/null; then sudo service postgresql start;  fi
EOF
# --------------- PSQL_BASHRC END BLOCK --------------- #

$BLOCK_SCRIPT_PATH ~/.bashrc "$PSQL_BASHRC" $CFG_BASENAME
echo -e '-------------------- PSQL: (END) Insert block to ~/.bashrc --------------------\n'
```

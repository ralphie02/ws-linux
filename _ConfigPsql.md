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
: ${PSQL_VER:=$(psql --version | cut -d' ' -f3 | cut -d. -f1)}
GREEN="\033[0;32m"
NO_COLOR="\033[0m"
PG_HBA_MSG="\n${GREEN}UPDATING:\n\nFrom:\nhost\tall\tall\t127.0.0.1/32\t<some-value>\n...\nhost\tall\tall\t::1/128\t<some-value>\n\nTo:\n# host\tall\tall\t127.0.0.1/32\t<some-value>\nhost\tall\tralphie02\t127.0.0.1/32\ttrust\n...\n# host\tall\tall\t::1/128\t<some-value>\nhost\tall\tralphie02\t::1/128\ttrust\n${NO_COLOR}"
echo -e $PG_HBA_MSG
sudo sed -i.bak -E "s/^(host\s+all\s+)(all)(\s+127\.0\.0\.1\/32\s+)(.+$)/# \1\2\3\4\n\1$USER\3trust/; s/^(host\s+all\s+)(all)(\s+::1\/128\s+)(.+$)/# \1\2\3\4\n\1$USER\3trust/" /etc/postgresql/$PSQL_VER/main/pg_hba.conf
echo -e '-------------------- PSQL: (END) Update conf --------------------\n'

echo -e '-------------------- PSQL: (START) Service start --------------------\n'
sudo service postgresql start
echo -e '-------------------- PSQL: (END) Service start --------------------\n'

# echo -e '-------------------- PSQL: (START) Update psql encoding --------------------\n'
# if cat /etc/*os-release | grep -q "ID=debian"; then
#   sudo -u postgres psql -c "update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'postgres';"
#   sudo -u postgres psql -c "update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'template0';"
#   sudo -u postgres psql -c "update pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'template1';"
# fi
# echo -e '-------------------- PSQL: (END) Update psql encoding --------------------\n'

echo -e '-------------------- PSQL: (START) Config $USER --------------------\n'
ROLE_EXIST=$(sudo --login -u postgres psql -tAc "SELECT 1 FROM pg_roles WHERE rolname='$USER'")
if [ $ROLE_EXIST != 1 ]; then
  # Create the PostgreSQL user with superuser privileges
  sudo --login -u postgres createuser $USER -s
else
  echo "Role '$USER' already exists. Skipping"  
fi
echo -e '-------------------- PSQL: (END) Config $USER --------------------\n'

echo -e '-------------------- PSQL: (START) Config DB --------------------\n'
DB_EXIST=$(sudo --login -u postgres psql -lqt | cut -d \| -f 1 | grep -w "$USER")
if [ -z $DB_EXIST ]; then
  createdb -p 5432
else
  echo "Database '$USER' already exists. Skipping"
fi
echo -e '-------------------- PSQL: (END) Config DB --------------------\n'

# # Optional: Set a password for the new user
# (not run; added for info) sudo -u postgres psql -c "ALTER USER $USERNAME WITH PASSWORD 'your_password';"

if grep -qi microsoft /proc/version; then 
echo -e '-------------------- PSQL: (START) Insert block to ~/.bashrc --------------------\n'
# --------------- PSQL_BASHRC BEGIN BLOCK --------------- #
read -rd '' PSQL_BASHRC << 'EOF'
if ! pgrep -x "postgres" >/dev/null; then sudo service postgresql start;  fi
EOF
# --------------- PSQL_BASHRC END BLOCK --------------- #

$BLOCK_SCRIPT_PATH ~/.bashrc "$PSQL_BASHRC" $CFG_BASENAME
echo -e '-------------------- PSQL: (END) Insert block to ~/.bashrc --------------------\n'
fi
```

```bash
#!/bin/bash

sudo apt install -y libpq-dev && \
    sudo apt install -y postgresql-$PSQL || sudo apt install -y postgresql && \
    sudo service postgresql start && \
    sudo -u postgres createuser $USER -s

```

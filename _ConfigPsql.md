```bash
#!/bin/bash

sudo apt install postgresql-$PSQL_VER libpq-dev || \
    sudo apt install postgresql libpq-dev && \
    sudo -u postgres createuser $USER -s
```
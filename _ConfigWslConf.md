```bash
#!/bin/bash

if ! grep -qi microsoft /proc/version; then exit; fi

# --------------- WSL_CONF BEGIN BLOCK --------------- #
read -rd '' WSL_CONF << 'EOF'
[automount]
options = "metadata,umask=22,fmask=11"

[network]
generateHosts = false

# https://github.com/microsoft/WSL/issues/4966
[interop]
appendWindowsPath = false
EOF
# --------------- WSL_CONF END BLOCK --------------- #

sudo ${BLOCK_SCRIPT_PATH} /etc/wsl.conf "$WSL_CONF"
```
---
tags: [wsl, wsl/conf]
---

```bash
#!/bin/bash

if ! grep -qi microsoft /proc/version; then exit; fi

# --------------- WSL_CONF BEGIN BLOCK --------------- #
read -rd '' WSL_CONF << 'EOF'
[automount]
options = "metadata,umask=22,fmask=11"

# command = "<command-1>; <command-2>"
# https://github.com/microsoft/WSL/issues/7915#issuecomment-1163333151
[boot]
command = "service dbus start;"

[network]
generateHosts = false

# https://github.com/microsoft/WSL/issues/4966
[interop]
appendWindowsPath = false

# https://github.com/microsoft/WSL/issues/7368
[wsl2]
guiApplications=false
gpuSupport=false
EOF
# --------------- WSL_CONF END BLOCK --------------- #

sudo ${BLOCK_SCRIPT_PATH} /etc/wsl.conf "$WSL_CONF"
```

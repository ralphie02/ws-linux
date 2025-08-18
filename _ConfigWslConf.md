---
tags: [wsl, wsl/conf]
---
```bash
#!/bin/bash

echo -e '-------------------- WSL CONF: (START) Guard clause --------------------\n'
if ! grep -qi microsoft /proc/version; then exit; fi
echo -e '-------------------- WSL CONF: (END) Guard clause --------------------\n'

echo -e '-------------------- WSL CONF: (START) Insert block to /etc/wsl.conf --------------------\n'
# DEPRECATE:
# [automount]
# options = "metadata,umask=22,fmask=11"
#
# https://github.com/microsoft/WSL/issues/7915#issuecomment-1163333151
# #command = "<command-1>; <command-2>"
# [boot]
# command = "service dbus start;"
#
# https://github.com/microsoft/WSL/issues/7368
# https://github.com/microsoft/WSL/issues/10667
# https://github.com/microsoft/WSL/issues/9454
# [wsl2]
# guiApplications = false
# gpuSupport = false
#
# https://docs.docker.com/desktop/wsl/#prerequisites - tip
# https://learn.microsoft.com/en-us/windows/wsl/wsl-config#experimental-settings

# --------------- WSL_CONF BEGIN BLOCK --------------- #
read -rd '' WSL_CONF << 'EOF'
[boot]
systemd = true

[network]
generateHosts = false
generateResolvConf = false

# https://github.com/microsoft/WSL/issues/4966
[interop]
appendWindowsPath = false

[experimental]
autoMemoryReclaim = gradual
sparseVhd = true
EOF
# --------------- WSL_CONF END BLOCK --------------- #

sudo $BLOCK_SCRIPT_PATH /etc/wsl.conf "$WSL_CONF" $CFG_BASENAME
echo -e '-------------------- WSL CONF: (END) Insert block to /etc/wsl.conf --------------------\n'
```

---
tags: bash, fzf, sed
---
```bash
#!/bin/bash

echo -e '-------------------- ASDF: (START) Set env vars --------------------\n'
[ $(dpkg --print-architecture) = amd64 ] && FILE_ARCH=x86_64 || FILE_ARCH=arm64
ASDF_PATH=$(wget -qO- https://api.github.com/repos/asdf-vm/asdf/releases/latest | grep browser_download_url | cut -d\" -f4 | grep linux | grep -e $FILE_ARCH.tar.gz$)
echo -e '-------------------- ASDF: (END) Set env vars --------------------\n'

echo -e '-------------------- ASDF: (START) Download/extract --------------------\n'
##git -C ~/.asdf pull || git clone https://github.com/asdf-vm/asdf.git ~/.asdf
sudo rm -rf /tmp/asdf* && \
  mkdir -p /tmp/asdf
wget -O /tmp/asdf.tar.gz $ASDF_PATH
tar -xvf /tmp/asdf.tar.gz -C /tmp/asdf
sudo rm -rf /usr/local/asdf && \
  sudo mv /tmp/asdf /usr/local/asdf && \
  sudo ln -sf /usr/local/asdf/asdf /usr/local/bin/asdf
echo -e '-------------------- ASDF: (END) Download/extract --------------------\n'

#echo -e '-------------------- ASDF: (START) Insert block to ~/.bashrc --------------------\n'
## --------------- BASHRC BEGIN BLOCK --------------- #
#read -rd '' BASHRC << 'EOF'
#. "$HOME/.asdf/asdf.sh"
#. "$HOME/.asdf/completions/asdf.bash"
#EOF
## --------------- BASHRC END BLOCK --------------- #
#$BLOCK_SCRIPT_PATH ~/.bashrc "$BASHRC" $CFG_BASENAME
#echo -e '-------------------- ASDF: (END) Insert block to ~/.bashrc --------------------\n'

echo -e '-------------------- ASDF: (START) Insert block to ~/.asdfrc --------------------\n'
# --------------- ASDFRC BEGIN BLOCK --------------- #
read -rd '' ASDFRC << 'EOF'
legacy_version_file = yes
EOF
# --------------- ASDFRC END BLOCK --------------- #
$BLOCK_SCRIPT_PATH ~/.asdfrc "$ASDFRC" $CFG_BASENAME
echo -e '-------------------- ASDF: (END) Insert block to ~/.asdfrc --------------------\n'
```

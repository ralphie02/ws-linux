---
tags: bash, fzf, sed
---

```bash
#!/bin/bash

echo -e '-------------------- ASDF: (START) Download/extract --------------------\n'
git -C ~/.asdf pull || git clone git@github.com/excid3/asdf.git ~/.asdf
echo -e '-------------------- ASDF: (END) Download/extract --------------------\n'

echo -e '-------------------- ASDF: (START) Insert ~/.bashrc block --------------------\n'
# --------------- BASHRC BEGIN BLOCK --------------- #
read -rd '' BASHRC << 'EOF'
. "$HOME/.asdf/asdf.sh"
. "$HOME/.asdf/completions/asdf.bash"
EOF
# --------------- BASHRC END BLOCK --------------- #
${BLOCK_SCRIPT_PATH} ~/.bashrc "$BASHRC"
echo -e '-------------------- ASDF: (END) Insert ~/.bashrc block --------------------\n'

echo -e '-------------------- ASDF: (START) Insert ~/.asdfrc block --------------------\n'
# --------------- ASDFRC BEGIN BLOCK --------------- #
read -rd '' ASDFRC << 'EOF'
legacy_version_file = yes
EOF
# --------------- ASDFRC END BLOCK --------------- #
${BLOCK_SCRIPT_PATH} ~/.asdfrc "$ASDFRC"
echo -e '-------------------- ASDF: (END) Insert ~/.asdfrc block --------------------\n'
```

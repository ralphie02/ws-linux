---
tags: bash, fzf, sed
---

```bash
#!/bin/bash

echo -e '-------------------- FZF: (END) Configs --------------------\n'
git -C ~/.asdf pull || git clone git@github.com/excid3/asdf.git ~/.asdf

# --------------- BASHRC BEGIN BLOCK --------------- #
read -rd '' BASHRC << 'EOF'
. "$HOME/.asdf/asdf.sh"
. "$HOME/.asdf/completions/asdf.bash"
EOF
# --------------- BASHRC END BLOCK --------------- #

${BLOCK_SCRIPT_PATH} ~/.bashrc "$BASHRC"

# --------------- BASHRC BEGIN BLOCK --------------- #
read -rd '' ASDFRC << 'EOF'
legacy_version_file = yes
EOF
# --------------- BASHRC END BLOCK --------------- #

${BLOCK_SCRIPT_PATH} ~/.asdfrc "$ASDFRC"
```

```bash
#!/bin/bash

# --------------- INPUTRC BEGIN BLOCK --------------- #
read -rd '' INPUTRC << 'EOF'
set show-all-if-ambiguous on
set show-all-if-unmodified on
set menu-complete-display-prefix on
set completion-ignore-case on
# disable beep for Linux on Win10
set bell-style none
"\e[1;5C": forward-word
"\e[1;5D": backward-word
"\e[A": history-search-backward
"\e[B": history-search-forward
"\C-s": nop
EOF
# --------------- INPUTRC END BLOCK --------------- #

${ADD_CFG_BLOCK_PATH} ~/.inputrc "$INPUTRC"
```
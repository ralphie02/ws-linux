---
tags: [vim, vimrc]
---

```bash
#!/bin/bash

echo -e '-------------------- VIMRC: (START) Insert block to ~/.vimrc --------------------\n'
# --------------- VIMRC BEGIN BLOCK --------------- #
read -rd '' VIMRC << 'EOF'
syntax on
set background=dark
" disable beep for Linux on Win10
set t_vb=
set tabstop=2 softtabstop=0 expandtab shiftwidth=2 smarttab
:set mouse= "Enables Windows right-click pasting on VIM 8.0
EOF
# --------------- VIMRC END BLOCK --------------- # 

${BLOCK_SCRIPT_PATH} ~/.vimrc "$VIMRC" '""------ BEGIN: VIMRC CFG' '""------ END: VIMRC CFG'
echo -e '-------------------- VIMRC: (END) Insert block to ~/.vimrc --------------------\n'
```

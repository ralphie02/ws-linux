```bash
#!/bin/bash

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

${ADD_CFG_BLOCK_PATH} ~/.vimrc "$VIMRC" '""------ BEGIN BLOCK' '""------ END BLOCK'
```
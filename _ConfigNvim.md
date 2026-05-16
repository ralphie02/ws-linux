---
tags: [bash, fzf, sed]
---
```bash
#!/bin/bash

echo -e '-------------------- NVIM: (START) Install fd|ripgrep|xclip --------------------\n'
sudo apt install  -y --no-install-recommends fd-find ripgrep xclip unzip
echo -e '-------------------- NVIM: (END) Install fd|ripgrep|xclip --------------------\n'

echo -e '-------------------- NVIM: (START) Set env vars --------------------\n'
[ $(dpkg --print-architecture) = amd64 ] && FILE_ARCH=x86_64 || FILE_ARCH=arm64
NVIM_URL=$(wget -qO- https://api.github.com/repos/neovim/neovim/releases/latest | grep browser_download_url | cut -d\" -f4 | grep linux | grep $FILE_ARCH.tar.gz)
NVIM_VER=$(echo $NVIM_URL | cut -d\/ -f8)
echo -e '-------------------- NVIM: (END) Set env vars --------------------\n'

echo -e '-------------------- NVIM: (START) Download/Extract/Install --------------------\n'
if [ $(nvim --version 2>/dev/null | head -n 1 | cut -d' ' -f2) = $NVIM_VER ]; then
  echo -e "$NVIM_VER already installed"
else
  echo -e "Installing $NVIM_VER"
  sudo rm -rf /tmp/nvim* && \
    mkdir -p /tmp/nvim
  wget -O /tmp/nvim.tar.gz $NVIM_URL
  tar -xvf /tmp/nvim.tar.gz -C /tmp/nvim > /dev/null
  #sudo mkdir -p /opt/bin && \
  sudo rm -rf /usr/local/nvim && \
    sudo mv /tmp/nvim/** /usr/local/nvim && \
    sudo ln -sf /usr/local/nvim/bin/nvim /usr/local/bin/nvim
fi
echo -e '-------------------- NVIM: (END) Download/Extract/Install --------------------\n'

echo -e '-------------------- NVIM: (START) LazyVimRah config --------------------\n'
git -C ~/.config/nvim pull || git clone git@github.com:ralphie02/LazyVimRah.git ~/.config/nvim
echo -e '-------------------- NVIM: (END) LazyVimRah config --------------------\n'

echo -e '-------------------- NVIM: (START LAZYGIT) Set env vars --------------------\n'
LAZY_URL=$(wget -qO- https://api.github.com/repos/jesseduffield/lazygit/releases/latest | grep browser_download_url | cut -d\" -f4 | grep $FILE_ARCH | grep linux)
# VERSION=$(echo $LAZY_URL | rev | cut -d\/ -f2 | rev | cut -c2-)
# TAR_FILE=$(echo $LAZY_URL | rev | cut -d\/ -f1 | rev)
# UNTARRED_DIR=$(echo ${TAR_FILE%.*.*})
LAZY_VER=$(echo $LAZY_URL | cut -d\/ -f8)
echo -e '-------------------- NVIM: (END LAZYGIT) Set env vars --------------------\n'

echo -e '-------------------- NVIM: (START) Lazygit download/extract --------------------\n'
if [ "v$(lazygit -v | cut -d, -f4 | cut -d= -f2)" = $LAZY_VER ]; then
  echo -e "$LAZY_VER already installed"
else
  echo -e "Installing $LAZY_VER"
  sudo rm -rf /tmp/lazygit* && \
    mkdir -p /tmp/lazygit
  wget -O /tmp/lazygit.tar.gz $LAZY_URL
  tar -xvf /tmp/lazygit.tar.gz -C /tmp/lazygit
  #sudo mkdir -p /opt/bin && \
  sudo rm -rf /usr/local/lazygit && \
    sudo mv /tmp/lazygit /usr/local/lazygit && \
    sudo ln -sf /usr/local/lazygit/lazygit /usr/local/bin/lazygit
fi
echo -e '-------------------- NVIM: (END) Lazygit download/extract --------------------\n'

echo -e '-------------------- NVIM: (START) Tmux navigator plugin conf --------------------\n'
# (START) Fully written by chatgpt
CONFIG_FILE=~/.tmux.conf.local
MARKER_REGEX="^# -- custom variables -+\$"
UNIQUE_LINE=$(printf "BEGIN: _ConfigNvim.md\nEND: _ConfigNvim.md")

# Check if the unique line already exists
if grep -qF "$UNIQUE_LINE" "$CONFIG_FILE"; then
  echo "Block already exists. No changes made."
else
  # Insert above the marker line using regex
  awk -v regex="$MARKER_REGEX" '
    FNR==NR { block[NR] = $0; next }
    {
      if (!inserted && $0 ~ regex) {
        for (i = 1; i <= length(block); i++) print block[i]
        inserted = 1
      }
      print
    }
  ' <(cat << 'EOF'
##------ BEGIN: _ConfigNvim.md - rah customizations ----------------------------

# Smart pane switching with awareness of Vim splits.
# See: https://github.com/christoomey/vim-tmux-navigator
vim_pattern='(\S+/)?g?\.?(view|l?n?vim?x?|fzf)(diff)?(-wrapped)?'
is_vim="ps -o state= -o comm= -t '#{pane_tty}' \
    | grep -iqE '^[^TXZ ]+ +${vim_pattern}$'"
bind-key -n 'C-h' if-shell "$is_vim" 'send-keys C-h'  'select-pane -L'
bind-key -n 'C-j' if-shell "$is_vim" 'send-keys C-j'  'select-pane -D'
bind-key -n 'C-k' if-shell "$is_vim" 'send-keys C-k'  'select-pane -U'
bind-key -n 'C-l' if-shell "$is_vim" 'send-keys C-l'  'select-pane -R'
tmux_version='$(tmux -V | sed -En "s/^tmux ([0-9]+(.[0-9]+)?).*/\1/p")'
if-shell -b '[ "$(echo "$tmux_version < 3.0" | bc)" = 1 ]' \
    "bind-key -n 'C-\\' if-shell \"$is_vim\" 'send-keys C-\\'  'select-pane -l'"
if-shell -b '[ "$(echo "$tmux_version >= 3.0" | bc)" = 1 ]' \
    "bind-key -n 'C-\\' if-shell \"$is_vim\" 'send-keys C-\\\\'  'select-pane -l'"

bind-key -T copy-mode-vi 'C-h' select-pane -L
bind-key -T copy-mode-vi 'C-j' select-pane -D
bind-key -T copy-mode-vi 'C-k' select-pane -U
bind-key -T copy-mode-vi 'C-l' select-pane -R
bind-key -T copy-mode-vi 'C-\' select-pane -l

##------ END: _ConfigNvim.md - rah customizations ----------------------------
EOF
  ) "$CONFIG_FILE" > "${CONFIG_FILE}.tmp" && mv "${CONFIG_FILE}.tmp" "$CONFIG_FILE"
  echo "Block inserted successfully."
fi
# (END) Fully written by chatgpt
echo -e '-------------------- NVIM: (END) Tmux navigator plugin conf --------------------\n'
```

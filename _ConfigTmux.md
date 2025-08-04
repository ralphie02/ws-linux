---
tags: tmux
---
```bash
#!/bin/bash

echo -e '-------------------- TMUX: (START) Pkg install --------------------\n'
sudo apt install -y --no-install-recommends tmux
echo -e '-------------------- TMUX: (END) Pkg install --------------------\n'

echo -e '-------------------- TMUX: (START) Install gpakosz conf --------------------\n'
git clone --single-branch https://github.com/gpakosz/.tmux.git ~/.tmux
ln -s -f .tmux/.tmux.conf ~/
cp .tmux/.tmux.conf.local ~/
echo -e '-------------------- TMUX: (END) Install gpakosz conf --------------------\n'

echo -e '-------------------- TMUX: (START) Personalize gpakosz conf --------------------\n'
CONFIG_FILE=~/.tmux.conf.local
MARKER_REGEX="^# -- tpm -+\$"
UNIQUE_LINE="# -- rah customizations --"

BLOCK=$(cat <<'EOF'
# -- rah customizations -------------------------------------------------------

# Update status bar
tmux_conf_battery_status_charging="🔌"     # U+1F50C
tmux_conf_battery_status_discharging="🔋"  # U+1F50B
my_tmux_conf_status_right=" #{prefix}#{mouse}#{pairing}#{synchronized}#{?battery_status,#{battery_status},} #{?battery_percentage,#{battery_percentage},} , %R %d-%b | #{username}#{root} "
tmux_conf_theme_status_right=" #[fg=yellow]#(cd #{pane_current_path}; echo \"$(basename $(git rev-parse --show-toplevel)) | $(git rev-parse --abbrev-ref HEAD)\")#[fg=default]$my_tmux_conf_status_right"

# in copy mode, copying selection also copies to the OS clipboard
tmux_conf_copy_to_os_clipboard=true

# https://stackoverflow.com/questions/60309665/neovim-colorscheme-does-not-look-right-when-using-nvim-inside-tmux#comment124479399_60313682 
# which points to https://www.cyfyifanchen.com/blog/neovim-true-color
set-option -ga terminal-overrides ",xterm-256color:Tc"

# increase history size
set -g history-limit 100000

# start with mouse mode enabled
set -g mouse on

# change prefix
unbind space
unbind C-a
set -g prefix2 C-space
bind C-space send-prefix -2

# session
unbind C-c
bind C-n new-session

# window
unbind c
bind n new-window
EOF
)

# Check if the unique line already exists
if grep -qF "$UNIQUE_LINE" "$CONFIG_FILE"; then
  echo "Block already exists. No changes made."
else
  # Insert above the marker line using regex
  awk -v block="$BLOCK\n" -v regex="$MARKER_REGEX" '
    BEGIN { inserted = 0 }
    {
      if (!inserted && $0 ~ regex) {
        print block
        inserted = 1
      }
      print
    }
  ' "$CONFIG_FILE" > "${CONFIG_FILE}.tmp" && mv "${CONFIG_FILE}.tmp" "$CONFIG_FILE"
  echo "Block inserted successfully."
fi

echo -e '-------------------- TMUX: (END) Personalize gpakosz conf --------------------\n'
```
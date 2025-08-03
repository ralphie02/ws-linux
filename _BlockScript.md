---
tags: [bash, sed]
---
```bash
#!/bin/bash

# Inserts a BLOCK_BODY(string) into the specified FILE.
# @example
#   (block uses env vars, don't wrap EOF in ''):
#   read -rd '' CFG_BLOCK << EOF
#   Version=$VERSION
#   EOF
#
#   (block does not use vars, wrap EOF in ''):
#   read -rd '' CFG_BLOCK << 'EOF'
#   Version=1.2
#   EOF
#
#   1. Without a dynamic script path
#     /tmp/block_script.sh /path/to/.configure "$CFG_BLOCK" ${CFG_BASENAME:-'BASHRC CFG'}
#
#     /path/to/.configure (generate default block comments):
#     ##------ BEGIN[_ConfigBashrc]
#     Version=1.2
#     SecondValue=if_exists
#     ##------ END[_ConfigBashrc]
#
#   2. With a dynamic script path
#     $BLOCK_SCRIPT_PATH /path/to/.configure "$CFG_BLOCK" ${CFG_BASENAME:-'BASHRC CFG'} '"'
#
#     /path/to/.configure (override block comments; use vimrc comment instead of '#'):
#     ""------ BEGIN: BASHRC CFG
#     Version=1.2
#     SecondValue=if_exists
#     ""------ END: BASHRC CFG
#
FILE=$1
BLOCK_BODY=$2
COMMENT_CHAR=${4:-#}
BLOCK_BEGIN="$COMMENT_CHAR$COMMENT_CHAR------ BEGIN: $3"
BLOCK_END="$COMMENT_CHAR$COMMENT_CHAR------ END: $3"

# https://stackoverflow.com/a/6287940 - used as ref | delete block between patterns
# https://unix.stackexchange.com/a/303649 - replace block between patterns
# https://stackoverflow.com/a/39269241 - use VAR as replace term
add_cfg_block() {
  local filepath=$(dirname $FILE)
  local block_wrap=$(printf "$BLOCK_BEGIN\n$BLOCK_END")

  local block_body="${BLOCK_BODY//\\/\\\\}"
  block_body="${block_body//\//\\/}"
  block_body="${block_body//&/\\&}"
  block_body="${block_body//$'\n'/\\n}"

  local sed_block="/^${BLOCK_BEGIN}/,/^${BLOCK_END}/c${BLOCK_BEGIN}\n${block_body}\n${BLOCK_END}"

  echo -e '-------------------- BLOCKSCRIPT: (START) --------------------\n'
  GREEN="\033[0;32m"
  NO_COLOR="\033[0m"
  MESSAGE="Inserting block:\n\n${GREEN}${BLOCK_BODY}${NO_COLOR}\n\nTo ${FILE} in ${filepath}\n"
  echo -e "$MESSAGE"
  mkdir -p $filepath && touch $FILE && \
    # Checks if the wrapper (BLOCK_BEGIN & BLOCK_END) exists
    grep -q "$block_wrap" $FILE && \
    # Insert BLOCK_BODY between BEGIN & END
    sed -i "$sed_block" $FILE || \
    # Insert wrapper if check above (grep) is falsey
    printf "\n$block_wrap" >> $FILE && \
    # Insert BLOCK_BODY between BEGIN & END
    sed -i "$sed_block" $FILE
  echo -e '-------------------- BLOCKSCRIPT: (END) --------------------\n'
}
add_cfg_block
```

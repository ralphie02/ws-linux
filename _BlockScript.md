---
tags: [bash, sed]
---

```bash
#!/bin/bash

# Inserts a BLOCK_BODY(string) into the specified FILE.
# @example
#   (block uses env vars, don't wrap EOF in ''):
#   read -rd '' VAR_NAME << EOF
#   Version=$VERSION
#   EOF
#
#   (block does not use vars, wrap EOF in ''):
#   read -rd '' VAR_NAME << 'EOF'
#   Version=1.2
#   EOF
#
#   1. Without a dynamic script path
#     /tmp/block_script.sh ~/.file/path "$VAR_NAME" '# BEGIN COMMENT LINE NEEDS' 'TO BE UNIQUE PER FILE'
#
#   2. With a dynamic script path
#     ${BLOCK_SCRIPT_PATH} ~/.file/path "$VAR_NAME" '##------ BEGIN[<FILESOURCE>] CFG' '##------ END[<FILESOURCE>] CFG'
#
FILE=$1
BLOCK_BODY=$2
BLOCK_BEGIN=${3:-'##------ BEGIN BLOCK'}
BLOCK_END=${4:-'##------ END BLOCK'}

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

```bash
#!/bin/bash

FILE=$1
BLOCK_BODY=$2
BLOCK_BEGIN=${3:-'##------ BEGIN BLOCK'}
BLOCK_END=${4:-'##------ END BLOCK'}

# https://stackoverflow.com/a/6287940 - used as ref | delete block between patterns
# https://unix.stackexchange.com/a/303649 - replace block between patterns
# https://stackoverflow.com/a/39269241 - use VAR as replace term 
add_cfg_block () {
  local filepath=$(dirname $FILE)
  local block_wrap=$(echo -e "$BLOCK_BEGIN\n$BLOCK_END")

  local block_body="${BLOCK_BODY//\\/\\\\}"
  block_body="${block_body//\//\\/}"
  block_body="${block_body//&/\\&}"
  block_body="${block_body//$'\n'/\\n}"

  mkdir -p $filepath && touch $FILE && \
    grep "$block_wrap" $FILE && \
    sed -i "/^${BLOCK_BEGIN}/,/^${BLOCK_END}/c${BLOCK_BEGIN}\n${block_body}\n${BLOCK_END}" $FILE || \
    echo -e "$BLOCK_BEGIN\n$block_body\n$BLOCK_END" >> $FILE
}
add_cfg_block
```
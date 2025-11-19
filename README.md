mkdir ~/telnet-dict
cd ~/telnet-dict

cat > dict.txt << 'EOF'
apple:A common fruit, usually red, green, or yellow.
banana:A long yellow tropical fruit.
gandhi:Indian leader (example entry).
linux:An open-source operating system kernel.
hello:A greeting or expression of goodwill.
EOF

cat > dict_handler.sh << 'EOF'
#!/bin/sh

DICT_FILE="dict.txt"

printf "Welcome to Simple Telnet Dictionary\r\n"
printf "Type a word and press ENTER. Type 'quit' to exit.\r\n"
printf "\r\n"

while true; do
  printf "word> "

  if ! IFS= read -r line; then
    break
  fi

  word=$(printf "%s" "$line" | awk '{$1=$1;print}')

  case "$word" in
    "" )
      printf "Please type a word or 'quit'.\r\n"
      continue
      ;;
    quit|exit )
      printf "Goodbye!\r\n"
      break
      ;;
  esac

  result=$(awk -F: -v w="$word" 'tolower($1)==tolower(w){print substr($0, index($0,$2))}' "$DICT_FILE" | sed 's/^://')

  if [ -n "$result" ]; then
    printf "Meaning: %s\r\n\r\n" "$result"
  else
    printf "Not found in dictionary.\r\n\r\n"
  fi
done
EOF

chmod +x dict_handler.sh


./dict_handler.sh

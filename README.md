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



<img width="392" height="200" alt="image" src="https://github.com/user-attachments/assets/cedcf56a-3b51-4a1e-b5f2-79ffabc87bf9" />



This project is a simple Telnet-based dictionary server made using a shell script.
It stores words and meanings in a local file dict.txt.
A handler script dict_handler.sh reads user input over Telnet.
It shows a prompt and waits for the user to type a word.
The script searches the word in dict.txt using a case-insensitive lookup.
If the word exists, it sends back the meaning to the client.
If not, it displays a “Not found” message.
Typing quit ends the session.
socat is used to listen on port 2323 and run the handler for each client.
Users connect by running: telnet localhost 2323.

# voi-node-upgrade
VOI node manually upgrade procedure

This procedure has been tested on an already running node on Ubuntu 22.04.
The procedure used to set up the VOI node is this: https://d13.co/posts/set-up-voi-participation-node/

## 1. Put the node offline
```
getaddress() {
  if [ "$addr" == "" ]; then echo -ne "\nNote: Completing this will remember your address until you log out. "; else echo -ne "\nNote: Using previously entered address. "; fi; echo -e "To forget the address, press Ctrl+C and enter the command:\n\tunset addr\n";
  count=0; while ! (echo "$addr" | grep -E "^[A-Z2-7]{58}$" > /dev/null); do
    if [ $count -gt 0 ]; then echo "Invalid address, please try again."; fi
    echo -ne "\nEnter your voi address: "; read addr;
    addr=$(echo "$addr" | sed 's/ *$//g'); count=$((count+1));
  done; echo "Using address: $addr"
}
getaddress &&\
goal account changeonlinestatus -a $addr -o=0 &&\
sleep 1 &&\
goal account dump -a $addr | jq -r 'if (.onl == 1) then "You are online!" else "You are offline." end'
```

## 2. Stop the node

```
sudo systemctl stop voi && sudo systemctl disable voi && echo OK
```

## 3. Upgrade the node

```
sudo apt update &&\
suao apt upgrade -y
```

## 4. Restart the node

```
sudo systemctl start voi && sudo systemctl enable voi && echo OK
```

## 5. Put the node online

```
getaddress() {
  if [ "$addr" == "" ]; then echo -ne "\nNote: Completing this will remember your address until you log out. "; else echo -ne "\nNote: Using previously entered address. "; fi; echo -e "To forget the address, press Ctrl+C and enter the command:\n\tunset addr\n";
  count=0; while ! (echo "$addr" | grep -E "^[A-Z2-7]{58}$" > /dev/null); do
    if [ $count -gt 0 ]; then echo "Invalid address, please try again."; fi
    echo -ne "\nEnter your voi address: "; read addr;
    addr=$(echo "$addr" | sed 's/ *$//g'); count=$((count+1));
  done; echo "Using address: $addr"
}
getaddress &&\
goal account changeonlinestatus -a $addr -o=1 &&\
sleep 1 &&\
goal account dump -a $addr | jq -r 'if (.onl == 1) then "You are online!" else "You are offline." end'
```

# voi-node-upgrade
VOI node manually upgrade procedure

This procedure has been tested on an already running node on Ubuntu 22.04.
The procedure used to set up the VOI node is this: https://d13.co/posts/set-up-voi-participation-node/

## 0. If you want you can check the node status (online / offline)

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
goal account dump -a $addr | jq -r 'if (.onl == 1) then "You are online!" else "You are offline." end'
```

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
sudo apt upgrade -y
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

## 6. Check if everything is ok

```
goal node status
```
should prompt the following output
```
Last committed block: 5153512
Time since last block: 0.9s
Sync Time: 64.4s
Last consensus protocol: https://github.com/algorandfoundation/specs/tree/925a46433742afb0b51bb939354bd907fa88bf95
Next consensus protocol: https://github.com/algorandfoundation/specs/tree/925a46433742afb0b51bb939354bd907fa88bf95
Round for next consensus protocol: 5153513
Next consensus protocol supported: true
Last Catchpoint:
Genesis ID: voitest-v1
Genesis hash: IXnoWtviVVJW5LGivNFc0Dq14V3kqaXuK2u5OQrdVZo=
```
and
```
goal -v
```
should prompt the following output (depending on your version)
```
3298903982081
3.22.1.stable [rel/stable] (commit #08407ca8)
go-algorand is licensed with AGPLv3.0
source code available at https://github.com/algorand/go-algorand
```

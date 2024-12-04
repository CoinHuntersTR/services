---
description: 'Version: 2.2.1'
---

# Upgrade

```
cd avail
# screen -r avail ile screen içinde node durduruyoruz.

rm -rf x86_64-ubuntu-2204-avail-node.tar.gz
wget https://github.com/availproject/avail/releases/download/v2.2.5.1/x86_64-ubuntu-2204-avail-node.tar.gz
tar -xf x86_64-ubuntu-2204-avail-node.tar.gz

# screen -r avail ile screen içinde node tekrar başlatıyoruz. 
./avail-node --chain turing --name <nodename> --validator -d ./node-data
```

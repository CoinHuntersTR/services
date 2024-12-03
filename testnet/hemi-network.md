# Hemi Network

<figure><img src="https://pbs.twimg.com/media/GXws9xzXoAA5XTX?format=jpg&#x26;name=large" alt=""><figcaption></figcaption></figure>

### Gerekli kütüphaneleri yüklüyoruz.

```
sudo apt update -y && sudo apt upgrade -y
```

```
sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev tmux iptables curl nvme-cli git wget make jq libleveldb-dev build-essential pkg-config ncdu tar clang bsdmainutils lsb-release libssl-dev libreadline-dev libffi-dev jq gcc screen unzip lz4 -y
```

```
wget https://github.com/hemilabs/heminetwork/releases/download/v0.7.0/heminetwork_v0.7.0_linux_amd64.tar.gz 
```

<pre><code><strong>tar xvf heminetwork_v0.7.0_linux_amd64.tar.gz  &#x26;&#x26; cd heminetwork_v0.7.0_linux_amd64
</strong></code></pre>

### Şimdi testnet için cüzdan oluşturuyoruz.

```
./keygen -secp256k1 -json -net="testnet" > ~/popm-address.json
```

Cüzdan ve private key bilgilerimizi alıyoruz ve **bir yere not edelim.**

```
cat $HOME/popm-address.json
```

### Faucet

[Discord](https://discord.com/invite/hemixyz) sunucusuna gidiyoruz ve verify işlemi ile twitter adresimizi bağladıktan sonra faucet kanalı açılacak oradan tBTC isteyeceğiz. tBTC adresimizi yukarıdaki popm-address içindeki "pubkey\_hash": "mmmvzFf8t6DG9AKBdBMk6mt14ZFqd6GDcM" yerinde buna benzer bir şey olacak onunla isteyebilirsiniz.

Sunucu durduğunda otomatik re-start yapalım.

```
nano restart_hemi.sh
```

```
#!/bin/bash

restart_count=0
restart_log="restart_log.txt"

while true; do
    # Start the application
    ./popmd

    # Increment the restart count and write to the log file
    ((restart_count++))
    echo "Restart $restart_count at $(date)" >> $restart_log

    # Uygulama çökerse veya durursa, döngü yeniden başlar
    echo "Uygulama durdu. 5 saniye sonra yeniden başlatılacak..."
    sleep 5
done
```

```
sudo chmod +x restart_hemi.sh
```

Şimdi screen açalım

```
screen -S hemi
```

sonrasında ;

```
echo 'export POPM_BTC_PRIVKEY=BURAYA VERİLEN PRİVATE KEY YAZ' >> ~/.bashrc
echo 'export POPM_STATIC_FEE=50' >> ~/.bashrc
echo 'export POPM_BFG_URL=wss://testnet.rpc.hemi.network/v1/ws/public' >> ~/.bashrc
source ~/.bashrc
```

Yukarıdaki Private key kısmına sunucu da verilen private key yazalım.

```
./restart_hemi.sh
```

Bu komutu çalıştırıp, biraz bekleyelim.

<figure><img src="../.gitbook/assets/Ekran görüntüsü 2024-10-15 151513.png" alt=""><figcaption></figcaption></figure>

bu şekilde tx çıktısı alırsanız CTRL A D ile çıkış yapabilirsiniz.

### Birden fazla sunucuda aynı cüzdan ile çalıştırma;

Yukarıdaki adımları cüzdan oluşturma bölümüne kadar aynen yapıyoruz.

```
nano $HOME/popm-address.json
```

bunu açıyoruz.

içerisine

```
{
  "ethereum_address": "",
  "network": "testnet",
  "private_key": "",
  "public_key": "",
  "pubkey_hash": ""
}
```

yukarıda aldığımız cüzdanın bilgilerini giriyoruz ve CTRL X Y enter yapıyoruz.

Sonrasında re-start bölümünden aynen devam ediyoruz. İstediğiniz kadar sunucuya ekleyebilirsiniz.


---
description: Turing Testnet
---

# Installation

## Avail-Turing-Testnet

## Kurulum

### Go Setup

```
sudo apt update
sudo apt install make clang pkg-config libssl-dev build-essential
```

```
cd
sudo mkdir avail
```

```
cd avail
```

### Dosyaları çekiyoruz

```
wget https://github.com/availproject/avail/releases/download/v2.2.0.0-rc1/x86_64-ubuntu-2204-avail-node.tar.gz
```

### Dosyaları zipten çıkartıyoruz.

```
tar -xf x86_64-ubuntu-2204-avail-node.tar.gz
```

### Sevis Oluşturuyoruz...

```
screen -S avail
```

### Node Başlatalım

> \[name your node] yerine Validator ismini giriyoruz.

```
./avail-node --chain turing --name [name your node] --validator
```

### Systemd ile kurulum

> VALIDATORNAME yerine belirlediğimiz ismi yazıyoruz.

```
sudo tee /etc/systemd/system/availd.service > /dev/null <<'EOF'
[Unit]
Description=Avail Validator Node
After=network.target

[Service]
User=root
WorkingDirectory=/root/avail/
ExecStart=/root/avail/avail-node --chain turing --name VALIDATORNAME --validator
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

#### Node başlatalım

```
sudo systemctl daemon-reload
sudo systemctl enable availd.service
sudo systemctl start availd.service
```

```
sudo systemctl status availd.service
```

#### Log Kontrolü

```
journalctl -f -u availd.service
```

# Installation

### Manual Installation <a href="#installation" id="installation"></a>

| CPU   | RAM      | SSD   |
| ----- | -------- | ----- |
| 1 CPU | 2 GB RAM | 20 GB |

Tavsiye Edilen



| CPU   | RAM      | SSD    |
| ----- | -------- | ------ |
| 2 CPU | 4 GB RAM | 20 SSD |

**Glacier Network** Kayıt: [LINK](https://www.glacier.io/points/?inviter=0x13B19fd63d34Be9d3D6d25ebd7142134C0cfc725)

Buradan girip Galxe ve diğer görevleri yapıp puan kazanabilirsiniz. Şimdi ilk olarak BNB ağında test BNB olması gerekiyor. Eğer test BNB'niz yok ise BNB discord kanalından edinebilirsiniz.&#x20;

BNB Discord Kanalı [LINK](https://discord.gg/bnbchain)

BNB ağında test coinlerinizi aldıktan sonra, OPBNB ağına gönderiyoruz. Aşağıdaki linkten BNB - OPBNB ağına bridge yapabilirsiniz.

Bridge :  [LINK](https://opbnb-testnet-bridge.bnbchain.org/deposit)

#### Gerekli Pakelteri yükleyelim;

```
sudo apt update -y && sudo apt upgrade -y
```

```
sudo apt install htop ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev tmux iptables curl nvme-cli git wget make jq libleveldb-dev build-essential pkg-config ncdu tar clang bsdmainutils lsb-release libssl-dev libreadline-dev libffi-dev jq gcc screen unzip lz4 -y
```

#### Docker Kuralım

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
docker version
```

#### Docker Compose Kuralım

```
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)
curl -L "https://github.com/docker/compose/releases/download/$VER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

#### Gerekli İzinleri verelim;

```
sudo groupadd docker
sudo usermod -aG docker $USER
```

#### Glacier Node Başlatalım

> Private-key yazan yere kullanacağınız Metamask Private key'inizi giriyorsunuz.

```
docker run -d -e PRIVATE_KEY=Metamask-Private-Key --name glacier-verifier docker.io/glaciernetwork/glacier-verifier:v0.0.2
```

#### Node Kontrolü

```
docker logs -f glacier-verifier
```

### Automatic Installation <a href="#auto-installation" id="auto-installation"></a>

> Girişte Metamask Private-Keyi'nizi vermeniz yeterli.

```
bash <(wget -qO- https://raw.githubusercontent.com/CoinHuntersTR/props/refs/heads/main/AutoInstall/glacier_setup.sh)
```

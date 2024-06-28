# Worker Node

### **Sistem Gereksinimleri**

**Operating System**: Any modern operating system including Windows, macOS, or Linux\
**CPU**: Minimum of 1/2 core.\
**Memory**: 2 to 4 GB.\
**Storage**: SSD or NVMe with at least 5GB of space.

Sistem gereksinimleri düşük olduğu için, çalıştırdığınız bir sunucu içine ekleyebilirsiniz.

### Kurulum

#### Güncelleme ve Paket yüklemelerini yapıyoruz.

```bash
sudo apt update & sudo apt upgrade -y

sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make jq build-essential pkg-config lsb-release libssl-dev libreadline-dev libffi-dev gcc screen unzip lz4 -y

sudo apt install python3
sudo apt install python3-pip
```

#### Docker Yükleyelim

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)

curl -L "https://github.com/docker/compose/releases/download/"$VER"/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

sudo groupadd docker
sudo usermod -aG docker $USER
```

#### Go Kuralım

```bash
sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.22.4.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> $HOME/.bash_profile
source .bash_profile
```

#### Allora  Ağı ve Cüzdan kurulumu

```bash
git clone https://github.com/allora-network/allora-chain.git
cd allora-chain && make all
```

Cüzdan açıyoruz.

> `WalletName` yerine istediğiniz bir isim verebilirsiniz.

> Cüzdanı açarken sizden iki kere şifre isteyecek aynı şifreleri giriyoruz. Sonrasında, cüzdan adresinizi ve gizli kelimelerinizi verecek, bir yere not etmeyi unutmayın.

```bash
allorad keys add WalletName
```



### Allora Kayıt ve Cüzdan Aktarma

> Öncelikle yukarıkdaki komutta oluşturduğumuz cüzdanımızın gizli kelimeleriyle Keplr wallet'a cüzdanımızı import ediyoruz.

> Cüzdanımıza Allora ağını eklemek için [BURADAN ](https://explorer.edgenet.allora.network/wallet/suggest)cüzdanımızı siteye bağlıyor ve ağı ekliyoruz. Ağı ekledikten sonra Keplr Wallet Manange Chain Visibility sekmesine basım tüm ağları kabul ederek, Allora ağına ulaşabilirsiniz.

> [BURADAN ](https://app.allora.network/?ref=eyJyZWZlcnJlcl9pZCI6IjA3YjViODQzLWUyOTItNDEwYy04NGVjLWNlZmQ0ZWU5MDA4ZiJ9)Allora Dashboard'a gidiyoruz ve Keplr wallet'ımız ile bağlanıyoruz.



### Worker Başlatalım

```bash
cd $HOME
git clone https://github.com/allora-network/basic-coin-prediction-node

cd basic-coin-prediction-node
```

Dosyalarımızı oluşturalım

```bash
mkdir worker-data
mkdir head-data
sudo chmod -R 777 worker-data
sudo chmod -R 777 head-data
```

Head key oluşturma

```bash
sudo docker run -it --entrypoint=bash -v ./head-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"
```

Word key oluşturma

```bash
sudo docker run -it --entrypoint=bash -v ./worker-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"
```

Head keyi alma

```bash
cat head-data/keys/identity
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-28 150618.png" alt=""><figcaption></figcaption></figure>

> Size buna benzer bir çıktı verirse, son bölümde root@Nodes başlayan kısmı almıyorsunuz. Yani, 12D.....b9 gibi bir değeri bir yere not edelim.

```bash
rm -rf docker-compose.yml
```

Öncelikle aşağıdaki dosyayı düzenliyoruz. Bunun içinde;

`HEAD-ID-BURAYA YAZ` ve `GIZLI KELİMELERİNİ BURAYA YAZ` bölümlerini bulup, yukarıda aldığımız cüzdanımızın gizli kelimeleri ile head-id'imizi komutun içine ekleyip ondan sonrasında işlemlere devam ediyoruz.

````bash
```yaml
version: '3'

services:
  inference:
    container_name: inference-basic-eth-pred
    build:
      context: .
    command: python -u /app/app.py
    ports:
      - "8000:8000"
    networks:
      eth-model-local:
        aliases:
          - inference
        ipv4_address: 172.22.0.4
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/inference/ETH"]
      interval: 10s
      timeout: 5s
      retries: 12
    volumes:
      - ./inference-data:/app/data

  updater:
    container_name: updater-basic-eth-pred
    build: .
    environment:
      - INFERENCE_API_ADDRESS=http://inference:8000
    command: >
      sh -c "
      while true; do
        python -u /app/update_app.py;
        sleep 24h;
      done
      "
    depends_on:
      inference:
        condition: service_healthy
    networks:
      eth-model-local:
        aliases:
          - updater
        ipv4_address: 172.22.0.5

  worker:
    container_name: worker-basic-eth-pred
    environment:
      - INFERENCE_API_ADDRESS=http://inference:8000
      - HOME=/data
    build:
      context: .
      dockerfile: Dockerfile_b7s
    entrypoint:
      - "/bin/bash"
      - "-c"
      - |
        if [ ! -f /data/keys/priv.bin ]; then
          echo "Generating new private keys..."
          mkdir -p /data/keys
          cd /data/keys
          allora-keys
        fi
        # Change boot-nodes below to the key advertised by your head
        allora-node --role=worker --peer-db=/data/peerdb --function-db=/data/function-db \
          --runtime-path=/app/runtime --runtime-cli=bls-runtime --workspace=/data/workspace \
          --private-key=/data/keys/priv.bin --log-level=debug --port=9011 \
          --boot-nodes=/ip4/172.22.0.100/tcp/9010/p2p/HEAD-ID-BURAYA YAZ \
          --topic=1 \
          --allora-chain-key-name=testkey \
          --allora-chain-restore-mnemonic='GIZLI KELİMELERİNİ BURAYA YAZ' \
          --allora-node-rpc-address=https://allora-rpc.edgenet.allora.network/ \
          --allora-chain-topic-id=1
    volumes:
      - ./worker-data:/data
    working_dir: /data
    depends_on:
      - inference
      - head
    networks:
      eth-model-local:
        aliases:
          - worker
        ipv4_address: 172.22.0.10

  head:
    container_name: head-basic-eth-pred
    image: alloranetwork/allora-inference-base-head:latest
    environment:
      - HOME=/data
    entrypoint:
      - "/bin/bash"
      - "-c"
      - |
        if [ ! -f /data/keys/priv.bin ]; then
          echo "Generating new private keys..."
          mkdir -p /data/keys
          cd /data/keys
          allora-keys
        fi
        allora-node --role=head --peer-db=/data/peerdb --function-db=/data/function-db  \
          --runtime-path=/app/runtime --runtime-cli=bls-runtime --workspace=/data/workspace \
          --private-key=/data/keys/priv.bin --log-level=debug --port=9010 --rest-api=:6000
    ports:
      - "6000:6000"
    volumes:
      - ./head-data:/data
    working_dir: /data
    networks:
      eth-model-local:
        aliases:
          - head
        ipv4_address: 172.22.0.100


networks:
  eth-model-local:
    driver: bridge
    ipam:
      config:
        - subnet: 172.22.0.0/24

volumes:
  inference-data:
  worker-data:
  head-data:
```
````

Bu komutu başlatıyoruz.&#x20;

```bash
nano docker-compose.yml
```

Yukarıda düzenlediğimiz dosyayı kopyalayıp içine yapıştıralım. Sonrasında CRTL X+Y enter ile kayıt edelim.

#### Worker Başlatalım

```
docker compose build
docker compose up -d
```

> Container ID bulmak için;

<pre><code><strong>docker ps 
</strong></code></pre>

komutunu çalıştırıyoruz. Size gerekli olan Container ID'nin görselini ekliyorum. Açıklama kısmında worker olanı ID numarasını alın.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-28 151631.png" alt=""><figcaption></figcaption></figure>

Orada yazan `77bade3f9244` benzer ID bir yere not edelim.

```
docker logs -f container_id
# Örnek 
docker logs -f 77bade3f9244
```

Komutunu çalıştırınca

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-28 144933.png" alt=""><figcaption></figcaption></figure>

Görseldeki gibi bir çıktı alıyorsanız. İşlem tamamdır. Belli süreler sonrasında puanınız artmaya başlayacaktır.


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

Kullandığınız cüzdanı içine aktarmak istersenizde aşağıdaki komutu kullanabilirsiniz. `WalletName` yerine istediğiniz bir isim girebilirsiniz.&#x20;

```
allorad keys add WalletName--recover
```

### Allora Kayıt ve Cüzdan Aktarma

> Öncelikle yukarıkdaki komutta oluşturduğumuz cüzdanımızın gizli kelimeleriyle Keplr wallet'a cüzdanımızı import ediyoruz.

> Cüzdanımıza Allora ağını eklemek için [BURADAN ](https://explorer.edgenet.allora.network/wallet/suggest)cüzdanımızı siteye bağlıyor ve ağı ekliyoruz. Ağı ekledikten sonra Keplr Wallet Manange Chain Visibility sekmesine basım tüm ağları kabul ederek, Allora ağına ulaşabilirsiniz.

> [BURADAN](https://faucet.edgenet.allora.network/)  cüzdanımıza Allora token istiyoruz.

> [BURADAN ](https://app.allora.network/?ref=eyJyZWZlcnJlcl9pZCI6IjA3YjViODQzLWUyOTItNDEwYy04NGVjLWNlZmQ0ZWU5MDA4ZiJ9)Allora Dashboard'a gidiyoruz ve Keplr wallet'ımız ile bağlanıyoruz.



### Worker Başlatalım

```bash
cd $HOME
git clone https://github.com/allora-network/allora-huggingface-walkthrough
cd allora-huggingface-walkthrough
```

Dosyalarımızı oluşturalım

```bash
mkdir -p worker-data
chmod -R 777 worker-data
```

#### Nano congif dosyasını düzenleyelim.

```
nano config.json
```

> Aşağıdaki dosyada değişecek parametreleri yazıyorum. "cüzdanadı" bölümüne cüzdanınızı oluştururken hangi ismi yazdıysan onu yazıyorsun.&#x20;

> Cüzdan Kelimeleri bölümüne ise, cüzdanı oluşturduğunda sana verilen gizli kelimeleri yazıyorsun. Diğer bölümlere dokunmana gerek yok.

```
{
    "wallet": {
        "addressKeyName": "cüzdanadı",
        "addressRestoreMnemonic": "Cüzdan kelimeleri",
        "alloraHomeDir": "/root/.allorad",
        "gas": "1000000",
        "gasAdjustment": 1.0,
        "nodeRpc": "https://allora-rpc.testnet-1.testnet.allora.network/",
        "maxRetries": 1,
        "delay": 1,
        "submitTx": false
    },
    "worker": [
        {
            "topicId": 1,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 1,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "ETH"
            }
        },
        {
            "topicId": 2,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 3,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "ETH"
            }
        },
        {
            "topicId": 3,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 5,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "BTC"
            }
        },
        {
            "topicId": 4,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 2,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "BTC"
            }
        },
        {
            "topicId": 5,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 4,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "SOL"
            }
        },
        {
            "topicId": 6,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 5,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "SOL"
            }
        },
        {
            "topicId": 7,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 2,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "ETH"
            }
        },
        {
            "topicId": 8,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 3,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "BNB"
            }
        },
        {
            "topicId": 9,
            "inferenceEntrypointName": "api-worker-reputer",
            "loopSeconds": 5,
            "parameters": {
                "InferenceEndpoint": "http://inference:8000/inference/{Token}",
                "Token": "ARB"
            }
        }
        
    ]
}
```

CTRL X Y enter yapıp kayıt edip çıkıyoruz.

#### Coingecko API Key

Öncelikle Coingecko'dan bir tane hesapl oluşturuyoruz. Yoksa aşağıdaki linkten girip hesap oluşturabilirsiniz.&#x20;

[BURADAN](https://www.coingecko.com/en/developers/dashboard) coingecko sitesine gidiyoruz. Kayıt adımlarınıa hallettikten sonra,

Demo account diyoruz ve kendimize bir API oluşturuyoruz.&#x20;

```
nano app.py
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-08-25 013029.png" alt=""><figcaption></figcaption></figure>

Bu işaretlediğim yeri buluyoruz ve coingeckodan aldığımız API key bu bölüme girip, CTRL X Y enter yapıp kayıt ediyoruz.

#### Worker başlatalım&#x20;

```
chmod +x init.config
./init.config
```

```
docker compose up --build -d
```

#### Log kontrolü yapalım

```
docker ps -a
```

```
docker logs -f container id yazıyoruz. 
```

> Örnek :  docker logs -f 14ce1bc8fe26 gibi sizde container başındaki id'leri alıp logları kontrol edebilirsiniz.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-08-25 013441.png" alt=""><figcaption></figcaption></figure>

Yukarıdaki görsel gibi çıktı alırsanız. işlem tamamdır. Puan kazanmaya başlayabilirsiniz.&#x20;

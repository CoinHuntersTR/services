# Farcaster Node Hubble

### Sistem Gereksinimleri

| CPU  | RAM   | SSD    |
| ---- | ----- | ------ |
| 4CPU | 16 GB | 200 GB |

Öncelikle Farcaster kaydınızın olması gerekiyor. Yıllık 5$ maliyeti var. Eğer üyeliğiniz yok ise [BURADAN ](https://warpcast.com/\~/invite-page/421313?id=b32031df)kayıt olabilirsiniz.&#x20;

> Mobil uygulamasını indiriyoruz ve kayıt cüzdan tanımlama işlemleri sonrasında diğer adımlara geçebiliriz.



### Sistem Güncellemeleri

```bash
sudo apt update && sudo apt upgrade -y
```

```bash
sudo apt install screen -y
```

```bash
screen -S farcaster
```

> screen ekranına daha sonradan ulaşmak için `screen -r farcaster` komutunu girerek ulaşabilirsiniz. İşiniz bittiğinde screenden CTRL A+D ile çıkabilirsiniz.&#x20;



### Docker Kurulumu

```bash
sudo apt-get update
```

```bash
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```bash
echo \
  "deb [arch=$$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```bash
sudo apt-get update
```

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$$(uname -s)-$$(uname -m)" -o /usr/local/bin/docker-compose
```

```bash
sudo chmod +x /usr/local/bin/docker-compose
```



### Farcaster Kurulumu

```bash
curl -sSL https://download.thehubble.xyz/bootstrap.sh | bash
```

> Bu bölümde sizden iki tane farklı RPC isteyecek.

> 1. olarak ETH Mainnet RPC'si 2. Olarak da Op Mainnet RPC'si bunları almak için infura sitesini kullanabilirsiniz. [BURADAN ](https://app.infura.io/dashboard)infura sitesine girip,&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-25 144910.png" alt=""><figcaption></figcaption></figure>

Hem ETH hem de OP için RPC alabilirsiniz.&#x20;

> 3. olarak Farcaster ID yani FID isteyecek bunun için Profile sekmesinden sağ üste Edit Profile yanındaki 3 noktaya basıyoruz. About kısmına basınca hem cüzdan adresinizi hem de FID numaranızı görebilirsiniz.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-25 145019.png" alt=""><figcaption></figcaption></figure>

Bunları girdikten sonra; Bir süre bekledikten sonra aşağıdaki gibi çıktı alıyorsanız. İşlem tamamlanmıştır.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-25 142209.png" alt=""><figcaption></figcaption></figure>

Doğru kurup kurmadığınızı kontrol etmek için;

```bash
docker logs hubble-hubble-1 2>&1 | grep "Hub Operator FID"
```

screen dışında bu komutu çalıştırın, kullanıcı adınızı ve FID görüyorsanız sıkıntı yok demektir.

Şimdi de bize gerekli olan portları açalım;

```bash
sudo ufw enable
```

```bash
sudo ufw allow 22
sudo ufw allow 2281
sudo ufw allow 2282
sudo ufw allow 3000
```



### Grafana ile sunucu izleme

```bash
http://sunucu-ip adresiniz:3000/
```

Yukarıdaki linke kendi sunucu adresinizi yazarak tarayıcı da çalıştırdığınızda aşağıdaki gibi bir görsel alıyorsanız. İşlem tamamdır.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-25 145823.png" alt=""><figcaption></figcaption></figure>

# Installation

### Önemli Notlar <a href="#setup-validator-name" id="setup-validator-name"></a>

> Mevcutta çalışan bir sunucunuzda çalıştırabilirsiniz. Donanımsal sıkıntı yok.

> İlk işlem olarak [BURADAN ](https://app.pingpong.build/points?invite\_code=oshGUg2u)siteye girip Metamask cüzdanımızla bağlanıyoruz.&#x20;

### Kullanıcı bölümü

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-09 003202.png" alt=""><figcaption></figcaption></figure>

> Burada Points bölümüne giriyoruz. Kullanıcı işlemleri var. Faucetten token talep ediyoruz. Bridge ile Morph Holesy ağına geçiriyoruz. Twittwer, Discord gibi sosyal medya görevlerini yapıyoruz. Puanlarımızı topluyoruz.

### Kurulum

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-09 003339.png" alt=""><figcaption></figcaption></figure>

Önce More butonuna basıyoruz, sonrasında DePIN Harvester kısmına basıyoruz.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-09 003534.png" alt=""><figcaption></figcaption></figure>

Sonrasında Devices ve ardından Add Devices butonuna basıyoruz.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-09 003707.png" alt=""><figcaption></figcaption></figure>

Device Name kısmına istediğiniz bir isim verebilirsiniz. Sonraki adımda Linux seçiyoruz ve Let's Gooo! butonuna basıyoruz.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-09 001710.png" alt=""><figcaption></figcaption></figure>

Your device key, bir yere not ediyoruz.&#x20;

> Platformda yapacağımız işlemler bu kadar. Şimdi Sunucumuza geçelim.

```bash
wget https://pingpong-build.s3.ap-southeast-1.amazonaws.com/linux/latest/PINGPONG
```

#### Docker kurulumu

```bash
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

```bash
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

```bash
sudo apt-get update
```

```bash
sudo apt-get install docker-ce
sudo systemctl start docker
sudo systemctl enable docker

sudo usermod -aG docker $USER

docker run hello-world
```

```bash
screen -S pingpong
```

> Aşağıdaki komutu girmeden önce, DEVICEKEY yazan yere biraz önce görselde aldığım keyi giriyoruz.

```bash
chmod +x ./PINGPONG && ./PINGPONG --key DEVICEKEY
```

> Screen içinden CTRL A+D ile çıkıyoruz. Aşağıdaki gibi görünüyorsa sorun olmadan çalışıyor demektir.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-09 004440.png" alt=""><figcaption></figcaption></figure>


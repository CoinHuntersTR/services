# Verifier Program

#### Gereksinimler

Ubuntu 22.04&#x20;

| **CPU** | 4 vCPU |
| ------- | ------ |
| **RAM** | 8 GB   |
| **SSD** | 80 GB  |

```bash
sudo apt update && sudo apt upgrade -y
```

```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

```bash
sudo apt install screen
```

**Docker Kurulumunu yapıyoruz.**

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

```bash
 echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```bash
sudo apt update
sudo apt install docker-ce
```

```bash
docker --version
```

Docker 27.1.1 ve üzeri bir version sonucu alırsanız işlem tamamdır.&#x20;

#### Docker test edelim.

```bash
docker container run --rm hello-world
```

**Accuser yüklüyoruz.**

```bash
docker pull nillion/retailtoken-accuser:v1.0.0
```

yeni bir dizin oluşturalım

```bash
mkdir -p nillion/accuser
```

```bash
screen -S verifier
```

> Yeni bir screen açalım ve komutları burada çalıştırmaya devam edelim.

```bash
docker run -v ./nillion/accuser:/var/tmp nillion/retailtoken-accuser:v1.0.0 initialise
```

> Bu komutu çalıştırdığınızda, size iki tane parametre verecek bu parametreleri bir yere not edelim.
>
>
>
> * accound\_id: Nillion address of the accuser
> * public\_key: Public Key of the accuser
>
> Bunları bir yere not edelim.&#x20;

<figure><img src="https://nillion.com/wp-content/uploads/2024/08/5-1024x554.png" alt=""><figcaption></figcaption></figure>

Görselde olduğu gibi ilk kutucuğa, sunucu içinde size verilen nillion12... diye başlayan adresi giriyoruz. İkinci bölüme ise accound-id yada pub key adresimizi giriyoruz ve onaylıyoruz.&#x20;

[BURADAN](https://faucet.testnet.nillion.com/)  sunucu içindeki nillion adresimize test tokeni istiyoruz. Bir süre bekleyin test tokeni geldikten sonra diğer adıma geçeceğiz.



{% hint style="danger" %}
Bu adıma devam etmeden önce 1 saat kadar bekliyoruz.&#x20;
{% endhint %}

```bash
docker run -v ./nillion/accuser:/var/tmp nillion/retailtoken-accuser:v1.0.0 accuse --rpc-endpoint "http://51.89.195.146:26657" --block-start 5033189
```

screen den çıkmak için; CTRL A D'yi kullanabilirsiniz. tekrar screen içine girmek için

```bash
screen -r verifier
```

Tüm adımları başarılı şekilde yaptığınızda ise aşağıdaki gibi verifier sonuçlarınızı siteden takip edebilirsiniz.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-08-27 234810.png" alt=""><figcaption></figcaption></figure>

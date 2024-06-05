# Rollup Evm Airchains

### Sistem Gereksinimleri

Ubuntu 22.04

| CPU | RAM | SSD    |
| --- | --- | ------ |
| 2   | 4   | 100SSD |

### Kurulum

#### Sistem Güncellemesi

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip screen -y
```



#### Go Yükleyelim

```
cd $HOME && \
ver="1.21.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

#### Gerekli Dosyalaları alalım

```
git clone https://github.com/airchains-network/evm-station.git
```

```
git clone https://github.com/airchains-network/tracks.git
```

```
git clone https://github.com/availproject/availup.git
```

#### EVM-Station Kurulumu

```
screen -S evms
```

```
cd evm-station
```

```
go mod tidy
```

```
/bin/bash ./scripts/local-setup.sh
```

#### EVM-Station başlatıyoruz.

```
/bin/bash ./scripts/local-start.sh
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 145902.png" alt=""><figcaption></figcaption></figure>

EVM station başlattığınızda yukarıdaki görseldeki gibi bloklar akmaya başladığında CTRL A + D ile screenden çıkıyoruz. tekrar girmek istediğimizde `screen -r evms` komutuyla blokların aktığı yere ulaşabilirsiniz.&#x20;

#### Airchain Rollup Key

```
cd evm-station
/bin/bash ./scripts/local-keys.sh
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 143435.png" alt=""><figcaption></figcaption></figure>

> Buradakine benzer bir KEY alacaksınız. Bunu bir yere not etmeyi unutmayın.

#### Avail light node

```
screen -S avail
```

```
cd availup
/bin/bash availup.sh --network "turing" --app_id 36
```

> Bu adımlardan sonra aşağıdaki görselde olduğu gibi size Public adresinizi vs verecek onları bir yere not edelim.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 143547.png" alt=""><figcaption></figcaption></figure>

> Aşağıdaki görseldeki gibi bloklar akmaya başladığında CTRL A+ D ile çıkıyoruz.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 143642.png" alt=""><figcaption></figcaption></figure>

#### Avail Cüzdan Kelimelerini alma ve Faucet

```
nano /root/.avail/identity/identity.toml
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 143826.png" alt=""><figcaption></figcaption></figure>

> Yukarıdaki görseldeki gibi kelimeleriniz tırnak içinde yazar. Onları bir yere not edersiniz. Ayrıca bize Test avail'de lazım. Onun için Cüzdan adresinizle, `Avail ss58 address: 5...` ile başlayan sizin AVAIL adresiniz, kayıt etmiştik. Eğer etmediyseniz sıkıntı yok. Tasliman wallet kelimelerinizi import edin yine aynı adresi verecektir.&#x20;

> [Buradan ](https://faucet.avail.tools/)faucete gidip test AVAIL istiyoruz.

#### Track kurulumu

```
cd
cd tracks
```

```
go mod tidy
```

> AVAIL kelimeler yerine biraz önce aldığınız cüzdan kelimelerini yazıyoruz.&#x20;
>
> Moniker Adınızı Yazınız yerine Bir isim yazmanız yeterli.

```
go run cmd/main.go init --daRpc "http://127.0.0.1:7000" --daKey "AVAIL Kelimeler" --daType "avail" --moniker "Moniker Adınızı Yazınız" --stationRpc "http://127.0.0.1:16545" --stationAPI "http://127.0.0.1:16545" --stationType "evm"
```

```
go run cmd/main.go keys junction --accountName cüzdan-ismi-yazınız --accountPath $HOME/.tracks/junction-accounts/keys
```

> Yukarıdaki komutta `cüzdan-ismi-yazınız` yerine bir isim yazıyoruz. Sonrasında çalıştırdığınızda aşağıdaki görselde olduğu gibi bir çıktı almanız gerekiyor. Airchains cüzdan adresiniz ve gizli kelimelerinizi verecek onu bir yere not ediyoruz.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 144321.png" alt=""><figcaption></figcaption></figure>

> Bu adımı yaptıktan sonra discorddan fauceti kullanarak airchains cüzdanınıza test tokeni istiyoruz.

Fauceti kullanmak için aşağıdaki gibi faucet kanalına cüzdan adresimizi atıyoruz.

```
$faucet air1....
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 144436.png" alt=""><figcaption></figcaption></figure>

#### Prover Başlatıyoruz.

```
go run cmd/main.go prover v1EVM
```

> Aşağıdaki gibi bir çıktı elde ettiyseniz işlemlere devam ediyoruz.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 144510.png" alt=""><figcaption></figcaption></figure>

#### Junction oluşturalım

> Öncelikle bize node ID lazım onun için aşağıdaki dosyayı açıyoruz ve p2p bölümünde Node-ID yazıyor onu bir yere not edelim.

```
nano /root/.tracks/config/sequencer.toml
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 144920.png" alt=""><figcaption></figcaption></figure>

```
go run cmd/main.go create-station --accountName cüzdan-adını-yazınız --accountPath $HOME/.tracks/junction-accounts/keys --jsonRPC "https://airchains-testnet-rpc.cosmonautstakes.com/" --info "EVM Track" --tracks cüzdan-adresini-yazınız --bootstrapNode "/ip4/SUNUCU-IP/tcp/2300/p2p/NODE-ID-yazınız"
```

> Şimdi yukarıdaki komutu düzenleyip sonrasında terminale yazıyoruz.
>
> `cüzdan-adını-yazınız` yerine yukarıdak vermiş olduğumuz cüzdan adını yazıyoruz.&#x20;
>
> `cüzdan-adresini-yazınız` yerine air1... ile başlayan cüzdan adresimizi ekliyoruz.&#x20;
>
> &#x20;`SUNUCU-IP` yerine kiralamış olduğunuz sunucunun IP adresini yazıyoruz.&#x20;
>
> `NODE-ID-yazınız` yerine biraz önce aldığımız node-ıd ekliyoruz.&#x20;

#### Rollup Başlatalım

```
screen -S rollup
```

```
go run cmd/main.go start
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 153553.png" alt=""><figcaption></figcaption></figure>

Bu şekilde çıktı alırsanız CTRL A+D ile çıkış yapabilirsiniz. Puanlarınızı takip etmek için [BURADAN ](https://points.airchains.io/)airchains sitesine gidiyoruz. Node için kurduğumuz cüdanı Leap wallet import edip onunla siteye bağlanıyoruz. Aşağıdaki gibi bir sonuç görürseniz. İşlem tamamdır puanların gelmesi uzun sürebiliyor.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-04 153532.png" alt=""><figcaption></figcaption></figure>

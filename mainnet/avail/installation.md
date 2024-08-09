# Installation

## Avail-DA-Mainnet

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
wget https://github.com/availproject/avail/releases/download/v2.2.4.1/x86_64-ubuntu-2204-avail-node.tar.gz
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

> \[name your node] yerine istediğiniz bir ismi girebilirsiniz.&#x20;

```
./avail-node --chain mainnet --name [name your node] --validator
```

> Kurulum sonrasında validatör'ünüzün eşleşip eşleşmediğini [BURADAN ](https://telemetry.avail.so/#list/0xb91746b45e0346cc2f815a520b9c6cb4d5c0902af848db0a80f85932d2e8276a)kontrol edebilirsiniz. Ekradan Nodeisminizi aratmanız yeterli.&#x20;

> Eşleştikten sonra [BURADAN](https://docs.availproject.org/docs/operate-a-node/become-a-validator/stake-your-validator) stake adımlarını takip edip, aktif set için bekleyebilirsiniz. Şuan bekleme listesine girmek için minimum 50.000 AVAIL stake etmeniz gerekiyor.&#x20;

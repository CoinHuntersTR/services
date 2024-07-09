# Linux Client

Daha önce windows serverlar için Rivalz testnetine katılmıştık. Dileyenler oradan kurup devam edebilir. [BURADAN ](https://coinhunterstr.com/rivalz-odullu-testnet-rehberi/)windows server kurulumuna ulaşabilirsiniz.&#x20;

İlk defa katılacaksanız, kaçırdığınız bir şey yok [BURADAN](https://rivalz.ai/?r=0xmtnslk) girip siteye kayıt olduktan sonra da işlemlere başlayabilirsiniz.&#x20;

Şimdi çalışan herhangi bir node'unuzun yanına ekleyip çalıştırabilirsiniz.&#x20;

#### Gereksinimler

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
source ~/.bashrc

nvm install 20.0.0
nvm use 20.0.0
```

#### Screen açıyoruz.

```bash
screen -S rivalzNode
```

Client çalıştırılam

```bash
npm i -g rivalz-node-cli
```

```bash
rivalz run
```

> Bu komutu çalıştırdıktan sonra sizden sırasıyla, Metamask adresinizi, CPU değeri RAM değeri ve disk alanı isteyecek, sunucunuzdaki miktara göre bunları belirleyip enter basıyoruz. Aşağıya görselini ekliyorum. Her değer girdikten sonra ENTER basmanız yeterli.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-09 145728.png" alt=""><figcaption></figcaption></figure>

Bu adımı geçtikten sonra aşağıdaki gibi bir çıktı alırsanız, işlem tamamdır. Hata alırsanız son komutu tekrar çalıştırıp değerleri değiştirebilirsiniz.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-09 145811.png" alt=""><figcaption></figcaption></figure>

Bu çıktıyı aldıktan sonra [BURADAN ](https://rivalz.ai/?r=0xmtnslk)siteye gidiyoruz ve Yeni rClientimizi validate ediyoruz. işlem tamamdır. Çalıştırdığınız tüm linux sunucuların yanına ekleyebilirsiniz.&#x20;

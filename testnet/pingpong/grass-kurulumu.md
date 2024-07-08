# Grass Kurulumu

Grass cihazını sunucu içinde çalıştırmak için aşağıdaki adımları yapabilirsiniz.

Daha önce grass çalıştırmadıysanız. [BURADAN](https://app.getgrass.io/register/?referralCode=VuiTMDILfnbpZ0P) girip başlayabilirsiniz. Kayıt ve diğer işlemleri hallettikten sonra Dashboard açıkken F12 yada sağ tık incele yapıyoruz.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-09 001422.png" alt=""><figcaption></figcaption></figure>

> Sırasıyla adımları anlatıyorum. İncele dedikten sonra sağdaki bölümden Application bölümünü buluyoruz. Sonrasında Local Storage bölümüne basıyoruz. Alt bölümünde `https://app.getgrass.io` bölümüne basıyoruz. Orada UserId bölümünde yazan değeri bir yere not edelim.

> Şimdi sunucuya geçelim ve aşağıdaki komutu çalıştıralım. UserId yerine üstte gösterdiğim değeri yazıyoruz.

```bash
./PINGPONG config set --grass=UserId
```

> Sonrasında screen -r pingpong ile tekrar CTRL + C ile durduruyoruz.

```bash
chmod +x ./PINGPONG && ./PINGPONG --key DEVICEKEY
```

> bu komutu tekrar girip çalıştırıyoruz. Bu adımları düzgün şekilde yaptıktan sonra, Hem 0G hem de grass için sunucumuzu çalıştırmış oluyoruz.

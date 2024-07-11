---
description: Version 1.13.6 Upgrade
---

# Upgrade

Farcaster [http://IPADRESIN:3000/](http://65.109.61.219:3000/) gidiyoruz ve versionumuza bakıyoruz. Eğer version 1.13.5 yada daha düşükse aşağıdaki adımları yapıyoruz.

```
cd ~/hubble && ./hubble.sh upgrade
```

Bunu işlemi bitirince loglar akmaya başlar onu CTRL + C ile kapatıyoruz.&#x20;

```
screen -r farcaster
```

screen içine gidiyoruz ve aşağıdaki komutu çalıştırıyoruz.

```
cd ~/hubble && ./hubble.sh logs
```

Loglar akmaya başladıktan  sonra, CTRL A D ile çıkıyoruz ve versionu güncellemiş oluyoruz.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-07-11 123027.png" alt=""><figcaption></figcaption></figure>

# Light Node

#### Nubit Light Node Kurulumu <a href="#install-dependencies" id="install-dependencies"></a>

Herhangi bir node'nuzun yanında çalıştabilirsiniz. (Windows server hariç)

İlk olarak bir screen açıyoruz.

```
screen -S nubit
```

Screen içerisinde aşağıdak komutu çalıştabilirsiniz. Çalıştırmadan önce tüm rehberi okumayı unutmayın.

```
curl -sL1 https://nubit.sh | bash
```

> screen içine ulaşmak için screen -r nubit ile tekrardan screen'i açabilirsiniz. Screen içinden çıkmak için CTRL A+D kullanmayı unutmayın.

> komutu çalıştırdığınızda aşağıdaki gibi hem nubit adresini hem de gizli kelimlerinizi verecek. Bunları bir yere not etmeyi unutmayın.

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-24 222744.png" alt=""><figcaption></figcaption></figure>

> Eğer gizli kelimeleriniz alamadıysanız. Screen dışında aşağıdaki komutu çalıştırıp gizli kelimelerinizi alabilirsiniz.

```
nano $HOME/nubit-node/mnemonic.txt
```

> Public key yani nubit cüzdan adresiniz öğrenmek içinde tabii ki screen dışında aşağıdaki komutu çalıştırıp öğrenebilirsiniz.

```
 $HOME/nubit-node/bin/nkey list --p2p.network nubit-alphatestnet-1 --node.type light
```

> Bu adımları yaptıktan sonrna size verilen gizli kelimeler ile keplr wallet içinde açın

> Sonrasında onunla [BURADAN ](https://alpha.nubit.org/)platforma bağlanın.

> Sizi Galxe yönlendirecek. orada twiiter, discord doğrulamalarını tamamlayın.

Light node başlattıktan yaklaşık 15-20 dk sonrasında verify işlemi yapmayı unutmayın. Size verilen PUBKEY giriyoruz.

```
A+Tw8Rraes....
```

başlayan adresimizi orada doğrulayacağız. Şimdilik işlemler bu kadar.


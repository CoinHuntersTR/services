# 0G Storage Kurulumu

PingPong kurduğumuz node içerisine 0g Storage kurabiliyoruz. Bunun için yapılması gereken adımlara geçelim.

#### 0G Kurulum

> ilk olarak screen içine girelim ve CTRL C ile durduralım

```bash
screen -r pingpong
```

> Ardından güncellemelerimizi yapalım.

```bash
sudo rm PINGPONG

wget https://pingpong-build.s3.ap-southeast-1.amazonaws.com/linux/latest/PINGPONG

chmod +x ./PINGPONG && ./PINGPONG --key DEVICEKEY
```

> Çalışmaya başladıktan sonra CTRL A+D ile çıkıyoruz. DEVICEKEY yerine ilk çalıştırırken aldığımız KEY girmeyi unutmayın.

#### 0G Faucet ve Private Key

> Daha önce OG testnetlerine katılmadıysanız, [BURADAN ](https://faucet.0g.ai/)test için kullandığınız cüzdanınıza token istiyoruz. İşlemlere token geldikten sonra devam edebilirsiniz.  Test ağını da [BURADAN ](https://storagescan-newton.0g.ai/)cüzdanınızı bağlayarak ekleyip gelip gelmediğini kontrol edebilirsiniz.

```bash
./PINGPONG config set --0g=PRIVATEKEY
```

> PRIVATEKEY yazan yere test için kullandığınız cüzdanın private key ekliyoruz ve çalıştırıyoruz. Komutun sonunda successful  çıktısı sonrasında işlem tamamdır.

> Bu adım sonrasında `screen -r pingpong` diyerek screen içine giriyoruz ve CTRL + C ile durdurup tekrardan `chmod +x ./PINGPONG && ./PINGPONG --key DEVICEKEY` komutu çalıştırıyoruz. Screen içinden çıkmak için CTRL A D kullanıyoruz.&#x20;


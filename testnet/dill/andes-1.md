# Alps

#### Başvuru

[BURADAN](https://app.galxe.com/quest/n92t8xtegfVMAFUcXJor9E/GCVWntghfL) Galxe sitesine bağlanıyoruz ve testnet için kullanacağımız cüzdan ile Discord hesabımızla giriş yapıyoruz. Eğer seçim gerçekleşirse, size 2500 DILL token gönderecek ve Andes validator kanalına erişebileceksiniz. Bu adımı tamamladıktan sonra diğer işlemlere geçebilirsiniz.

#### Andes Network

Bu parametreleri Metamask cüzdanımıza ekliyoruz.&#x20;

| Network Name    | Dill Testnet Alps                                      |
| --------------- | ------------------------------------------------------ |
| RPC URL         | [https://rpc-alps.dill.xyz](https://rpc-alps.dill.xyz) |
| Chain ID        | 102125                                                 |
| Currency symbol | DILL                                                   |
| Explorer URL    | [https://alps.dill.xyz](https://alps.dill.xyz)         |

### Kurulum

**Light Validator**

<table><thead><tr><th>CPU</th><th width="174">RAM</th><th>SSD</th></tr></thead><tbody><tr><td>2 vCPU</td><td>2 GB</td><td>20 GB</td></tr></tbody></table>

**Full Validator**

<table><thead><tr><th>CPU</th><th width="174">RAM</th><th>SSD</th></tr></thead><tbody><tr><td>4 vCPU</td><td>8 GB</td><td>256 GB</td></tr></tbody></table>

#### 1. Gerekli Kütüphaneleri yüklüyoruz.

```bash
sudo apt update && sudo apt upgrade -y && sudo apt install curl tar -y
```

Sonrasında altaki komutu çalıştırıyoruz.

```bash
curl -sO https://raw.githubusercontent.com/DillLabs/launch-dill-node/main/dill.sh  && chmod +x dill.sh && ./dill.sh
```

Öncelikle Light Validator yada Full Validator seçeneği geliyor (light validator seçilenler 1, full validator seçilenler 2 yazıp enter basıyor.)

Çalıştırdığınızda, ilk adımda validator için yeni bir cüzdan oluşturuyoruz.

Bu cüzdan kelimelerini bir yere not edelim.

Sonrasında kelimeleri tekrar giriyoruz.

Bize bir şifre oluşturuyor. Onu da not edelim.&#x20;

Kaç adet coin stake edileceğini soruyor. 3600 yazıp enter basıyoruz.

{% hint style="danger" %}
Withdraw cüzdan adresi isteyecek. Buraya ödül alacağımızı adresi giriyoruz. Daha önce Dill Galxe için verdiğimiz adresi girebilirsiniz. İki kere withdraw adresi verdikten sonra aşağıdaki ekranın bitmesini bekleyin.
{% endhint %}

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-09-16 121513.png" alt=""><figcaption></figcaption></figure>

Bu adımları bitirdikten sonra;

Dill'in discorduna gidiyoruz ve Alps kanalında test tokeni talep ediyoruz.&#x20;

```
$request metamask cüzdan adresi
```

şeklinde komut göndererek, Galxe için kayıt ettiğiniz adresinizi giriyorsunuz. Size 3601 DILL coin gönderecek.&#x20;

Sonrasında yukarıda verdiğim test ağ bilgileri cüzdanına kayıt edip DILL Apls ağına geçiyoruz.&#x20;

[BURADAN ](https://staking.dill.xyz/en/)siteye bağlanıyoruz. ilk olarak bizden istediği json dosyasını buluyoruz. Dosyanın yeri aşağıdaki gibidir.

```
/root/dill/validator_keys/deposit_data-xxxxx.json
```

şeklinde olan dosyayı bilgisayarımıza indirelim.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-09-16 121522.png" alt=""><figcaption></figcaption></figure>

Siteye gittiğimizde biraz önce indirdiğimiz json dosyasını içeri aktarıyoruz.&#x20;

<figure><img src="https://cdn.udelivrs.com/2024/09/36d5f81eb2e396bb8bf3aba61da156d4_1726215895635.png" alt=""><figcaption></figcaption></figure>

Stake için minimum 3600 DILL tokene ihtiyacımız var.&#x20;

<figure><img src="https://cdn.udelivrs.com/2024/09/4aa730a078e6a580070525bb46e94c34_1726215895630.png" alt=""><figcaption></figcaption></figure>

Stake miktarını belirledikten sonra, Metamask cüzdanımıza gelen bildirimi onaylıyoruz ve aşağıdaki gibi çıktı aldığımızda işlem tamamlanmış oluyor.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-09-16 112245.png" alt=""><figcaption></figcaption></figure>

Bu adımlar bittikten sonra [BURADAN ](https://alps.dill.xyz/validators)Public keyimiz ile kontrol edebiliriz. Yaklaşık 1 saat sonra siteye sonucu yansıyor.&#x20;

Validator Public Key'inizi sunucunuzdaki Succes çıktısı içinde görebilirsiniz.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-09-16 122808.png" alt=""><figcaption></figcaption></figure>

Yada test ağında Stake ederken yaptığınız TX içinde bulabilirsiniz.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-09-16 122959.png" alt=""><figcaption></figcaption></figure>

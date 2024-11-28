# zNode

### Installion



| CPU   | RAM  | SSD     |
| ----- | ---- | ------- |
| 4 CPU | 8 GB | 160 SSD |

Sitem güncellemelerini yapalım.

```
sudo apt update && sudo apt upgrade -y
```

Node js kurulumu

```
# Node.js repository ekleme
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

# Node.js kurulumu
sudo apt install -y nodejs

# Versiyon kontrolü
node --version
npm --version
```

Gerekli olan paketleri yükleyelim.

```
sudo apt install -y build-essential
```

zNode Kurulumu

Öncelikle bir tane screen oluşturalım.

```
sudo apt install screen
```

```
screen -S znode
```

> Screen içine tekrar girmek için `screen -r znode` yazarak girebilirsiniz. Screen içinden çıkmak içinde CTRL A ve D tuşlarına basarak çıkabilirsiniz. CTRL C kullanmayın!

```
npm i -g rivalz-znode-cli
```

Version kontrolü için

```
znode -V
```

zNode çalıştırmak için;

```
znode run
```

çalıştırdıktan sonra bize NODE ID gerekecek, bunun için;

```
znode show-node-id
```

yazıyoruz ve aşağıdaki gibi bir çıktı alıyoruz.

<figure><img src="https://docs.rivalz.ai/~gitbook/image?url=https%3A%2F%2F3708766619-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FtEAVMQUfOtXSxHdeCogt%252Fuploads%252FZ3IJCaKtBUgWwAeRQAHk%252Fphoto_update.jpg%3Falt%3Dmedia%26token%3D1da19173-b940-4135-afa5-7dffdbd153a6&#x26;width=768&#x26;dpr=1&#x26;quality=100&#x26;sign=5be938a6&#x26;sv=1" alt=""><figcaption></figcaption></figure>

bu aldığımız ID adresini sitede aldığımız zNode NFT'lere tanımlayacağız. Onun için

BURADAN : [LINK](https://znode.rivalz.ai/licenses) siteye gidip,

Cüzdanımızı bağlıyoruz. Delegate tuşuna basıyoruz. Sunucudan aldığımız Node ID üst bölüme yazıyoruz. Alt kısıma da çalıştıracağımız node adedini yazıyoruz.

<figure><img src="https://docs.rivalz.ai/~gitbook/image?url=https%3A%2F%2F3708766619-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FtEAVMQUfOtXSxHdeCogt%252Fuploads%252FzljfcUeuvlzl2ctF5jQs%252FScreenshot%25202024-11-16%2520at%25206.54.46%2520PM.png%3Falt%3Dmedia%26token%3D9f0f99f6-968e-42de-8ec6-cce962ed6f3b&#x26;width=768&#x26;dpr=1&#x26;quality=100&#x26;sign=b4f93075&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Bu adımlar bittikten sonra aşağıdaki gibi görünecek;

<figure><img src="https://docs.rivalz.ai/~gitbook/image?url=https%3A%2F%2F3708766619-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FtEAVMQUfOtXSxHdeCogt%252Fuploads%252FQP6ZmC3oB8yTct2q46SO%252FScreenshot%25202024-11-16%2520at%25206.58.31%2520PM.png%3Falt%3Dmedia%26token%3D025a7735-a55b-4c1b-b1f5-23e9497a4a14&#x26;width=768&#x26;dpr=1&#x26;quality=100&#x26;sign=3abb94a&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Sonra sunucuya dönüyoruz ve tekrar çalıştırıyoruz.

```
znode run
```


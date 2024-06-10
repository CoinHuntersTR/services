# Auto TX Script

Airchains için kendi kurduğumuz, Rollup içinde TX atabilmek için, airchains rollup kurduğunuz sunucu da bu scripti çalıştırabilirsiniz.&#x20;



### Gerekli Kurumlar

```bash
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip screen -y
```

```bash
sudo apt install -y curl git jq lz4 build-essential cmake perl automake autoconf libtool wget libssl-dev -y
```

```bash
curl -sL https://deb.nodesource.com/setup_20.x | sudo -E bash -
```

#### Nodejs ve Npm Kurulumu&#x20;

```bash
sudo apt-get install -y nodejs
sudo apt install nodejs npm 
npm install -g npm@10.8.1
npm install web3@1.5.3
```

```bash
nano airchainstx.js
```

Açtığımız dosyaya aşağıdaki komutları, hiçbir yerine dokunmadan ekleyip. CTRL X Y enter ile çıkıyoruz.

```bash
const readline = require('readline');
const Web3 = require('web3');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout
});

rl.question('Enter your MetaMask wallet private key: ', (privateKey) => {
  rl.question('Enter the wallet address you want to send to: ', (toAddress) => {
    rl.question('Enter the amount you want to send (in tevmos): ', (amount) => {
      rl.question('How often do you want to repeat the transaction? (in seconds): ', (interval) => {

        const rpcURL = "http://localhost:8545"; // or your server's IP: "http://your-server-ip:8545"
        const web3 = new Web3(new Web3.providers.HttpProvider(rpcURL));

        function sendTransaction() {
          const account = web3.eth.accounts.privateKeyToAccount(privateKey);
          web3.eth.accounts.wallet.add(account);
          web3.eth.defaultAccount = account.address;

          const tx = {
            from: web3.eth.defaultAccount,
            to: toAddress,
            value: web3.utils.toWei(amount, 'ether'),
            gas: 21000,
            gasPrice: web3.utils.toWei('1', 'gwei')
          };

          web3.eth.sendTransaction(tx)
            .then(receipt => {
              console.log('Transaction successful with hash:', receipt.transactionHash);
            })
            .catch(err => {
              console.error('Error sending transaction:', err);
            });
        }

        setInterval(sendTransaction, interval * 1000);

        rl.close();
      });
    });
  });
});
```

Yeni bir screen açalım

```bash
screen -S autotx
```

Scriptimizi başlatıyoruz.

```bash
node airchainstx.js
```

Bizden bazı bilgileri isteyecek, sırasıyla yazıyorum.

> `MetaMask wallet private key` bunu bulmak için `/bin/bash ./scripts/local-keys.sh` ile aldığımız private key giriyoruz.&#x20;
>
> `Enter the wallet address you want to send to` göndermek istediğimiz bir cüzdan adresi yazıyoruz. İstediğinizi yazabilirsiniz.&#x20;
>
> `Enter the amount you want to send` göndermek istediğiniz miktarı yazıyorsunuz . 1,2,3 veya istediğiniz bir miktar.&#x20;
>
> `How often do you want to repeat the transaction` burada ne kadar sürede bir gönderim yapacak onu seçiyoruz. 30,40,50 saniye tam sayı olarak girebilirsiniz.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-06-10 141050.png" alt=""><figcaption></figcaption></figure>

Bu şekilde TX'ler görmeye başladıysanız, işlem tamamdır.&#x20;

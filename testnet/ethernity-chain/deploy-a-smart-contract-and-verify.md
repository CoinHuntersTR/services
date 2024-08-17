# Deploy a Smart Contract and Verify

Aşağıdaki tüm adımları yapmadan önce Cüzdanınızda Sepolia ETH ağında biraz test ETH olması gerekiyor. Oradaki ETH'ları [BURADAN ](https://testnetbridge.ethernitychain.io/)Ethernity test ağına geçirmeyi unutmayın. Yoksa işlemleriniz onaylanmaz.&#x20;

Herhangi bir VPS Ubuntu 22.04 sunucu içerisinde çalıştırabilirsiniz. veya [BURADAN ](https://github.com/codespaces)github codespace üzerinden tüm adımları yapabilirsiniz. Size kalmış.

### Gereksinimleri Yükleyelim..

```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Hello adında dosya açalım

```
mkdir hello && cd hello
```

```
npm init -y
```

> Aşağıdakine benzer bir çıktı alırsanız işlem tamamdır.&#x20;

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-08-18 012626.png" alt=""><figcaption></figcaption></figure>

### Hardhat için gereksinimleri yükleyelim.

```
npm install --save-dev hardhat ts-node typescript @nomicfoundation/hardhat-toolbox ethers@^6.1.0 dotenv

npm install --save-dev @nomicfoundation/hardhat-verify
```

```
npx hardhat
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-08-18 012842.png" alt=""><figcaption></figcaption></figure>

> Size seçenekler sunacak TypeScript project seçin diğerlerine enter yaparak ilerleyebilirsiniz.

```
cd contracts
rm Lock.sol
nano Hello.sol
```

Hello sol adında açtığımız akıllı kontrata aşağıdaki bilgileri değiştirmeden ekliyoruz.&#x20;

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Hello {
    function hi() public pure returns (string memory) {
        return "Hey there!";
    }
}
```

```
cd ..
nano hardhat.config.ts
```

> hardhat config dosyasını aşağıdaki gibi ekliyoruz. Ekledikten sonra CTRL X Y enter yapmayı unutmayın.

```
   import { HardhatUserConfig } from "hardhat/config";
   import "@nomicfoundation/hardhat-toolbox";
   import "@nomicfoundation/hardhat-verify";
   import * as dotenv from "dotenv";

   dotenv.config();

   const config: HardhatUserConfig = {
     etherscan: {
       apiKey: {
         ernscan: "ernscan", // apiKey is not required, just set a placeholder
       },
       customChains: [
         {
           network: "ernscan",
           chainId: 233,
           urls: {
             apiURL: "https://api.routescan.io/v2/network/testnet/evm/233/etherscan",
             browserURL: "https://testnet.ernscan.io"
           }
         }
       ]
     },
     networks: {
       ernscan: {
         url: 'https://testnet.ethernitychain.io',
         accounts: [process.env.PRIVATE_KEY!]
       },
     },
     solidity: "0.8.0",
   };

   export default config;
```

> Metamask cüzdanımızın private key'ini ekleyeceğiz.&#x20;

```
nano .env
```

```
PRIVATE_KEY=a43b28caca8..................
```

> şeklinde private keyimizi ekleyip CTRL X Y enter yapıyoruz.

```
npx hardhat compile
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-08-18 013314.png" alt=""><figcaption></figcaption></figure>

> bu şekilde çıktı alırsanız işlem tamamdır. Şimdi devam edelim.

```
mkdir scripts
nano scripts/deploy.ts
```

```
import { ethers } from "hardhat";

async function main() {
    const Hello = await ethers.getContractFactory("Hello");
    const hello = await Hello.deploy();
    await hello.waitForDeployment();
    console.log("Hello deployed to:", await hello.getAddress());
}

main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
  });

```

> Hiçbir şeye dokunmadan direkt olarak yapıştırıp. kayıt ediyoruz.

```
npx hardhat run scripts/deploy.ts --network ernscan
```

<figure><img src="../../.gitbook/assets/Ekran görüntüsü 2024-08-18 013441.png" alt=""><figcaption></figcaption></figure>

> Böyle bir çıktı aldığınızda tebrikler. Ethernity ağında akıllı kontrat yayınladıınız. Sıra geldi verify etmeye.

```
npx hardhat verify --network ernscan <deployed_contract_address>
```

> Bir önceki komutta aldığınız kontrat adresiniz <> dahil silip oraya yazdığınızda, ağda yayınlamış olduğunuz kontratı verify yapmış olursunuz.&#x20;

[https://testnet.ernscan.io/](https://testnet.ernscan.io/) adresinden kontrat adresinizi kontrol edebilirsiniz.&#x20;


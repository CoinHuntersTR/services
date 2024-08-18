# Create an NFT Collection and Verify

İlk iki adımda kullandığımız yerde işlemlere devam edebiliriz.

```
mkdir nft && cd nft
```

```
npm init -y
```

npm install --save-dev hardhat ts-node typescript @nomicfoundation/hardhat-toolbox ethers@^6.1.0 dotenv
npm install --save-dev @nomicfoundation/hardhat-verify
npm install --save-dev hardhat ts-node typescript @nomicfoundation/hardhat-toolbox ethers @openzeppelin/contracts dotenv


```
npx hardhat
```

İstendiğinde, "Create a TypeScript project" seçeneğini seçin ve .gitignore ve bağımlılıklar için "Yes" yanıtını verin.

### Yükleyeceğimiz resmi hazırlıyoruz.

Bu adımları kendi bilgisayarımızda yapıyoruz.

Bunun için [https://www.pinata.cloud/](https://www.pinata.cloud/) adresine ücretsiz kayıt olup oraya bir görsel yükleyebilirisiniz. yükledikten sonra size, CID ile bir numara verecek aşağıdaki komutta CID yazan yerlere o verdiği karakterleri ekleyebilirsiniz.

```
{
  "name": "Ethernity Genesis",
  "description": "Ethernity Genesis NFT for CoinHunters Community",
  "image": "ipfs://CID",
  "animation_url": "ipfs://CID,
  "external_url": "https://coinhunterstr.com/",
  "attributes": {
    "trait 1": "value 1",
    "trait 2": "value 2"
  }
}
```

bunu yaptıktan sonra, bu dosyayı kendi bilgisayarınızaa json uzantısı olarak kayıt edin. Daha sonra bu json uzantılı dosyayı da pinnata içerisine atıyoruz ve onun CID numarasını bir yere not ediyoruz.

```
cd contracts
rm Lock.sol
nano Nft.sol
```

> Aşağıdaki komutları değiştirmeden aynen ekliyoruz.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract Nft is ERC721URIStorage, Ownable {
    uint256 private _tokenIds;
    string private _baseTokenURI;

    constructor(string memory name, string memory symbol, string memory baseTokenURI) 
        ERC721(name, symbol) 
        Ownable(msg.sender) // Burada msg.sender'ı Ownable kurucusuna geçiriyoruz
    {
        _baseTokenURI = baseTokenURI;
    }

    function mint(address recipient) public onlyOwner returns (uint256) {
        _tokenIds++;
        uint256 newItemId = _tokenIds;
        _mint(recipient, newItemId);
        _setTokenURI(newItemId, _baseURI());
        return newItemId;
    }

    function _baseURI() internal view override returns (string memory) {
        return _baseTokenURI;
    }
}
```

```
cd ..
nano hardhat.config.ts
```

> Aşağıdaki komutları değiştirmeden devam ediyoruz.

```
import { HardhatUserConfig } from "hardhat/config";
import "@nomicfoundation/hardhat-toolbox";
import "@nomicfoundation/hardhat-verify";
import * as dotenv from "dotenv";

dotenv.config();

const config: HardhatUserConfig = {
  etherscan: {
    apiKey: {
      ernscan: "ernscan", // API anahtarı gerekmiyor, sadece bir placeholder
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
    "ethernity-testnet": {
      url: 'https://testnet.ethernitychain.io',
      chainId: 233,
      accounts: [process.env.PRIVATE_KEY!]
    }
  },
  solidity: {
    version: "0.8.20",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200
      }
    }
  }
};

export default config;
```

Şimdi de metamask cüzdanımızın private key'ini eklemek için

```
nano .env
```

dosyasını açıyoruz ve aşağıdaki gibi dosyanın içine private key'imizi ekliyoruz.

```
PRIVATE_KEY=your_private_key_here
```

```
npx hardhat compile
```

```
mkdir scripts
nano scripts/deploy.ts
```
> Aşağıdaki Metadata temel URI'deki yere, biraz önce yüklediğimiz json dosyası CID numarasını ekliyoruz. Geri kalan komutlara dokunmanıza gerek yok. 
```
import { ethers } from "hardhat";

async function main() {
    const baseTokenURI = "ipfs://<CID>"; // Metadata'nın temel URI'si
    const Nft = await ethers.getContractFactory("Nft");
    const nft = await Nft.deploy("Ethernity NFT", "ETNFT", baseTokenURI);
    await nft.waitForDeployment();
    console.log("NFT deployed to:", await nft.getAddress());
}

main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
  });
```

Akıllı sözleşmeyi Ethernity Testnet'e dağıtın:

```
npx hardhat run scripts/deploy.ts --network ethernity-testnet
```

sözleşme adresini kullanarak doğrulama yapın:

```
npx hardhat verify --network ethernity-testnet <deployed_contract_address> "Ethernity NFT" "ETNFT" "ipfs://CID"
```

en son kayıt ettiğimiz CID kodunu buraya yapıştırıp verify işlemini yapıyoruz.

[https://testnet.ernscan.io/](https://testnet.ernscan.io/) adresine gidiyoruz ve  Write Contract bölümüne gidip, Bu işlemler için kullandığımız Metamask cüzdanı bağlayıp, Mint fonksiyonu yerine kendi cüzdan adresimizi yazıp NFT mintliyoruz.&#x20;

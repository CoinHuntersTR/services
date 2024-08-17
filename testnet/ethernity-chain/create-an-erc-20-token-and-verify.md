# Create an ERC-20 token and Verify

Akıllı kontrat yayınladığınız yerden devam edebilirsiniz.&#x20;

```
mkdir token && cd token
```

npm projesini başlatıyoruz.

```
npm init -y
```

```
npm install --save-dev hardhat ts-node typescript @nomicfoundation/hardhat-toolbox ethers @openzeppelin/contracts dotenv
```

```
npx hardhat
```

> İstendiğinde, "Create a TypeScript project" seçeneğini seçin ve .gitignore ve bağımlılıklar için "Yes" yanıtını verin.

```
cd contracts
rm Lock.sol
nano Token.sol
```

> Aşağıdaki Token. Sol içindeki My Token ve TOK simgelerini kendinize göre değiştirebilirisiniz. Size kalmış.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract Token is ERC20 {
    constructor(uint256 initialSupply) ERC20("My Token", "TOK") {
        _mint(msg.sender, initialSupply);
    }
}
```

Editörden çıkmak ve kaydetmek için Ctrl + X, ardından Y ve Enter tuşlarına basın.

```
cd ..
nano hardhat.config.ts
```

> Aşağıdaki hiçbir bilgiyi değiştirmeden aynen ekliyoruz.

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

şimdi .env dosyası oluşturuyoruz.

```
nano .env
```

> Bu dosya içine metamask private key'imizi ekliyoruz.

```
PRIVATE_KEY=your_private_key_here
```

Token'imizi derleyelim.

```
npx hardhat compile
```

Token'imizi dağıtımı ve mint işlemini yapalım.

```
mkdir scripts
nano scripts/deploy.ts
```

```
import { ethers } from "hardhat";

async function main() {
    const initialSupply = ethers.utils.parseUnits("1000000", 18); // 1,000,000 TOK
    const Token = await ethers.getContractFactory("Token");
    const token = await Token.deploy(initialSupply);
    await token.waitForDeployment();
    console.log("Token deployed to:", await token.getAddress());
}

main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
  });
```

```
npx hardhat run scripts/deploy.ts --network ethernity-testnet
```

Şimdi de tokenimizi verify yapıyoruz. Bunun için bir üstteki komutta size verilen kontrat adresini yazmanız yeterlidir.&#x20;

```
npx hardhat verify --network ethernity-testnet <deployed_contract_address> 1000000000000000000000000
```

[https://testnet.ernscan.io/ ](https://testnet.ernscan.io/) adresten gidip hem tokeninizi hem de Verify olup olmadığını kontrol edebilirsiniz.&#x20;

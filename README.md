# A hardhat testnet environment base on GuitarDefi-frontend  

## Node Environment  
(recommend)    
`nvm use 12`    

## Install
`sh install.sh`  

## Serve  
`sh serve.sh` 

## Deploy 
`sh deploy.sh`  

## Start
`sh start.sh`  

## Running in the serve  
If you plan to push your local project to the server:  
Configure`src/config/index.ts`=>`BASE_URL`to address of the server
```
cd GuitarDefi-frontend
npm run build
```
## Example
![Image text](https://raw.githubusercontent.com/chobynleo/Img/main/GuitarDefi-swap-frontend-hardhat-testnet/WechatIMG30.png)
![Image text](https://raw.githubusercontent.com/chobynleo/Img/main/GuitarDefi-swap-frontend-hardhat-testnet/WechatIMG31.png)
![Image text](https://raw.githubusercontent.com/chobynleo/Img/main/GuitarDefi-swap-frontend-hardhat-testnet/WechatIMG32.png)

## Configuration
If you want to try out the process of configuring GuitarDefi-frontend for yourself and build your own testnet environment, 
the following will show you which files need to be modified:  

### Preparing source    
- Clone`GuitarDefi-swap-core`
```  
git clone git@github.com:GuitarDefiswap/GuitarDefi-swap-core.git  
yarn install  
yarn compile  
```

- Clone`GuitarDefi-swap-periphery`  
```
git clone git@github.com:GuitarDefiswap/GuitarDefi-swap-periphery.git  
yarn install  
yarn compile  
```

- Clone`GuitarDefi-frontend`  
```
git clone git@github.com:GuitarDefiswap/GuitarDefi-frontend.git  
yarn install  
```

tip: If you got a compilation error about: `import @uniswap/v2-core/contracts/interfaces/IGuitarDefiPair.sol`  
Please refer to [upchain](https://learnblockchain.cn/question/2055)  

## Setup  
- The source code for the contract address
`https://bscscan.com/address/your_address#code`  

### In the `GuitarDefi-swap-core`directory    
+ Install `hardhat`and edit `GuitarDefiFactory.sol`;  
`npm install --save-dev hardhat`;  
`npx hardhat` （choose `Create an empty hardhat.config.js`);  
+ Deploy tab => Select `GuitarDefiFactory` -> Fill your address as `feeToSetter` in constructor -> Deploy  
creat  
 `scripts/deploy.js` 
`npx hardhat run scripts/deploy.js --network dev`;  
Remember to save: `INIT_CODE_PAIR_HASH`;  
 
### In the `GuitarDefi-swap-periphery`directory  
+ Install `hardhat`and edit `GuitarDefiRouter.sol` file;  
`npm install --save-dev hardhat`;  
`npx hardhat` （choose `Create an empty hardhat.config.js`);  
+ In the `GuitarDefiLibrary.sol` to find `pairFor`function，read `INIT_CODE_PAIR_HASH` -> Copy this hash without prefix `0x`;  
+ creat  
`scripts/deploy.js`
`npx hardhat run scripts/deploy.js --network dev`
(If you got an error about:`error:max code size exceeded`，set `solidity->optimizer->runs` to 200);  

### In the `GuitarDefi-frontend`directory
Here are the files that need to be modified:
```
/.env.development 
/.env.production
/src/config/constants/networks.ts
/src/config/constants/index.ts
/src/config/constants/tokens.ts
{
Local testnet(e.Hardhat)：
/src/config/constants/lists.ts
/public/GuitarDefi-default.tokenlist.json
Other testnet:
/src/config/constants/lists.ts
/src/config/constants/tokenlists/GuitarDefi-default.tokenlist.json
}
/src/config/constants/contracts.ts
/scr/config/constants/farms.ts
/src/config/constants/pools.ts  
/src/config/constants/ifo.ts  
/src/config/constants/priceHelperLps.ts
/src/config/constants/types.ts
/src/config/index.ts
/src/config/constants/nftsCollections/index.ts
```
And then we go to the `GuitarDefi-swap-sdk` directory, and we need to modify the file  
```
cd ../GuitarDefi-swap-sdk
```  
Modify the file:
```
/src/constants.ts
```
and run:
```
$ GuitarDefi-swap-sdk/
npm run build
cp -r ./dist ./local-GuitarDefiswap-libs/sdk/
cp -r ./local-GuitarDefiswap-libs ../GuitarDefi-frontend/node_modules
```

The following is the specific content to be modified:   
+ `ethers.providers.StaticJsonRpcProvider(RPC_URL)` is in the `src/utils/providers.ts`，that parameter is in the `env.development` of `REACT_APP_NODE_PRODUCTION`;  
+ `network url` is in the `GuitarDefi-FRONTEND/src/config/constants/networks.ts`;  
+ `testnet tokens`configuration is in the `src/config/constants/tokens.ts`;  
+ `tokenlist` in the `src/config/constants/tokenlists/GuitarDefi-default.tokenlist.json`(In order to facilitate local access, I copied it to `public/`, you can do the same;  
+ `GuitarDefi_EXTENDED`、`GuitarDefi_TOP100` is in the `src/config/constants/lists.ts`，    
+ The configuration of the wallet connection network is in the `src/utils/wallet.ts`      
+ `ROUTER_ADDRESS` is in the `src/config/constants/index.ts`;
+ `masterChef`、`lotteryV2`、`multiCall` all these `contract address` are in the `src/config/constants/contracts.ts`
+ The configuration of the `ABI` and `address` are in the `config/abi/`, `utils/addressHelpers`, the reference in `src/utils/contractHelpers.ts`,`src/hooks/useContract.ts`
+ The configuration of the `priceHelperLps` are in the `src/config/constants/priceHelperLps.ts`;  
+ In the `src/config/constants/types.ts` about `address`need to be modified;
+ In the `src/state/farms/hooks.ts` about `useFarmFromPid(251)`need to be modified;    
+ `src/utils/getTokenList.ts`about getting tokenlist, I modified into`http`to access. If you want to build project to serve, you may modify `url` into `https`to get tokenlist. Detailed see annotation that 42 line on `src/utils/getTokenList.ts`;
+ In the `src/config/index.ts` about `BASE_BSC_SCAN_URLS,BASE_URL,BASE_BSC_SCAN_URL`need to be modified;
+ In the `src/config/constants/nftsCollections/index.ts` about `address`need to be modified;
+ `farm`、`pools`、`ifo`、`pricehelper`、`contracts` all these about `chainId` need to be modified， which are in the   
`src/config/constants/farm.ts`  
`src/config/constants/pools.ts`  
`src/config/constants/ifo.ts`     
`src/config/constants/pricehelper.ts`  
`src/config/constants/contract.ts`   
+ The configuration of `FACTORY_ADDRESS_JSON` and `INIT_CODE_HASH_JSON` are in the `node_modules/local-GuitarDefiswap-libs/sdk/dist/sdk.cjs.development.js`, 
see `GuitarDefiswap-frontend-hardhat-testnet/depoly.sh` => 
`cp -r ./local-GuitarDefiswap-libs ../GuitarDefi-frontend/node_modules`;  
PS: 
1. If you want to change it  
set These parameters about`FACTORY_ADDRESS_JSON`  `INIT_CODE_HASH_JSON`  `ChainId` in `GuitarDefiswap-frontend-hardhat-testnet/GuitarDefi-swap-sdk/src/constants.ts`
and then  
```
$ GuitarDefiswap-frontend-hardhat-testnet/GuitarDefi-swap-sdk/:
npm run build  
cp -r ./local-GuitarDefiswap-libs ../GuitarDefi-frontend/src/node_modules
```
2. If you want to build frontend project and deploy on your server  
`FACTORY_ADDRESS_JSON` and `INIT_CODE_HASH_JSON` should be hard coded
in `GuitarDefiswap-frontend-hardhat-testnet/GuitarDefi-swap-sdk/src/constants.ts`.
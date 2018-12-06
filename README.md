# Aragon React Boilerplate

> 🕵️ [Find more boilerplates using GitHub](https://github.com/search?q=topic:aragon-boilerplate) | 
> ✨ [Official boilerplates](https://github.com/search?q=topic:aragon-boilerplate+org:aragon)

React boilerplate for Aragon applications.

This boilerplate also includes a fully working example app, complete with a background worker and a front-end in React (with Aragon UI).

## Usage

```sh
aragon init foo.aragonpm.test react
```

## Running your app

### Using HTTP

Running your app using HTTP will allow for a faster development process of your app's front-end, as it can be hot-reloaded without the need to execute `aragon run` every time a change is made.

- First start your app's development server running `npm run start:app`, and keep that process running. By default it will rebuild the app and reload the server when changes to the source are made.

- After that, you can run `npm run start:aragon:http` which will compile your app's contracts, publish the app locally and create a DAO. You will need to stop it and run it again after making changes to your smart contracts.

Changes to the app's background script (`app/script.js`) cannot be hot-reloaded, after making changes to the script, you will need to either restart the development server (`npm run start:app`) or rebuild the script `npm run build:script`.

### Using IPFS

Running your app using IPFS will mimic the production environment that will be used for running your app. `npm run start:aragon:ipfs` will run your app using IPFS. Whenever a change is made to any file in your front-end, a new version of the app needs to be published, so the command needs to be restarted.

## What's in the box?

### npm Scripts

- **start** or **start:aragon:ipfs**: Runs your app inside a DAO served from IPFS
- **start:aragon:http**: Runs your app inside a DAO served with HTTP (hot reloading)
- **start:app**: Starts a development server for your app
- **compile**: Compile the smart contracts
- **build**: Builds the front-end and background script
- **build:app**: Builds the front-end
- **build:script**: Builds the background script
- **test**: Runs tests for the contracts
- **publish**: Builds the apps and the contracts and publishes them to IPFS and APM

### Libraries

- [**@aragon/os**](https://github.com/aragon/aragonos): Aragon interfaces
- [**@aragon/client**](https://github.com/aragon/aragon.js/tree/master/packages/aragon-client): Wrapper for Aragon application RPC
- [**@aragon/ui**](https://github.com/aragon/aragon-ui): Aragon UI components (in React)

#### Publish
This app has 3 environments defined in `arapp.json`:

| Environment | Network |
|---|---|
| default | localhost |
| staging | rinkeby |
| production | mainnet |

Prerequisites:
- ENS Registry address

Note: the `default` environment which points to `localhost` does not have an ENS Registry address specified, that's because the `@aragon/cli` will use the default value of `0xB9462EF3441346dBc6E49236Edbb0dF207db09B7`.

##### Staging environment
This environment defined in `arapp.json` points to:
- an ENS registry (`0x314159265dd8dbb310642f98f50c066173c1259b`)
- an APM registry (`open.aragonpm.eth`)
- an APM repository (`pomodoro-counter`)
- an Ethereum network (`rinkeby`)

the `rinkeby` network is further defined in `truffle.js`, and has:
- an Ethereum API address (`https://rinkeby.infura.io`)
- an Ethereum Account (`0xb4124cEB3451635DAcedd11767f004d8a28c6eE7`)
    (which is the first account generated from the `DEFAULT_MNEMONIC` variable)

##### Content & contract
Command:
```
aragon apm publish major CounterApp --environment staging
```

This will:
1. _build_ the app's frontend (which lives in `dist`)
2. _compile_ the app's contracts (which live in `build`)
3. publish the app to the **staging** environment.

Sample output:
```
 ✔ Check IPFS
 ✔ Applying version bump (major)
 ✔ Deploy contract
 ✔ Determine contract address for version
 ✔ Building frontend
 ✔ Prepare files for publishing
 ✔ Generate application artifact
 ✔ Publish pomodoro-counter.aragonpm.eth
 ✔ Fetch published repo

 ✔ Successfully published pomodoro-counter.aragonpm.eth v4.0.0: 
 ℹ Contract address: 0xC56a94cB177B297A9f4fe11781CE4E2eD1829f8B
 ℹ Content (ipfs): QmetwPT5CSuzAgTdqwwr5NTBm2R53YMNHp4p6PUbWxrVwg
 ℹ Transaction hash: 0x6ad17abdac51aca2f37679c43ce4fcbc68dc65515ea1eea587c9ca8f5bf293b8
```


##### Content/frontend only
Command:
```
aragon apm publish minor --only-content --environment staging
```

This will:
1. _build_ the app's frontend (which lives in `dist`)
2. publish the app to the **staging** environment.

Sample output:
```
 ✔ Check IPFS
 ✔ Applying version bump (minor)
 ✔ Determine contract address for version
 ✔ Building frontend
 ✔ Prepare files for publishing
 ✔ Generate application artifact
 ✔ Publish pomodoro-counter.open.aragonpm.eth
 ✔ Fetch published repo

 ✔ Successfully published pomodoro-counter.open.aragonpm.eth v2.1.0: 
 ℹ Content (ipfs): QmQCPnPrVFQzKRYHYffa28HGM3ge5ztGp2qCp4ELiF3pBa
 ℹ Transaction hash: 0x8092746febb63ed888fca33c8319f237c5bf4fe6fa18381e7407e5dddef97f68
```

##### Help
To deploy from a different account, you can:
- define a `~/.aragon/mnemonic.json` file
    ```
    {
        "mnemonic": "explain tackle mirror kit ..."
    }
    ```
    or
- define a `~/.aragon/rinkeby_key.json` file
    ```
    {
        "rpc": "http://127.0.0.1:8545",
        "keys": [
            "a8a54b2d8197bc0b19bb8a084031be71835580a01e70a45a13babd16c9bc1563"
        ]
    }
    ```

You might encounter the following errors:
1. `No "from" address specified in neither the given options, nor the default options.`
    That is because the network you are trying to deploy does not have an Ethereum account provider.

2. `Transaction would not succeed ("gas required exceeds allowance or always failing transaction")`
    when running `aragon apm publish minor CounterApp --environment staging`.
    Solution: deploy a `major` version. ¯\_(ツ)_/¯

3. `Transaction would not succeed ("Returned error: VM Exception while processing transaction: revert REPO_INVALID_VERSION")`
    when running `aragon apm publish minor CounterApp`.
    Solution: deploy a `major` version. ¯\_(ツ)_/¯

4. `→ ENOENT: no such file or directory, open '.../pomodoro-counter/build/contracts/CounterApp.json'`
    when running `aragon apm publish patch`.
    Is it expected to have the contracts compiled and the current version's contract to be redeployed.
    

#### Install the app in a DAO
To install this app on a rinkeby dao, e.g: `daniel.aragonid.eth`, which lives at `0xf9e4c6ee977b1fe7d296fbe3b75ba05f12a30e4e`

```
aragon dao install 0xF9e4c6ee977b1Fe7D296FbE3b75BA05f12A30E4e pomodoro-counter.open.aragonpm.eth --environment staging
```

You might encounter the following errors:
1. `Transaction has been reverted by the EVM:`
    This is because you do not have direct permissions.

2. `→ Cannot find artifacts in APM repo. Please make sure the package is published and IPFS or your HTTP server running.`
    This could be because there is no IPFS peer that has your app's content (frontend/artifact)
    You can check by going to `https://ipfs.io/ipfs/${hash}` and see if you can fetch it.
    You can fix it by doing `ipfs add` (??)

3. Stuck at `Inspecting DAO`
    This could be because `aragon.js` needs a websocket connection to ethereum to query data, and truffle providers being on `web3 0.x`, not supporting web sockets, the following workaround is needed:
    ```diff
    {
      "registry": "0x98df287b6c145399aaa709692c8d308357bc085d",
      "appName": "pomodoro-counter.open.aragonpm.eth",
    + "wsRPC": "wss://rinkeby.eth.aragon.network/ws",
      "network": "rinkeby"
    } 
    ```
    Note: this will work in the next version of the cli, when [pull/270](https://github.com/aragon/aragon-cli/pull/270) is merged!


Other issues:
1. `dao apps daniel.aragonid.eth --environment staging`
3. `dao apps 0xF9e4c6ee977b1Fe7D296FbE3b75BA05f12A30E4e --environment staging`
2. `dao acl daniel.aragonid.eth --environment staging`
   Gets stuck at _Inspecting DAO_

3. `dao apps 0xE9866fE6dF20F032E62Cc3094EdDe9748fE51753` <- generated dao from `aragon run`
```
Error: The EthJsENS Constructor requires a network or registry address.
    at new Ens (/home/daniel/repos/aragon/aragon-cli/node_modules/ethjs-ens/index.js:31:13)
```
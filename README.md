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

Command:

```
aragon apm publish minor --environment staging --files build
```

This will _build_ the app, _compile_ the contracts and publish the app to the **staging** environment.
This environment defined in `arapp.json` points to:
- an ENS registry (`0x314159265dd8dbb310642f98f50c066173c1259b`)
- an APM registry (`open.aragonpm.eth`)
- an APM repository (`pomodoro-counter`)
- an Ethereum network (`rinkeby`)

the `rinkeby` network is further defined in `truffle.js`, and has:
- an Ethereum API address (`https://rinkeby.infura.io`)
- an Ethereum Account (`0xb4124cEB3451635DAcedd11767f004d8a28c6eE7`)
    (which is the first account generated from the `DEFAULT_MNEMONIC` variable)

Sample output:
```
% aragon apm publish minor --environment staging --files build
 ✔ Check IPFS
 ✔ Applying version bump (minor)
 ✔ Deploy contract
 ✔ Determine contract address for version
 ✔ Building frontend
 ✔ Prepare files for publishing
 ✔ Generate application artifact
 ✔ Publish pomodoro-counter.open.aragonpm.eth
 ✔ Fetch published repo

 ✔ Successfully published pomodoro-counter.open.aragonpm.eth v0.1.0: 
 ℹ Contract address: 0x12137D5da5529E26235f421Be5D79807CB6C5cae
 ℹ Content (ipfs): QmWsFFWCk5rz9iXdE3AYVhmRe81k3XWBcxTMdG7Vukogxd
 ℹ Transaction hash: 0xb12fe902444c0d1cc5dafe254cfb5d2b27679743d0b08521b8fb951237278e07
 ❯ Published directory: /tmp/tmp-20363JM9HuSim3hWH
```

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


You might encounter the following error:
1. `No "from" address specified in neither the given options, nor the default options.`
    That is because the network you are trying to deploy does not have an Ethereum account provider

TODO:
- add the app to a dao
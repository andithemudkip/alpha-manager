# alpha-manager (NOT RELEASED YET!)

**alpha-manager** is a NodeJS module that makes it easy to run and manage multiple scripts (mainly useful for facebook bots at the moment)

  - Run multiple scripts from the same main script
  - Scripts get hot-reloaded when edited (no need to restart the main script!)
  - Automatically installs dependencies for all bots at runtime
  - Easily manage your bots using the _Dashboard [WIP]_

### Installation
```sh
$ npm install --save alpha-manager
```
### Quick Start
Basic folder structure

```
.
├── bots
|   └── bot_1
|       ├── index.js
|       └── package.json
|   └── bot_2
|       ├── index.js
|       └── package.json
├── node_ modules
├── index.js
└── package.json
```

- all ``index.js`` files within the `bots` folders will be loaded as modules in the main ``index.js``
- all dependencies found in the ``package.json`` files within the `bots` folders will be installed at runtime
- modifying an ``index.js`` file will lead to it being reloaded in the main script
- modifying a ``package.json`` file will lead to the new dependencies being installed (if any are new)

### bot ``index.js`` template
#### async
```js
//bots/bot_1/index.js

//doAsyncStuff() is a hypothetical async function that happens at some unknown time in the future

function PostFunctionAsync() {
    return new Promise((resolve, reject) => {
        doAsyncStuff().then(() => {
            resolve({
                type: 'text',
                message: 'hello! this will be posted every hour! [async]'
            });
        })
    });
}

module.exports = {
    page: PAGEID,
    token: APPTOKEN,
    post: PostFunctionAsync,
    recurrenceRule: '0 0 * * * *'
}
```
#### sync
```js
//bots/bot_1/index.js
function PostFunctionSync() {
    //do sync stuff, then
    return {
        type: 'text',
        message: 'hello! this will be posted every hour! [sync]'
    }
}

module.exports = {
    page: PAGEID,
    token: APPTOKEN,
    post: PostFunctionSync,
    recurrenceRule: '0 0 * * * *'
}
```

**alpha-manager** uses [bot-util](https://github.com/andithemudkip/bot-util) for posting and scheduling, for more info on the ``post object`` check out [bot-util](https://github.com/andithemudkip/bot-util)

### main ``index.js``

``.startBots(dir: string)`` - `dir` is a string that points to the directory of the bot folders relative to the main file

```js
const alpha = require('alpha-manager');

//optional
alpha.config({
    printLogs: false,
    startServer: true,
    serverPort: 3000
});

alpha.startBots('./bots').then(() => {
    console.log('bots are set up and running');
})
```

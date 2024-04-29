# Chapter 9: Wallet Project Practice 1

## linkeye wallet

Linkeye wallet is a project independently developed by the author, and it is connected to Linkeye's public chain project. This project is relatively small and the amount of code is not large. Below, the author will use Linkeye as an example to explain the development process of the project. Linkeye is a non-deterministic wallet that does not follow the Bip44 protocol.

## Linkeye wallet project architecture analysis

### 1. Brief description

Linkeye wallet is a wallet project that connects to the Linkeye public chain and cannot connect to other public chains. Linkeye wallet is a local desktop wallet developed by nodejs, electron and vue. The information of the entire account system is on the user's own device. nodeJs is a local service, and vue is the rendering process. The nodejs local service and the vue rendering process communicate through the main process and rendering process communication mechanism of electron. The user's account system data is stored in a local sqlite3 database. The entire project is divided into account creation keystore module, private key export module, private key import module, account system module, transfer record module, transfer module, and transfer confirmation module.


linkeye-wallet architecture diagram:
     ![linkeye-wallet architecture diagram:
](https://github.com/guoshijiang/go-ethereum-code-analysis/blob/master/wallet/linkeye-wallet/img/linkeye-wallet.png "linkeye-wallet architecture diagram:
")

In this project, the IPC communication mechanism is encapsulated. The encapsulation code of the rendering process is as follows:


         const { ipcRenderer } = require('electron')
         const {
           CLIENT_NORMAL_MSG,
           CRAWLER_NORMAL_MSG,
         } = require('./../../constants/constants')

         const ipcService = Object.create(null)
         const callbackCache = []

         ipcService.install = Vue => {
           Vue.prototype.$ipcRenderer = {
             send: (msgType, msgData) => {
               ipcRenderer.send(CLIENT_NORMAL_MSG, {
                 type: msgType,
                 data: msgData,
               })
             },
             on: (type, callback) => {
               callbackCache.push({
                 type,
                 callback,
               })
             },
             detach: type => {
               const idx = callbackCache.findIndex(v => v.type === type)
               idx > -1 ? callbackCache.splice(idx, 1) : console.error(`error type ${type}`)
             },
           }
           ipcRenderer.on(CRAWLER_NORMAL_MSG, (sender, msg) => {
             callbackCache.forEach(cache => {
               if (cache.type === msg.type) {
                 cache.callback && cache.callback(msg.data)
               }
             })
           })
         }

         export default ipcService


In the above code, the called event is placed in an event queue (actually an array). When the event call is completed, detach it and the event can be deleted from the array.

In the node service, each interface request linkeye wallet encapsulates an IPC communication class for the request. The codes are relatively similar. We will talk about one of the IPC classes here.

         import queryBlock from '../block/queryBlock'

         const {CLIENT_NORMAL_MSG, CRAWLER_NORMAL_MSG,} = require('../../constants/constants')

         export default class queryBlockIpc {
           constructor(listener, sender) {
             this.listener = listener
             this.sender = sender
             this.addListener(CLIENT_NORMAL_MSG, this.handleFn.bind(this))
             this.handlerQueryBlockIpc = queryBlock(this)
           }

           handleFn(event, data) {
             try {
               this.handlerQueryBlockIpc[data.type](event, data.data)
             } catch (error) {
               console.error('handler event error:' + error.message)
             }
           }

           addListener(chanel, cb) {
             this.listener.on(chanel, cb)
           }

           _sendMsg(chanel, msgBody) {
             this.sender.send(chanel, msgBody)
           }

           sendToClient(type, data) {
             this._sendMsg(CRAWLER_NORMAL_MSG, {
               type,
               data,
             })
           }
         }

The above code is the IPC class for scanning blocks. sendToClient sends the processed message to the rendering process, and addListener puts the processed event into the listener.

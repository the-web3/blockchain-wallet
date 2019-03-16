
# 第九章：钱包项目实战一

## linkeye钱包

Linkeye 钱包是笔者独立开发的一个项目，对接的是 Linkeye 的公链项目。这个项目比较小巧，而且代码量也不大。下面笔者就以 Linkeye 为例给讲解项目想的开发流程，Linkeye 是一个非确定性钱包，这个钱包没有遵循 Bip44 协议。

## linkeye钱包项目架构分析

### 一.简述

linkeye钱包是对接linkeye公链的钱包项目，不能对接其他的公链。linkeye钱包是nodejs, electron和vue开发的本地桌面钱包，整个账户体系的信息都在用户自己的设备上。nodeJs是一个本地的服务，vue是渲染进程，nodejs本地服务和vue渲染进程之间通过electron的主进程和渲染进程通信机制通信。用户的账户体系数据存储在本地sqlite3数据库中。整个项目分为创建账户生成keystore模块,导出私钥模块，导入私钥模块，账户体系模块，转账记录模块，转账模块，转账确认模块。


linkeye-wallet架构图： 
    ![linkeye-wallet架构图： 
](https://github.com/guoshijiang/go-ethereum-code-analysis/blob/master/wallet/linkeye-wallet/img/linkeye-wallet.png "linkeye-wallet架构图： 
")

本项目中把IPC通信的机制封装了，渲染进程的封装代码如下：


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


以上代码中将调用的事件放到一个事件队列中（其实是一个数组），当事件调用完成后，detach一下，就可以把事件从数组中删除了。

在node服务，每个接口请求linkeye钱包针对请求封装了一个IPC通信的类，代码都比较类似，我们这里就讲其中的一个IPC类

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

以上代码是扫块的IPC类，sendToClient是将处理好的消息发送给渲染进程，addListener将处理过的事件放到监听器














# 第十章：Ripple钱包开发

### 1. nodeJS 生成 ripple 地址


    const RippleAPI = require('ripple-lib').RippleAPI;
    var test_server = 'wss://s2.ripple.com';
    var keypairs = require('ripple-keypairs');
    const api = new RippleAPI({
        server: test_server 
    });

    api.connect().then(() => { 
        return api.generateAddress();
    }).then(address_info => {
        console.log("Secret: " + address_info.secret);
        console.log("Address: " + address_info.address);
        var keypair = keypairs.deriveKeypair(address_info.secret);
        var privateKey = keypair.privateKey;
        console.log("Private key: " + privateKey);
        var publicKey = keypair.publicKey;
        console.log("Public key: " + publicKey);
        var address = keypairs.deriveAddress(keypair.publicKey);
        console.log("Address: " + address);
    }).then(() => {
        return api.disconnect();
    }).then(() => {
        console.log('done and disconnected.');
    }).catch(console.error);


# vzone dapp与Vzone原生交互说明
1. **引入桥接对象示例**
```js
function setupWebViewJavascriptBridge(callback) {
  if (window.WebViewJavascriptBridge) {
    return callback(window.WebViewJavascriptBridge)
  }
  if (window.WVJBCallbacks) {
    return window.WVJBCallbacks.push(callback)
  }
  window.WVJBCallbacks = [callback]
  let WVJBIframe = document.createElement('iframe')
  WVJBIframe.style.display = 'none'
  WVJBIframe.src = 'https://__bridge_loaded__'
  document.documentElement.appendChild(WVJBIframe)
  setTimeout(() => {
    document.documentElement.removeChild(WVJBIframe)
  }, 0)
}

export default {
  callhandler(name, data, callback) {
    setupWebViewJavascriptBridge(function (bridge) {
      bridge.callHandler(name, data, callback)
    })
  }, registerhandler(name, callback) {
    setupWebViewJavascriptBridge(function (bridge) {
      bridge.registerHandler(name, function (data, responseCallback) {
        callback(data, responseCallback)
      })
    })
  }
}
```
2. **原生交互方法定义说明示例**
```js
var u = navigator.userAgent;
var _isIOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);

import Bridge from '../bridge';


/**
 * 获取钱包本地所有轻钱包地址，当前钱包为第一条。
 * 返回 Array<String> 没有返回空集合
 */
function getLocalAddress() {
  let defer = $.Deferred();
  let result;
  if (_isIOS) {
    var paramsJson = {};
    Bridge.callhandler('getLocalAddress', paramsJson, function (response) {
      result = response;
      defer.resolve(result);
    });
  } else {
    if (window.VDS_andorid) {
      result = window.VDS_andorid.getLocalAddress();
      defer.resolve(result);
    }
  }

  return defer.promise();
}


/**
 * Vds 合约：vds地址转hex地址。
 * @param vdsAddr string vds地址
 * 返回 String
 */
function getHexAddress(vdsAddr) {
  let defer = $.Deferred();
  let result;
  if (_isIOS) {
    var paramsJson = {"vdsAddr": vdsAddr};
    Bridge.callhandler('getHexAddress', paramsJson, function (response) {
      result = response;
      defer.resolve(result);
    });
  } else {
    if (window.VDS_andorid) {
      result = window.VDS_andorid.getHexAddress();
      defer.resolve(result);
    }
  }
  return defer.promise();
}


/**
 * Vds 合约：hex地址转vds地址
 * @param hexAddr string hex地址
 * @param testnet boolean 是否测试网路
 * 返回 String
 */
function fromHexAddress(hexAddr, testnet) {
  let defer = $.Deferred();
  let result;
  if (_isIOS) {
    var paramsJson = {
      "hexAddr": hexAddr,
      "testnet": testnet
    };
    Bridge.callhandler('fromHexAddress', paramsJson, function (response) {
      result = response;
      defer.resolve(result);
    });
  } else {
    if (window.VDS_andorid) {
      result = window.VDS_andorid.fromHexAddress();
      defer.resolve(result);
    }
  }
  return defer.promise();
}


/**
 * 获取钱包本地所有轻钱包地址，当前钱包为第一条。
 * 返回 BigDecimal 没有返回 0
 */
function getAddressBalance(address) {
  let defer = $.Deferred();
  let result;
  if (_isIOS) {
    var paramsJson = {
      "address": address
    };
    Bridge.callhandler('getAddressBalance', paramsJson, function (response) {
      result = response;
      defer.resolve(result);
    });
  } else {
    if (window.VDS_andorid) {
      result = window.VDS_andorid.getAddressBalance(address);
      defer.resolve(result);
    }
  }
  return defer.promise();
}

window['setAddressBalance'] = function (address, balance) {
  console.log("address:" + address + ",balance:" + balance);
  alert("address:" + address + ",balance:" + balance);
  return true;
}

/**
 * 分享二维码信息 邀请码 根据appname 存储到本地，在打开dapp链接时 带上该参数，例如 http://xxxxx/?invoteCode=xxxxxx
 * @param dappName String DAPP 名称
 * @param inviteCode String 邀请码
 * @param qrCodeTitle String 二维码标题
 * @param qrCodeDesc String  二维码描述
 * @param qrCodeContent String 二维码内容 二维码类型&二维码内容  例如定义dapp邀请二维码类型为 90001，内容为9001&123456
 */
function shareInviteQRCode(dappName, inviteCode, qrCodeTitle, qrCodeDesc, qrCodeContent) {
  if (_isIOS) {
    var paramsJson = {
      "dappName": dappName,
      "inviteCode": inviteCode,
      "qrCodeTitle": qrCodeTitle,
      "qrCodeDesc": qrCodeDesc,
      "qrCodeContent": qrCodeContent
    };
    Bridge.callhandler('shareInviteQRCode', paramsJson, function (response) {
    });
  } else {
    if (window.VDS_andorid) {
      window.VDS_andorid.shareInviteQRCode(dappName, inviteCode, qrCodeTitle, qrCodeDesc, qrCodeContent);
    }
  }
}

/**
 * 发送合约交易
 * @param feePerKb BigInteger  小费
 * @param value BigInteger  发送目的地址值，合约一般为0
 * @param contractAddress String 合约地址
 * @param op int 操作类型 如 ：普通合约 0xC2
 * @param gasLimit  BigInteger
 * @param gasPrice BigInteger
 * @param dataHex 合约16进制数据
 *
 * return 交易Hash
 */
function sendContractTx(feePerKb, value, contractAddress, op, gasLimit, gasPrice, dataHex, fromAddress) {
  if (_isIOS) {
    var paramsJson = {
      "feePerKb": feePerKb,
      "value": value,
      "contractAddress": contractAddress,
      "op": op,
      "gasLimit": gasLimit,
      "gasPrice": gasPrice,
      "dataHex": dataHex,
      "fromAddress": fromAddress
    };
    Bridge.callhandler('sendContractTx', paramsJson, function (response) {
    });
  } else {
    if (window.VDS_andorid) {
      window.VDS_andorid.sendContractTx(feePerKb, value, contractAddress, op, gasLimit, gasPrice, dataHex, fromAddress);
    }
  }
}

window['setSendContractTx'] = function (txHash) {
  console.log("txHash:" + txHash );
  alert("txHash:" + txHash);
  return true;
}


/**
 * 设置dapp bar 颜色
 * @param stateBarColor String
 */
function setStateBarColor(stateBarColor) {
  if (_isIOS) {
    var paramsJson = {
      "stateBarColor": stateBarColor
    };
    Bridge.callhandler('setStateBarColor', paramsJson, function (response) {
    });
  } else {
    if (window.VDS_andorid) {
      window.VDS_andorid.setStateBarColor(stateBarColor);
    }
  }
}

/**
 * 回退
 */
function nativeBack() {
  if (_isIOS) {
    var paramsJson = {};
    Bridge.callhandler('nativeBack', paramsJson, function (response) {
    });
  } else {
    if (window.VDS_andorid) {
      window.VDS_andorid.back();
    }
  }
}

/**
 * 显示原生提示
 * @param msg String
 */
function showToast(msg) {
  if (_isIOS) {
    try {
      Bridge.callhandler('showToast', msg, function (response) {
      });
    } catch (e) {
      alert(e);
    }
  } else {
    if (window.VDS_andorid) {
      window.VDS_andorid.showToast(msg);
    }
  }
}

let wallet = {
  getLocalAddress: function () {
    return getLocalAddress();
  },
  getHexAddress: function (vdsAddr) {
    return getHexAddress(vdsAddr);
  },
  fromHexAddress: function (hexAddr, testnet) {
    return fromHexAddress(hexAddr, testnet);
  },
  getAddressBalance: function (address) {
    return getAddressBalance(address);
  },
  nativeBack: function () {
    nativeBack();
  },
  appPlatform: function () {
    let apptype = _isIOS ? 2 : 1;
    return apptype;
  },
  shareInviteQRCode: function (dappName, inviteCode, qrCodeTitle, qrCodeDesc, qrCodeContent) {
    shareInviteQRCode(dappName, inviteCode, qrCodeTitle, qrCodeDesc, qrCodeContent);
  },
  sendContractTx: function (feePerKb, value, contractAddress, op, gasLimit, gasPrice, dataHex, fromAddress) {
    sendContractTx(feePerKb, value, contractAddress, op, gasLimit, gasPrice, dataHex, fromAddress);
  },
  showToast(msg) {
    showToast(msg);
  },
  setStateBarColor(stateBarColor) {
    setStateBarColor(stateBarColor);
  }


}
export default wallet

```
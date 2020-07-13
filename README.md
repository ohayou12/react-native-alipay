react-native-uiwjs-alipay
===

基于 React Native 的宝支付插件。适用于商家在 App 应用中集成支付宝支付功能，商家 APP 调用支付宝提供的 SDK，SDK 再调用支付宝 APP 内的支付模块。如果用户已安装支付宝APP，商家APP会跳转到支付宝中完成支付，支付完后跳回到商家 APP 内，最后展示支付结果。如果用户没有安装支付宝 APP，商家 APP 内会调起支付宝网页支付收银台，用户登录支付宝账户，支付完后展示支付结果。完整实例 [Example](./example)

![](https://gw.alipayobjects.com/zos/skylark-tools/public/files/c0aa8379f5f57c55f1e5bf25e6f426d1.png)

![](https://gw.alipayobjects.com/zos/skylark-tools/public/files/2454bffde14f428b2eeb2bfb6aa28d6b.png)

1. Android：支持2.3及以上的系统版本运行。
2. iOS：iOS 6.0以上(包含iOS 6.0)。
3. 支持手机系统：iOS（苹果）、Android（安卓）。
4. 调试请注意 支付宝接入应用必须 `已审核通过` 状态。
5. 支付宝开放平台-管理中心，签约 `APP支付` 和 `APP支付宝登录` 功能。
6. 适用于 `react-native >= 0.60+` 低版本未测试。

## 安装依赖

```bash
yarn add react-native-uiwjs-alipay
# react-native version >= 0.60+
$ cd ios && pod install
```

## API

### `Alipay.alipay` 支付

- ⚠️ 注意支付成功返回结果是一个字符串，[返回内容](https://github.com/uiwjs/react-native-uiwjs-alipay/blob/4c89c4e0e623e5073e4dc84a86978b1c5da18704/index.d.ts#L50-L74)  
- ⚠️ 支付宝需要设置 `Scheme` 和 iOS添加原生代码，才能支持支付和[回弹商家APP](#支付宝返回应用-ios-设置)的功能
- ⚠️ 支付宝 `管理中心-支付宝开放平台` 需要签约 [`APP支付`](https://opendocs.alipay.com/open/200/105310#%E6%B7%BB%E5%8A%A0%E5%BA%94%E7%94%A8%E5%8A%9F%E8%83%BD)

```javascript
import Alipay from 'react-native-uiwjs-alipay';

// 设置 支付宝 URL Schemes，要表述他是宇宙唯一性，可以使用 `bundle Identifier`
// scheme = `alipay` + `APPID`，`APPID` 为支付宝分配给开发者的应用ID
Alipay.setAlipayScheme(scheme);
// ⚠️ 目前不可用，设置支付宝沙箱环境，仅 Android 支持
// Alipay.setAlipaySandbox(isSandbox);
// 支付宝端支付
// payInfo 是后台拼接好的支付参数
Alipay.alipay(payInfo, (res)=> console.log(res))
```

订单详情 [`payInfo`](https://opendocs.alipay.com/open/204/105295#%E5%BF%AB%E6%8D%B7%E8%AE%A2%E5%8D%95%E6%94%AF%E4%BB%98%20iOS) 编码前的数据

```bash
alipay_sdk=alipay-sdk-java-dynamicVersionNo&app_id=xxxxxxxxxxxxx&biz_content={ "out_trade_no":"123123123123123", "total_amount":"0.01", "subject":"1234", "product_code":"QUICK_MSECURITY_PAY" }&charset=UTF-8&format=json&method=alipay.trade.app.pay&notify_url=http://ane.boshu.ltd/owner/pay/api/ownerPay/callback&return_url=uiwjspay://&sign=re/+2SICQggOUjfxl7MtP/qzir2e+LdH4m+02gDcw0fkByO5MqXW/9bmXw+c4RMqo835OAjMZs7s966ZuDx2PB+hO0tJ/bzdHLLqYlBeCcETkrfwRx+AFZNgzsCn75eRCA7GONH35BpfSeGkQUZ+vNXftqd6hWaa7m/MhQYrjQcV98IVJM+UR67Gj68c+LM586cnk0+rbj8zoos6tCvN8c3xx5UaCobzw4Ogf0PWZ7PZROTU9w2gtoxFfOC5d5slN3laaAXVjAxSf9JCNs8q95fDbzpbmstQOuPgGHkASkd/beH0F8eqTVv8gW1ZTo5v/d/E2wSDGV1DciaEnCroTw==&sign_type=RSA2&timestamp=2020-07-09 09:50:41&version=1.0
```

订单详情 `payInfo` 编码的数据

```bash
alipay_sdk=alipay-sdk-java-dynamicVersionNo&app_id=xxxxxxxxxxxxx&biz_content=%7B+%22out_trade_no%22%3A%22123123123123123%22%2C+%22total_amount%22%3A%220.01%22%2C+%22subject%22%3A%221234%22%2C+%22product_code%22%3A%22QUICK_MSECURITY_PAY%22+%7D&charset=UTF-8&format=json&method=alipay.trade.app.pay&notify_url=http%3A%2F%2Fane.boshu.ltd%2Fowner%2Fpay%2Fapi%2FownerPay%2Fcallback&return_url=uiwjspay%3A%2F%2F&sign=re%2F%2B2SICQggOUjfxl7MtP%2Fqzir2e%2BLdH4m%2B02gDcw0fkByO5MqXW%2F9bmXw%2Bc4RMqo835OAjMZs7s966ZuDx2PB%2BhO0tJ%2FbzdHLLqYlBeCcETkrfwRx%2BAFZNgzsCn75eRCA7GONH35BpfSeGkQUZ%2BvNXftqd6hWaa7m%2FMhQYrjQcV98IVJM%2BUR67Gj68c%2BLM586cnk0%2Brbj8zoos6tCvN8c3xx5UaCobzw4Ogf0PWZ7PZROTU9w2gtoxFfOC5d5slN3laaAXVjAxSf9JCNs8q95fDbzpbmstQOuPgGHkASkd%2FbeH0F8eqTVv8gW1ZTo5v%2Fd%2FE2wSDGV1DciaEnCroTw%3D%3D&sign_type=RSA2&timestamp=2020-07-09+09%3A50%3A41&version=1.0
```

- ⚠️ 后台 SDK 根据所有数据生成 `sign`，建议通过 API 拿到这个数据，拼接数据会报错。  
- ⚠️ `out_trade_no` 订单 id 和 `sign` 签名 是唯一的，每次不一样，需要后台生成。  

### `Alipay.authInfo` 登录授权

- ⚠️ 注意授权成功返回结果是一个字符串，[返回内容](https://github.com/uiwjs/react-native-uiwjs-alipay/blob/4c89c4e0e623e5073e4dc84a86978b1c5da18704/index.d.ts#L89-L113)  
- ⚠️ 支付宝需要设置 `Scheme` 和 iOS添加原生代码，才能支持验证[回弹商家APP](#支付宝返回应用-ios-设置)的功能
- ⚠️ 支付宝 `管理中心-支付宝开放平台` 需要签约 [`APP支付宝登录`](https://opendocs.alipay.com/open/200/105310#%E6%B7%BB%E5%8A%A0%E5%BA%94%E7%94%A8%E5%8A%9F%E8%83%BD)

```javascript
import Alipay from 'react-native-uiwjs-alipay';

// 设置 支付宝 URL Schemes，要表述他是宇宙唯一性，可以使用 `bundle Identifier`
// scheme = `alipay` + `APPID`，`APPID` 为支付宝分配给开发者的应用ID
Alipay.setAlipayScheme(scheme);
// 支付宝端支付
// authInfoStr 是后台拼接好的验证参数
// 如：apiname=com.alipay.account.auth&app_id=xxxxx&app_name=mc&auth_type=AUTHACCOUNT&biz_type=openservice&method=alipay.open.auth.sdk.code.get&pid=xxxxx&product_id=APP_FAST_LOGIN&scope=kuaijie&sign_type=RSA2&target_id=20141225xxxx&sign=fMcp4GtiM6rxSIeFnJCVePJKV43eXrUP86CQgiLhDHH2u%2FdN75eEvmywc2ulkm7qKRetkU9fbVZtJIqFdMJcJ9Yp%2BJI%2FF%2FpESafFR6rB2fRjiQQLGXvxmDGVMjPSxHxVtIqpZy5FDoKUSjQ2%2FILDKpu3%2F%2BtAtm2jRw1rUoMhgt0%3D
Alipay.authInfo(authInfoStr, (res)=> console.log(res))
```

授权返回结果，支付宝[返回结果参数说明](https://opendocs.alipay.com/open/218/105327#%E8%BF%94%E5%9B%9E%E7%BB%93%E6%9E%9C%E8%AF%B4%E6%98%8E)：

```json
{
  "resultStatus": 9000,
  "memo": "处理成功",
  "result": "success=true&result_code=200&app_id=202100117265&auth_code=8b6e5581b85WX84&scope=kuaijie&alipay_open_id=20881029919664670&user_id=20880025&target_id=15946456110003465"
}
```

## 支付宝返回应用 iOS 设置

- ⚠️ Android 端不需要做任何设置。
- ⚠️ 如果用户从 `支付宝App` 跳转到 `商家APP`，是通过系统功能切换，而不是通过 `支付宝APP` 功能键返回 `商家APP`，回调函数是不起作用的，可通过 [`AppState.addEventListener`](https://github.com/uiwjs/react-native-uiwjs-alipay/blob/5daea87bf0af05d60d0ae9e4c04e1e2d1a6e4273/example/App.js#L8-L24) 监听事件请求后台 API，来优化这一用户体验。

1. 在代码中设置支付宝 [`URL Schemes`](https://github.com/uiwjs/react-native-uiwjs-alipay/blob/5daea87bf0af05d60d0ae9e4c04e1e2d1a6e4273/example/App.js#L7)，下面实例 [`uiwjspay`](https://github.com/uiwjs/react-native-uiwjs-alipay/commit/f6d21b6b7ec7236b195c56281f971092f3c9bb08) 是定义的 `scheme`，你也可以定义为 `alipay` + `appid`，`appid` 为支付宝分配给开发者的应用ID，用来表述 `scheme` 唯一性。

```js
Alipay.setAlipayScheme('uiwjspay');
```

2. 在请求支付的 [`payInfo`](https://github.com/uiwjs/react-native-uiwjs-alipay/blob/1eff1dd94f3ae733db2913400e1aac382d056871/example/App.js#L27-L30) 中必须包含 [`return_url=uiwjspay://`](https://github.com/uiwjs/react-native-uiwjs-alipay/blob/5daea87bf0af05d60d0ae9e4c04e1e2d1a6e4273/example/App.js#L27-L28)，`return_url` 的值为定义的 `scheme` => `uiwjspay://`，才会返回[支付宝订单支付状态结果](https://opendocs.alipay.com/open/204/105301#%E8%BF%94%E5%9B%9E%E7%BB%93%E6%9E%9C%E7%A4%BA%E4%BE%8B%EF%BC%88iOS%7CAndroid%EF%BC%89)

```js
// payInfo 是后台拼接好的支付参数，这个参数必须包含 `return_url=uiwjspay://`
Alipay.alipay(payInfo, (res)=>console.log(res))
```

3. 用的 `URL Schemes` 列为白名单，在 [`ios/<应用名称>/Info.plist`](https://github.com/uiwjs/react-native-uiwjs-alipay/blob/5daea87bf0af05d60d0ae9e4c04e1e2d1a6e4273/example/ios/example/Info.plist#L23-L41) 中添加

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>alipay</string>
</array>
<key>CFBundleURLTypes</key>
<array>
  <dict>
    <key>CFBundleTypeRole</key>
    <string>Editor</string>
    <key>CFBundleURLName</key>
    <string></string>
    <key>CFBundleURLSchemes</key>
    <array>
      <string>uiwjspay</string>
    </array>
  </dict>
</array>
```

4. 修改 [`ios/<应用名称>/AppDelegate.m`](https://github.com/uiwjs/react-native-uiwjs-alipay/blob/4329bd62443bf377221860cd1acfaa710bbe562d/example/ios/example/AppDelegate.m#L60-L70) 添加下列代码：

```objective-c
#import <React/RCTLinkingManager.h>

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
  return [RCTLinkingManager application:application openURL:url
                      sourceApplication:sourceApplication annotation:annotation];
}

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey, id> *)options
{
  return [RCTLinkingManager application:application openURL:url options:options];
}
```

**命令测试**

- iOS: `xcrun simctl openurl booted uiwjspay://`
- Android：`adb shell am start -W -a android.intent.action.VIEW -d "uiwjspay://test/router" com.uiwjspay`

## 错误处理

```bash
[NetworkInfo] Signal strength query returned error: Error Domain=NSPOSIXErrorDomain Code=13 "Permission denied", descriptor: <CTServiceDescriptor 0x283317100, domain=1, instance=1>
```

在 `Product` -> `Scheme` -> `Edit Scheme` -> `Run` -> `Arguments` -> `Environment Variables` 添加 `OS_ACTIVITY_MODE` `disable`

## 其它

当前工程基于 [@brodybits/create-react-native-module](https://github.com/brodybits/create-react-native-module) 初始化。

```bash
npx create-react-native-module --package-identifier com.uiwjs --generate-example Alipay --example-react-native-version 0.62.2 --module-name react-native-uiwjs-alipay --github-account uiwjs --author-name "Kenny Wong" --author-email "wowohoo@qq.com"
```

## 相关连接 

- [支付宝：生成秘钥指南](https://opendocs.alipay.com/open/291/105971)
- [支付宝：SDK 下载地址，当前使用的是 AlipaySDK	15.7.7](https://opendocs.alipay.com/open/54/104509)
- [支付宝：客户端调试工具及使用教程](https://openclub.alipay.com/club/history/read/7695)
- [支付宝：支付，接入前准备](https://opendocs.alipay.com/open/204/105297/)
- [支付宝：完整版授权 SDK 调用方法](https://opendocs.alipay.com/open/218/105325)
- [支付宝：异步通知错误码: IllRet](https://opensupport.alipay.com/support/problem.htm?ant_source=antsupport)
- [React-native 0.6版本集成支付宝-Alipay爬坑](https://segmentfault.com/a/1190000020758279)

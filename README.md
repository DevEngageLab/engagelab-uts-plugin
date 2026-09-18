# uts-engagelabmtpush

## 兼容性

只支持 Android 和 iOS。

最低支持 iOS12。

## 插件市场地址
 [插件市场地址](https://ext.dcloud.net.cn/plugin?id=22391)

## Demo
[Github](https://github.com/DevEngageLab/engagelab-uts-plugin)

## 接入

- 3.在项目中引用插件
```
import { InitPushParams, initPush, setDebugMode, addEventCallBack, EventCallBack, EventCallBackParams, setBadge, SetBadge, getRegistrationId, requestSubscribeChannel } from "@/uni_modules/engagelab-mtpush"
```

## Android 配置 应用的 nativeResources/android 目录下新增 manifestPlaceholders.json 文件。 内容参考如下，根据自身项目填入正确的值。

```js
{
    "ENGAGELAB_PRIVATES_APPKEY" : "xxx",
    "ENGAGELAB_PRIVATES_CHANNEL": "xxx",
    "ENGAGELAB_PRIVATES_PROCESS": ":remote",
	"XIAOMI_APPID" : "MI-xxx",
	"XIAOMI_APPKEY" : "MI-xxx",
	"MEIZU_APPID" : "MZ-xxx",
	"MEIZU_APPKEY" : "MZ-xxx",
	"OPPO_APPID" :  "OP-xxx",
	"OPPO_APPKEY" : "OP-xxx",
	"OPPO_APPSECRET" : "OP-xxx",
	"VIVO_APPID":  "xxx",
	"VIVO_APPKEY" : "xxx",
	"HONOR_APPID" : "xxx"
}
```

如果支持谷歌通道，在应用的 nativeResources/android 目录下，放入 google-services.json 文件。
如果支持华为通道，在应用的 nativeResources/android 目录下，放入 agconnect-services.json 文件。

#### Android 厂商配置说明
插件支持 OPPO VIVO 魅族 小米 华为 FCM 荣耀七大厂商推送接入，如需接入请对应配置上述厂商信息。

- [厂商通道参数申请指南](https://jiguang-docs.yuque.com/staff-mg3p4r/vc4ysl/ca9ssa1c4izt4b5u?singleDoc#)

## 参考资料
[官方文档](https://www.engagelab.com/push)


# API 说明

## 注册监听

### addEventCallBack(param: EventCallBackParams) : void （android/ios都支持）

集成了 sdk 回调的事件

#### 参数说明
- param:返回的事件数据
  - param.eventName: 为事件类型
    - android:
      - "onNotificationStatus":应用通知开关状态回调,Map格式。enable属性：true为打开，false为关闭
      - "onConnectStatus":长连接状态回调, Map格式。enable属性：true为打开，false为关闭
      - "onNotificationArrived":通知消息到达回调，内容为通知消息体
      - "onNotificationClicked":通知消息点击回调，内容为通知消息体
      - "onNotificationDeleted":通知消息删除回调，内容为通知消息体
      - "onCustomMessage":自定义消息回调，内容为通知消息体
      - "onPlatformToken":厂商token消息回调，内容为厂商token消息体
      - "onTagMessage":tag操作回调
      - "onAliasMessage":alias操作回调
      - "onNotificationUnShow":在前台，通知消息不显示回调（后台下发的通知是前台信息时）
      - "onInAppMessageShow": 应用内消息展示
      - "onInAppMessageClick": 应用内消息点击
      - "onCommandResult":通用命令结果回调，小米消息频道订阅结果通过该事件返回
    - ios:
	  - "onNotificationStatus":检测通知权限授权情况。Map格式。enable属性：true为打开，false为关闭。需要先调用getNotiAuth() 接口。
	  - "onConnectStatus":长连接状态回调, Map格式。enable属性：true为打开，false为关闭 
      - "onNotificationArrived":通知消息到达回调，内容为通知消息体（同原生平台willPresentNotification回调）
      - "onNotificationClicked":通知消息点击回调，内容为通知消息体
      - "onCustomMessage":自定义消息回调，内容为通知消息体
      - "onTagMessage":tag操作回调 //todo
      - "onAliasMessage":alias操作回调
      - "onInAppMessageShow": 应用内消息展示
      - "onInAppMessageClick": 应用内消息点击
      - "onNotiInMessageShow": 增强提醒展示
      - "onNotiInMessageClick": 增强提醒点击
  - param.eventData: 为对应内容，Map格式

小米消息频道订阅对应的 `onCommandResult.eventData` 包含：

- `cmd`: 固定为 `2012`
- `errorCode`: 小米整体结果码。`0` 仅表示订阅弹窗正常展示并返回用户操作结果，不代表每个频道都订阅成功
- `msg`: 结果描述；当前订阅命令通常为空字符串
- `extra`: 小米扩展字段，并包含 `subscribe_code`（与 `errorCode` 一致）和 `platform`（小米为 `1`）

`errorCode` 说明：

|值|说明|
|:--:|---|
|`0`|弹窗正常展示，用户操作结果已返回|
|`-100`|当前版本不支持|
|`-200`|鉴权失败|
|`-300`|应用不在前台或设备未亮屏|
|`-400`|正在处理其他订阅请求|
|`-500`|触发小米频控|
|`-600`|参数无效|
|`-700`|用户取消|
|`-800`|没有通知权限|
|`-900`|未知异常|
|`-10000`|小米推送尚未完成注册|
|`-2147483648`|调用小米 SDK 异常|

当 `errorCode` 为 `0` 时，`extra.open_channel_result`（如果小米系统服务返回）为各频道结果的 JSON 字符串。单频道 `code`：`100` 创建成功、`-100` 创建失败、`-200` 用户拒绝、`-300` 已创建且开启、`-400` 已创建但关闭、`-500` 频道无效。


#### 代码示例

```js

let eventCallBack = {
	callback: (res: EventCallBack) => {
		console.log(res);
	}
} as EventCallBackParams;
addEventCallBack(eventCallBack);

```

## 订阅小米消息频道（仅 Android）

### requestSubscribeChannel(channelIds: string[]) : void

请求订阅一个或多个小米消息频道。该能力目前仅支持小米通道，结果通过
`addEventCallBack` 注册的 `onCommandResult` 事件返回。
原生行为及限制参考 [EngageLab Android SDK requestSubscribeChannel](https://www.engagelab.com/zh_CN/docs/app-push/developer-guide/client-sdk-reference/android-sdk/sdk-api-guide#requestsubscribechannel)。

`channelIds` 必须是在小米推送后台申请的订阅类频道 ID。单次最多传入 3 个，超出部分由小米 SDK 丢弃。
订阅弹窗 30 秒内最多请求一次；同一频道一个月内最多请求两次，达到上限时返回 `-500`。

#### 代码示例

```ts
let eventCallBack = {
  callback: (res: EventCallBack) => {
    if (res.eventName == 'onCommandResult' && res.eventData.get('cmd') == 2012) {
      const errorCode = res.eventData.get('errorCode');
      const extra = res.eventData.get('extra');
      console.log('订阅结果', errorCode, extra);
    }
  }
} as EventCallBackParams;

addEventCallBack(eventCallBack);
requestSubscribeChannel(['channel_id_1', 'channel_id_2']);
```


## 初始化

### initPush(param: InitPushParams) （ios/android）

初始化sdk

#### 接口定义

```js
  function initPush(param: InitPushParams): void
```

#### 代码示例
```js
// appkey对iOS生效，安卓的appkey需要配置在应用的 nativeResources/android/manifestPlaceholders.json中
let options = {
	appkey: '您的appkey',
	channel: 'test',
	isProduction: false
} as InitPushParams;
initPush(options);
```


## 开启 Debug 模式

### setDebugMode(param: boolean) （android/ios都支持）

设置是否debug模式，debug模式会打印更对详细日志

#### 接口定义

```js
function setDebugMode(paramA : boolean) : void
```

#### 参数说明

- param: 是否调试模式，true为调试模式，false不是

#### 代码示例

```js
setDebugMode(true);//发布前要删除掉
```

## 设置设备更换时重置RegistrationID功能

### setEnableResetOnDeviceChange(enable: boolean) （android/ios都支持）

开启或者关闭设备更换时重置RegistrationID的功能。若开启时，当检测到设备发生变化时（只有当设备型号发生变化时），会自动清除注册信息，重新注册。

需要在 initPush 之前调用。

默认为关闭。

#### 接口定义

```js
function setEnableResetOnDeviceChange(enable: boolean): void
```

#### 参数说明

- enable: 是否开启设备更换时重置RegistrationID功能，true为开启，false为关闭

#### 代码示例

```js
setEnableResetOnDeviceChange(true);
```

## 获取 RegistrationID （android/ios都支持）

### function getRegistrationId(): string

RegistrationID 定义:
获取当前设备的registrationId，Engagelab唯一标识，可同于推送

#### 接口定义

```js
function getRegistrationId(): string
```

#### 返回值

调用此 API 来取得应用程序对应的 RegistrationID。 只有当应用程序成功注册到 MTPush 的服务器时才返回对应的值，否则返回空字符串。

#### 代码示例

```js
const rid = getRegistrationId();
```

## 设置应用角标数量 （android/ios都支持）

### function setBadge(param: number) : void 

android: 仅华为/荣耀生效

#### 接口定义

```js
function setBadge(param: number) : void 
```

#### 返回值

无

#### 代码示例

```js
 setBadge(2);
```





## 开启fcm测试模式

### testConfigGoogle(enable: boolean) （仅android支持）

设置为true, 可以在国内测试fcm通道，需要在初始化函数之前调用。

该接口只用作测试，正式环境请不要调用该接口！！！

#### 接口定义

```js
export function testConfigGoogle(enable: boolean) : void 
```

#### 参数说明

- enable: 是否开启fcm调试模式，true为是，false不是

#### 代码示例

```js
testConfigGoogle(true);
```

## 检测通知授权情况

### getNotiAuth() （仅iOS支持）

调用该函数之前，请先调用 addEventCallBack(param: EventCallBackParams) : void 接口注册事件监听。

回调 event_name 为 "onNotificationStatus" 事件。

#### 接口定义

```js
export function getNotiAuth() : void 
```

#### 参数说明

无

#### 代码示例

```js
getNotiAuth();
```

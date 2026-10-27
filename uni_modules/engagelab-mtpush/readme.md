# engagelab-MTPush
### 开发文档
[UTS 语法](https://uniapp.dcloud.net.cn/tutorial/syntax-uts.html)
[UTS API插件](https://uniapp.dcloud.net.cn/plugin/uts-plugin.html)
[UTS uni-app兼容模式组件](https://uniapp.dcloud.net.cn/plugin/uts-component.html)
[UTS 标准模式组件](https://doc.dcloud.net.cn/uni-app-x/plugin/uts-vue-component.html)
[Hello UTS](https://gitcode.net/dcloud/hello-uts)

### requestVivoSubscribeChannel

仅 Android：需要 Android SDK 5.5.3、vivo 通道包 5.5.3（内含 vivo Push SDK 4.1.7.0_517）及支持订阅弹窗的 Android 10+ vivo 设备。SDK 初始化、通知权限开启后，在前台页面的按钮点击中调用，传入 1–3 个非空的 vivo **通知渠道 ID**（不是模板 ID）。非 vivo 设备忽略调用，不触发订阅结果。

最终结果沿用通用命令回调，`cmd = 2013`；小米订阅仍为 `2012`。保留原生字段 `cmd`、`errorCode`、`msg`、`extra`；`extra` 包含 `vivo_subscribe_result`、`platform`、`subscribe_code`、`operate_result` 和 JSON 字符串 `channel_operations`。渠道结果的 `channelId`、`operation` 保持原样，`operation=1` 为允许、`2` 为拒绝。不能仅凭请求提交成功或 `errorCode=0` 判断所有渠道已获授权，需逐项读取 `channel_operations`。

处理中重复请求返回 `6050`，SDK 本地超时返回 `6002`。如果厂商请求已发出后发生超时或无法确认是否发出的异常，为避免误认迟到结果，当前进程会丢弃后续无法归属的厂商终态；重试仍可能超时，恢复最终结果处理需重启进程。

通过 `addEventCallBack` 的 `onCommandResult` 事件读取 `eventData`。

```ts
import { requestVivoSubscribeChannel } from '@/uni_modules/engagelab-mtpush';
// 在前台按钮点击中调用。
requestVivoSubscribeChannel(['your_vivo_channel_id']);
```

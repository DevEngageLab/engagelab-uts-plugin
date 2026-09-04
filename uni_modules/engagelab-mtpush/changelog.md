## 1.1.5（2026-09-04）
1. Android SDK 及厂商插件升级至 5.4.4，iOS SDK 保持 5.4.4。
## 1.1.4（2026-08-31）
1. iOS SDK 升级至 5.4.4，Android 主 SDK 保持 5.4.3。
## 1.1.3（2026-08-25）
1. Android 主 SDK 保持 5.4.3，vivo 厂商插件升级至 5.4.3.1，补充桌面角标权限。

## 1.1.2（2026-08-19）
1. Android SDK 升级至 5.4.3。
2. 支持 OPPO 与 vivo 厂商消息角标处理。

## 1.1.1（2026-07-17）
1. update ios sdk 5.4.1 & android sdk 5.4.2
2. 新增 reportCustomDisplay(messageId, platform, platformMessageId)：上报自定义消息展示数据（Android 需 platform/platformMessageId，iOS 仅需 messageId）
3. 新增 reportCustomClick(messageId, platform, platformMessageId)：上报自定义消息点击数据，参数同上
4. android 5.4.2: 新增 onVoipMessage 回调（VoIP 消息，支持小米/OPPO/vivo/荣耀四大厂商通道），已接入插件 wrapper
## 1.1.0（2026-05-28）
1. update ios sdk 5.4.0 & android sdk 5.4.0
2. android 5.4.0: AndroidId 默认不再采集，CollectControl 中 aid 字段已废弃
3. android 5.4.0: 小米厂商 SDK 从 6.0.1 升级至 7.9.2
## 1.0.9（2026-05-15）
adjust support version
## 1.0.8（2026-05-15）
1. Fix the iOS compilation failure issue.
## 1.0.7（2026-01-20）
1. update ios sdk 5.3.0 & android sdk 5.3.0
2. android update setCollectControl api, add aid parameter support
## 1.0.6（2025-10-24）
1. update ios sdk 5.2.0 & android sdk 5.2.0
2. android add setCollectControl api
3. android change minSdkVersion to 23
## 1.0.5（2025-08-08）
1. update ios sdk 5.1.0 & android sdk 5.1.0
2. add setEnableResetOnDeviceChange api
## 1.0.3（2025-03-07）
iOS：
1. add api: getNotiAuth 
2. add PrivacyInfo.xcprivacy file 
3. add onNotificationStatus and onConnectStatus event callback
4. setbadge function add " clean local notification badge count" ability
## 1.0.2（2025-03-05）
update ios version to 4.5.1, and add UTS.entitlements file
## 1.0.1（2025-03-03）
add fcm and huawei dependencies
## 1.0.0（2025-02-28）
1. firset version support push

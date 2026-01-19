# 更新SDK

根据输入的需要更新的SDK版本号更新插件。

## 更新步骤

### 1. 更新SDK版本依赖

- **Android**: 在 `uni_modules/engagelab-mtpush/utssdk/app-android/config.json` 中更新 Android MTPush SDK 的版本依赖
  - 更新 `dependencies` 数组中所有 `com.engagelab` 相关依赖的版本号（如 `"com.engagelab:engagelab:5.2.0"`）
- **iOS**: iOS SDK 通过静态库（.a文件）引入，位于 `uni_modules/engagelab-mtpush/utssdk/app-ios/Libs/MTPush/` 目录
  - **使用脚本下载 SDK**：
    1. **运行下载脚本**：
       ```bash
       ./.cursor/scripts/download_ios_sdk.sh <版本标签>
       ```
       - 示例：`./.cursor/scripts/download_ios_sdk.sh v5.3.0` 或 `./.cursor/scripts/download_ios_sdk.sh 5.3.0`
       - 脚本会自动完成以下操作：
         1. 从 GitHub 仓库下载对应版本的 xcframework 到 `.temp/ios-sdk/` 目录（项目根目录下的临时目录）
         2. 自动从 xcframework 中提取 `ios-arm64` 文件夹下的文件到插件目录：
            - `libMTPush.a` → `uni_modules/engagelab-mtpush/utssdk/app-ios/Libs/MTPush/libMTPush.a`
            - `Headers/MTPushService.h` → `uni_modules/engagelab-mtpush/utssdk/app-ios/Libs/MTPush/MTPushService.h`
         3. 验证文件是否成功提取
       - **注意**：脚本执行完成后会显示提取的文件信息，无需手动操作

### 2. 查找SDK新增API

**⚠️ 重要：必须仔细逐项检查更新日志，不要因为看到"更新各厂商SDK"等主要更新内容就忽略新增API的检查！**

#### Android SDK
- 访问 [Android SDK Changelog](https://www.engagelab.com/zh_CN/docs/app-push/developer-guide/client-sdk-reference/changelog/android-sdk) 查找新版本的新增对外API
- **检查方法**：
  1. 找到目标版本（如 v5.2.0）的更新内容部分
  2. **逐项阅读**更新内容列表中的每一项，不要跳过任何条目
  3. 特别关注包含以下关键词的条目：
     - "新增"、"新增接口"、"新增API"、"新增方法"
     - "public static"、"public void" 等Java方法签名
     - "支持"、"功能"（可能包含新API）
  4. 对于每个疑似新增API的条目，记录：
     - API方法名（如 `setCollectControl`）
     - 完整方法签名（如 `public static void setCollectControl(MTPushCollectControl control)`）
     - 功能描述
- 在 [Android SDK API 文档](https://www.engagelab.com/zh_CN/docs/app-push/developer-guide/client-sdk-reference/android-sdk/sdk-api-guide) 中查找并确认新增API的详细用法、参数说明和示例代码

#### iOS SDK
- 访问 [iOS SDK Changelog](https://www.engagelab.com/zh_CN/docs/app-push/developer-guide/client-sdk-reference/changelog/ios-sdk) 查找新版本的新增对外API
- **检查方法**：
  1. 找到目标版本（如 v5.2.0）的更新内容部分
  2. **逐项阅读**更新内容列表中的每一项，不要跳过任何条目
  3. 特别关注包含以下关键词的条目：
     - "新增"、"新增接口"、"新增API"、"新增方法"
     - Objective-C方法签名（如 `- (void)setBadge:completion:`）
     - "支持"、"功能"（可能包含新API）
  4. 对于每个疑似新增API的条目，记录：
     - API方法名
     - 完整方法签名
     - 功能描述
- 在 [iOS SDK API 文档](https://www.engagelab.com/zh_CN/docs/app-push/developer-guide/client-sdk-reference/ios-sdk/sdk-api-guide) 中查找并确认新增API的详细用法、参数说明和示例代码

**检查清单**（在完成检查后确认）：
- [ ] 已找到目标版本的更新日志
- [ ] 已逐项阅读所有更新内容条目（包括次要更新）
- [ ] 已识别所有包含"新增"、"API"、"接口"、"方法"等关键词的条目
- [ ] 已记录所有新增API的方法名和签名
- [ ] 已在API文档中查找并确认了每个新增API的详细用法
- [ ] 已区分哪些是新增的对外API（需要封装），哪些是内部更新（不需要封装）

**常见误区**：
- ❌ 错误：看到"更新各厂商SDK"就认为只是版本更新，没有新增API
- ✅ 正确：即使主要更新是版本升级，也要仔细检查是否有新增API
- ❌ 错误：只关注主要更新内容，忽略列表中的其他条目
- ✅ 正确：必须逐项检查更新内容列表中的每一项
- ❌ 错误：依赖搜索结果判断是否有新增API
- ✅ 正确：直接查看官方更新日志，逐项检查
- ❌ 错误：文本识别有问题时（如缺少字母），直接忽略
- ✅ 正确：如果文本识别有问题，需要手动访问官方文档确认

### 3. 封装新增API（如有）

**⚠️ 重要：如果没有新增API，必须明确说明"经检查，该版本无新增对外API"，而不是简单说"没有新增API"。**

如果SDK有新增API，需要在插件中进行封装：
- 在 `uni_modules/engagelab-mtpush/utssdk/interface.uts` 中定义接口类型（如 `export type NewApi = (param: ParamType) => void`）
- 在 `uni_modules/engagelab-mtpush/utssdk/app-android/index.uts` 中实现Android端逻辑
- 在 `uni_modules/engagelab-mtpush/utssdk/app-ios/index.uts` 中实现iOS端逻辑

**封装原则**：
- 如果Android和iOS新增的API是同一个功能，封装成一个插件方法
- 如果不是同一个功能，分开封装
- **不要使用反射的方式调用SDK API，直接调用即可**
- 如果没有新增API，**必须明确说明已检查并确认无新增API**，然后跳过此步骤

**封装步骤**：
1. 在 `interface.uts` 中定义接口类型（参考现有接口定义格式）
2. 确定API的完整签名和参数类型
3. 确定API的调用时机（是否需要在init之前调用）
4. 在对应平台的 `index.uts` 中实现方法，使用 `export` 导出
5. 从 `interface.uts` 导入类型定义，保持与现有API风格一致
6. 添加必要的错误处理和日志

### 4. 更新示例代码

在 `pages/index/index.uvue` 中添加新增API的示例调用代码（如有新增API）。
- 在 `<script>` 部分导入新增的API
- 在 `<template>` 部分添加测试按钮
- 在 `methods` 中添加对应的测试方法

### 5. 更新API文档

如果新增了插件方法，需要更新文档：
- 在 `uni_modules/engagelab-mtpush/readme.md` 中补充新增的插件方法说明和使用示例

如果没有新增方法，跳过此步骤。

### 6. 更新插件版本号

在 `uni_modules/engagelab-mtpush/package.json` 中更新插件版本号：

**版本号格式**：遵循语义化版本号（Semantic Versioning），格式为 `主版本号.次版本号.修订号`

**版本号更新规则**：
- 修订号 +1
- 如果修订号到 10 了，就改成 0，次版本号 +1
- 如果次版本号到 10 了，就改成 0，主版本号 +1

**示例**：
- 假设当前版本为 `1.0.6`，更新后为 `1.0.7`
- 假设当前版本为 `1.0.9`，更新后为 `1.1.0`
- 假设当前版本为 `1.9.9`，更新后为 `2.0.0`

### 7. 更新README.md（如需要）

如果项目根目录的 `README.md` 中包含插件版本号信息，需要更新为最新的插件版本号。

UTS插件通过 `uni_modules` 方式引入，通常不需要在 `README.md` 中指定版本号，但如果有版本说明，需要同步更新。

### 8. 更新changelog.md

在 `uni_modules/engagelab-mtpush/changelog.md` 中记录本次更新的变更内容，包括：
- SDK版本更新（Android和iOS版本号）
- **新增的API方法（如有，必须列出具体方法名）**
- 其他相关变更（如配置变更、依赖更新等）

**格式示例**：
```
## 1.0.7（更新日期）
1. update ios sdk 5.3.0 & android sdk 5.3.0
2. 经检查，该版本无新增对外API
```

**如果有新增API**：
```
## 1.1.0（更新日期）
1. update ios sdk 5.3.0 & android sdk 5.3.0
2. android add newApiName api
3. ios add newApiName api
```

## 注意事项

- **必须逐项检查更新日志，不要遗漏任何新增API**
- 确保Android和iOS的SDK版本对应关系正确
- 新增API的封装需要保持与现有API风格一致
  - 在 `interface.uts` 中定义类型
  - 在对应平台的 `index.uts` 中实现并导出
  - 保持与现有代码风格一致（如命名规范、参数类型等）
- 更新后建议进行测试验证
  - 在 `pages/index/index.uvue` 中添加测试代码
  - 在真机上测试新增API功能
- **如果更新日志中的文本识别有问题（如缺少字母），需要手动访问官方文档确认**
- iOS静态库更新时，注意检查是否需要更新 `PrivacyInfo.xcprivacy` 文件
- Android依赖更新时，注意检查是否需要更新 `minSdkVersion` 或其他配置
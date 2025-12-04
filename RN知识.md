1.
| 工具                | 分类           | 作用                    |
| ----------------- | ------------ | --------------------- |
| **npm**           | 包管理器         | 安装依赖、管理 node_modules  |
| **yarn**          | 包管理器         | 更快更现代的依赖管理            |
| **bun（部分功能）**     | 包管理器         | 超快依赖安装 + JS runtime   |
| **bundle（Metro）** | 打包器（Bundler） | 把 JS 打成 RN 可执行 bundle |

npm / yarn / bun（的包管理部分）是一类：
→ 它们都是 “包管理器（Package Manager）”。

它们负责：

安装依赖（node_modules）

管理版本（package.json / lockfile）

运行脚本（npm run / yarn xyz / bun run）

三者的区别只是实现方式、速度、生态兼容性不同，但功能类别相同。

❌ bundle（Metro）不是这一类

bundle（Metro bundler）完全不是包管理器。
→ 它是 “打包构建工具（Bundler）”。

作用：

把你写的 JS/TS 代码打包成 RN 能运行的 bundle 文件

提供开发时热更新（fast refresh）

生成生产环境的 main.jsbundle / index.bundle

它跟 npm/yarn/bun 的作用完全不同，属于构建系统的一部分。

2.metro介绍
🗂️ React Native Local Setup 流程全图（Metro 在哪一步）

下面是完整 local dev 流程：

yarn install
安装依赖（跟 Metro 无关）

cd ios & pod install（iOS）
安装原生模块（跟 Metro 无关）

运行 app（最关键）

yarn ios
yarn android


👇 👇
🔥 Metro 在这里启动（开发模式 bundling）

Metro 启动后

watch JS/TS 文件

监听更改

给模拟器/真机推送热更新

on demand 编译 JS bundle

📦 那正式打包（生产环境）时 Metro 在哪？

当你执行：

npx react-native bundle ...


or 在 Xcode / Gradle 构建 release 时：

./gradlew assembleRelease
Xcode Product → Archive


RN CLI 会再调用一次 Metro（生产模式）：

📍 Metro 第二次出现

→ 用来生成 main.jsbundle / index.android.bundle

🧨 最清晰一句话总结
🟦开发阶段：Metro 在你 run-ios / run-android 的时候自动启动（提供热加载）。
🟧生产阶段：构建 release 包时 Metro 被调用一次，生成最终的 JS bundle。

3.pod install 是干嘛的？它和 yarn install 什么关系？有什么区别？什么时候要跑？

🧩 一句话总览
步骤	负责领域	作用
yarn install	JavaScript 世界	安装 JS/TS 依赖（node_modules）
pod install	iOS 原生世界	安装/更新 iOS 原生依赖（Pods）

它们完全不是同一类东西，也不互相替代。

🔵 1. yarn install 是干嘛的？

安装 JS 依赖（React、React Native、第三方库等） → 放到 node_modules 里。

示例：

react

react-native

redux

axios

你的 mini-app 的 JS 代码

...等 JS 包

只影响 JavaScript 层，不会改动 Xcode 项目。

🔴 2. pod install 是干嘛的？

安装 iOS 原生依赖 → 放到 ios/Pods 里。

它是 iOS CocoaPods 工具的一部分，专门管理：

RN 原生模块（Native Modules）

第三方 iOS SDK

原生依赖编译设置

bridging headers

iOS frameworks

Pod 管的东西来自 Podfile：

target 'YourApp' do
  pod 'React', :path => '../node_modules/react-native/React'
  pod 'Yoga', :path => '../node_modules/react-native/ReactCommon/yoga'
  ...
end


当你运行 pod install 时：

创建设备所需的 Xcode workspace

把原生依赖下载、编译配置好

链接 node_modules 中的原生库

🟡 3. yarn install 和 pod install 的关系
❌ 完全不一样

它们分别属于两个世界：

yarn → JavaScript 依赖

pod → iOS 原生依赖

✔️ 但 pod install “依赖” yarn install 的结果

因为 Podfile 里的许多原生库来自：

../node_modules/xxx


也就是说：

pods 里很多原生模块 = 来自 node_modules 的包。

所以正确顺序是：

👉 先 yarn install

（把 node_modules 装好）

👉 再 pod install

（把 node_modules 里的原生部分链接进 iOS 项目）

🟢 4. 什么时候需要 pod install？
🔥 必须 pod install 的情况

你做了以下任意情况：

① 新加 RN 原生模块

例如：

yarn add react-native-device-info


因为 device-info 有 iOS 原生组件，需要：

cd ios && pod install

② 升级 react-native 版本

因为原生依赖变化很大。

③ Podfile 修改了

必须重新 pod install。

④ 删除 node_modules 并重新安装

因为 Pods 引用 node_modules，需要同步更新。

🧊 不需要 pod install 的情况

你只改 JS 文件

改 UI、逻辑、业务代码

改 TypeScript

改 Redux、GraphQL、Axios

改 Metro 配置

改 Android 独有代码（不会影响 iOS）

这类情况只需要：

yarn ios


Metro 会热更新，pod 绝对不需要。

4.Podfile跟CocoaPods啥关系
ChatGPT said:

下面用一个极清晰、极直观的比喻告诉你：
Podfile 和 CocoaPods 到底是什么关系？

🧩 一句话总结

Podfile = 配方（recipe）
CocoaPods = 厨师（chef）

你写 Podfile → CocoaPods 按照它来执行。

🟦 Podfile 是什么？

Podfile = 一个配置文件（清单），告诉 iOS 工程要安装哪些原生库。

例如：

target 'MyApp' do
  pod 'React', :path => '../node_modules/react-native/React'
  pod 'RNDeviceInfo', :path => '../node_modules/react-native-device-info'
end


你在 Podfile 里“写规则”。

🟥 CocoaPods 是什么？

CocoaPods = 一个工具（依赖管理器），执行 pod install 来安装原生依赖。

当你运行：

pod install


CocoaPods 会做下面的事情：

读取 Podfile

下载所有写在 Podfile 里的原生库

放到 Pods/ 文件夹

创建 .xcworkspace（Xcode 要使用的工程入口）

生成 Podfile.lock 锁定版本

配置 Xcode build settings

🟩 最清晰关系图
你写的 Podfile (清单/规则)
         ↓   被 CocoaPods 读取
CocoaPods (工具/执行者)
         ↓   执行 pod install
生成 Pods (原生库)
生成 Podfile.lock
生成 .xcworkspace


所以：

Podfile 是“说明书”

CocoaPods 是“执行说明书的工具”

5.Node = JavaScript 引擎
npm / yarn = 安装依赖的工具
npm install / yarn install = 把包装进 node_modules
corepack enable = 让 Node 自动管理 yarn/pnpm 的版本，避免混乱

corepack enable 实际做了什么？
✔️ ① 启用 Node 自带的 yarn 代理（shim）

当你运行：

yarn


它不再调用你电脑上全局安装的 yarn。

而是调用：

Node 内置的 corepack → corepack 根据项目要求的 yarn 版本执行


换句话说：

👉 你的电脑不需要提前安装 yarn
👉 项目指定 yarn@3.6.1，corepack 就自动使用 yarn@3.6.1

✔️ ② 自动根据项目里的 packageManager 字段下载 yarn

你的项目可能有：

"packageManager": "yarn@3.6.1"


当你执行：

corepack enable
yarn install


corepack 会自动帮你：

下载 yarn 3.6.1

激活它

把 3.6.1 作为此项目的 yarn

不管你本地有没有 yarn，版本都统一。

最通俗总结（你能直接背）
**corepack enable = 禁用全局 yarn，转为由 Node 自动下载并使用项目需要的 yarn 版本。

这样所有人都用同一版 yarn，不会再出现“版本不一致”的问题。**

monorepo 是干嘛的？

Monorepo 是整个 Walmart Allspark Core 的基础概念。
你现在用的 allspark-core / integrated-people-experience 全部是 monorepo。

下面一句话让你秒懂：

👉 monorepo = 一个仓库里包含多个项目、多个包的“大仓库结构”。

不是一个项目一个 repo，而是：

7.FlatList 是一个 高性能列表组件，用于渲染长列表。

最核心的三个 props：

属性	作用
data	数据数组
renderItem	每一行怎么显示
keyExtractor	返回每行唯一 key

一个大 repo（mono）

里面有多个 package（repo）

8.react里面函数必须有return,而且必须是return()不是{}

6.

# 1. 包管理器 vs 打包器区别（npm / yarn / bun / Metro）

| 工具                | 分类           | 作用                    |
| ----------------- | ------------ | --------------------- |
| **npm**           | 包管理器         | 安装依赖、管理 node_modules  |
| **yarn**          | 包管理器         | 更快更现代的依赖管理            |
| **bun（部分功能）**     | 包管理器         | 超快依赖安装 + JS runtime   |
| **bundle（Metro）** | 打包器（Bundler） | 把 JS 打成 RN 可执行 bundle |

---

## 🟦 npm / yarn / bun（的包管理部分）是一类工具

它们负责：

- 安装依赖（node_modules）
- 管理版本（package.json / lockfile）
- 运行脚本（npm run / yarn xyz / bun run）

❗三者功能相同，只是速度与生态不同。

---

## 🟥 Metro (bundle) 完全不是包管理器

Metro 是 **React Native 专用打包器**：

- 把 JS/TS 打包成 RN 可执行的 bundle
- 提供开发热更新（Fast Refresh）
- 生产环境生成 main.jsbundle / index.bundle

---

# 2. Metro 在 React Native 的流程中做什么？

## 🗂️ React Native Local Setup 全流程（Metro 在哪里）

### 1. 安装依赖（与 Metro 无关）
```
yarn install
```

### 2. 安装 iOS Pods（与 Metro 无关）
```
cd ios && pod install
```

### 3. **运行 app 时 Metro 启动（开发模式 bundling）**
```
yarn ios
yarn android
```

Metro 会：

- 打包 JS（Development Mode）
- 监听文件变化
- 推送热更新（Fast Refresh）
- 按需编译 JS bundle

---

## 🏭 生产构建时 Metro 再跑一次

执行：

```
npx react-native bundle ...
```

或：

```
./gradlew assembleRelease
Xcode → Archive
```

Metro 会：

- 生成生产环境 bundle  
  - `main.jsbundle` (iOS)
  - `index.android.bundle` (Android)

---

## 🟦 最终总结（可背）

- **开发阶段：** Metro 自动随 run-ios / run-android 启动  
- **生产阶段：** 打包 Release 时 Metro 再跑一次生成 bundle  

---

# 3. `pod install` 是什么？与 `yarn install` 的关系和区别？

## 🧩 一句话对比

| 步骤          | 负责领域     | 作用                           |
|--------------|-------------|--------------------------------|
| **yarn install** | JavaScript | 安装 JS 依赖 → node_modules       |
| **pod install**  | iOS 原生     | 安装 iOS 原生依赖 → Pods/

它们完全不属于同一类工具。

---

## 🔵 `yarn install` 做什么？

安装 JS 依赖，例如：

- react  
- react-native  
- redux  
- axios  

📌 **只影响 JS 层，不动 Xcode 工程。**

---

## 🔴 `pod install` 做什么？

安装 iOS 原生依赖，包括：

- RN 原生模块（Camera、DeviceInfo 等）
- iOS frameworks
- 原生 SDK

执行时：

1. 读取 Podfile  
2. 安装 Pods  
3. 生成 Xcode `.xcworkspace`  
4. 链接 node_modules 里的原生代码  
5. 生成 Podfile.lock  

---

## 🟡 两者的关系（非常重要）

`pod install` 依赖 `yarn install` 的 node_modules。

正确顺序：

```
yarn install
cd ios
pod install
```

---

## 🟢 什么时候需要 pod install？

需要：

1. 新增 RN 原生模块  
2. 升级 React Native  
3. 修改 Podfile  
4. 重新安装 node_modules  

不需要：

- 改 JS 代码  
- 改 UI  
- 改 hooks、redux、axios  
- 改 Android 代码  

只跑：

```
yarn ios
```

---

# 4. Podfile 与 CocoaPods 的关系

## 🧩 一句话总结

**Podfile = 配方（规则）  
CocoaPods = 厨师（执行者）**

---

## 🟦 Podfile 是什么？

描述 iOS 原生依赖的清单：

```ruby
pod 'React', :path => '../node_modules/react-native/React'
pod 'RNDeviceInfo', :path => '../node_modules/react-native-device-info'
```

你写规则。

---

## 🟥 CocoaPods 是什么？

执行：

```
pod install
```

它会：

- 读取 Podfile  
- 安装 Pods  
- 生成 Pods 文件夹  
- 生成 Podfile.lock  
- 生成 .xcworkspace  

---

## 🟩 最清晰关系图：

```
Podfile (规则)
      ↓
CocoaPods (工具)
      ↓
pod install 执行
      ↓
Pods / Podfile.lock / .xcworkspace
```

---

# 5. Node / npm / yarn / corepack 的关系

## 总览：

- **Node = JavaScript 运行环境 (V8)**  
- **npm / yarn = 包管理器**  
- **npm install / yarn install = 安装依赖**  
- **corepack enable = 启用 Node 自动管理 yarn/pnpm 版本**

---

## corepack enable 做了什么？

### ✔ 1. 启用 yarn 代理（shim）
执行：

```
yarn
```

不再执行全局 yarn，而是通过 corepack 调用 **项目需要的 yarn 版本**。

---

### ✔ 2. 自动根据 packageManager 字段下载 yarn

如果项目写了：

```json
"packageManager": "yarn@3.6.1"
```

执行：

```
corepack enable
yarn install
```

corepack 自动：

- 下载 yarn 3.6.1  
- 激活它  
- 强制项目使用该版本  

---

## 🟦 一句话总结（可背）

**corepack enable = 禁用全局 yarn，让 Node 自动下载并使用项目指定版本的 yarn。**

保证团队 yarn 版本一致。

---

# 6. Monorepo 是干嘛的？

## 🟦 一句话解释：

**Monorepo = 一个仓库（repo）里包含多个项目/包的大型工程结构。**

例如：

```
/repo-root
  /packages
    /app1
    /app2
    /utils
    /shared-hooks
```

优势：

- 多包代码共享  
- 版本统一  
- 更适合集成大型项目  

Walmart Allspark、Integrated People Experience 都是 Monorepo。

---

# 7. FlatList 是什么？

FlatList 是 **高性能虚拟化列表组件**，用于渲染长列表。

核心 props：

| 属性 | 作用 |
|------|------|
| **data** | 数据数组 |
| **renderItem** | 每一行怎么展示 |
| **keyExtractor** | 返回唯一 key |

---

# 8. React 里函数必须 return，而且必须 return(...) 不是 {}

## 原因：

### `{}` 在 JS 中是 **代码块**，不是返回值。

错误写法：

```jsx
return {
  <Text>Hello</Text>
}
```

JS 当成函数体 → 没 return → 返回 undefined → React 报错。

正确写法：

```jsx
return (
  <Text>Hello</Text>
)
```

也可以：

```jsx
return <Text>Hello</Text>
```

---


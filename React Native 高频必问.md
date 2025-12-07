React Native 和 React（Web）有什么区别？运行环境分别是什么？
Keywords: native components vs DOM, platform APIs, Metro bundler

解释 React Native 的 Bridge / 新架构（Fabric + JSI）的大致思路。
Keywords: JS thread, native modules, async bridge, JSI, TurboModules

如何在 RN 里做导航？Stack / Tab / Drawer 分别适合什么场景？
Keywords: React Navigation, navigation props, deep linking

ScrollView 和 FlatList 的区别？什么时候必须用 FlatList？ ← 很常问
Reddit
+1

Keywords: virtualization, performance, infinite list

RN 中样式系统（StyleSheet / inline styles）和 Web CSS 有什么不同？
Keywords: Flexbox-only, no cascade, no class selectors, style props

如何在 RN 里做网络请求和状态管理？
Keywords: Fetch/axios, Redux/Zustand/RTK Query, React Query

解释一下 RN 中的多线程：JS thread / UI thread / Native Modules。
Keywords: blocking JS thread, InteractionManager, offloading work

React Native 常见性能问题和优化手段？
Keywords: useMemo/useCallback, React.memo, useNativeDriver for animations, list virtualization

如何处理平台差异（iOS / Android）？
Keywords: Platform.OS, Platform.select, .ios.tsx/.android.tsx, SafeAreaView

如何在 RN 里上传文件 / 处理图片？
Keywords: ImagePicker, FormData, presigned URL, performance (resize/compress)

RN 中如何管理环境变量和不同构建配置（dev/stage/prod）？
Keywords: .env, react-native-config, build variants, scheme/flavor

RN 应用的测试策略？
Keywords: Jest, React Native Testing Library, Detox (E2E)

有没有写过/接触过原生模块（Native Modules）？简单描述一下如何桥接一个原生功能。
Keywords: NativeModules, iOS Swift/ObjC, Android Kotlin/Java

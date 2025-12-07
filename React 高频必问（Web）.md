What is React? 它的核心思想和主要特性是什么？
Keywords: component-based, declarative, virtual DOM, one-way data flow

什么是 JSX？JSX 如何被编译？
Keywords: JSX to React.createElement, Babel, syntax sugar

解释 Virtual DOM 和 diff / reconciliation 过程。优缺点？
Keywords: virtual DOM, diffing algorithm, keys

React 组件有哪几类？函数组件 vs Class 组件的区别？
Keywords: functional components, hooks, lifecycle methods

state 和 props 的区别？什么时候“提升状态”（lift state up）？
Keywords: unidirectional data flow, controlled components

什么是受控组件（controlled）和非受控组件（uncontrolled）？
Keywords: form input, refs, defaultValue vs value

说一下 React Hooks：useState / useEffect 的工作原理和常见坑。
Keywords: closures, stale state, dependency array

useMemo 和 useCallback 的区别？什么时候用，防止什么问题？ ← 最近美国面试超高频
LinkedIn

Keywords: memoization, referential equality, re-renders

React 如何做性能优化？
Keywords: React.memo, code splitting, list virtualization, avoiding unnecessary re-renders

列表渲染时 key 的作用是什么？为什么不能用 index 当 key？
Keywords: reconciliation, reordering, bugs in controlled inputs

Context 是干什么的？和 Redux 相比什么时候用 Context，什么时候用状态管理库？
Keywords: prop drilling, global state, performance

React 中错误边界（Error Boundary）是什么？能捕获哪些错误？
Keywords: class component componentDidCatch, render phase vs event handlers

说一下你是怎么在 React 项目里做数据请求的（data fetching）？
Keywords: useEffect, React Query/TanStack Query, SWR, caching, loading/error states

React Router（或其他路由库）的基本概念？
Keywords: SPA, route params, nested routes, code splitting with routes

如何在 React 中做表单管理和校验？
Keywords: controlled inputs, form libraries (Formik/React Hook Form), validation

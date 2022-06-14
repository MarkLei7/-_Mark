#### Hook
- Effect Hook
    把这个函数传给React，在DOM更新后调用它，保留了函数的context，闭包
清除effect，例如订阅外部数据源，清除是很重要的，可以防止内存泄漏
在React class中，在componentDidMount中订阅，在componentWillUnmount中清除
effect返回一个函数，React将会在执行清除操作时调用它
React会在组件卸载的时候执行清除操作，effect的清除阶段在每次重新渲染时都会执行
取消了componentDidUpdate的步骤，不需要特定的代码来处理更新逻辑，useEffect默认就会处理
它会在调用一个新的effect之前对前一个effect进行清理

- Hook规则
    只在最顶层使用Hook
    只在React函数中调用Hook

- 自定义Hook
    在组件之间共享状态逻辑
    始终以use开头
# ractor-react

## 安装

```ts
npm i ractor-react
```

## state store vs domain store

`ractor-react` 支持两种用法，分别是 `动态加载` 和 `依赖注入`。分别对应 `mobx` 的 `state store` 和 `redux` 的 `domain store`。

### state store

所谓 `state store`， 就是你可以把 store 当一个外部状态使用，和内部状态类似，view 是监听这个 state 的，也是动态加载和卸载的。

```ts
@Connect(CounterStore)
export class Counter extends React.Component<{ value: number }> {}
```

这里注入的 store 在组件 `didMount` 的时候初始化并订阅，在组件 `Unmount` 的时候取消监听。你可以把这个 store 和你组件放在一起，仅仅作为状态和行为的集合，和视图做逻辑分离。

具体代码可以看 examples 里的 counter

### domain store

`domain store`。类似领域驱动设计里的 `model`，对应后端的 model 层，也可以简单的理解为对应数据库里的表。和 redux 的 reducer 有点类似。

```ts
render(
  <Provider stores={[TodoStore]}>
    {React.createElement(Todo)}
  </Provider>,
  document.getElementById("app")
)
```

使用 `Provider` 组件注入 我们所有的 store，Provider 内部会依次全部实例化，并放在 context 里面。

```ts
@Providers([AStore, BStore], (aStore, bStore) => ({}))
export default class TodoComponent extends React.Component {}
```

使用 `Providers` 从全局中选择性定义传入的 store

## Api

### Privider

高阶组件，需要传入 stores 数组。

```ts
<Provider system={system} stores={[TodoStore, CounterStore]}>
  // 你的组件
</Provider>,
```

### connect

实现动态注入 store 的函数，方便喜欢 pub/sub 模式的同学，连接 store 和 react 的高阶组件。会在组件初始化传入的 store， 并监听之。组件卸载的时候同时从 system 中卸载 store.

```ts
@Connect(TodoStore)(TodoComponent)
```

### Providers

依赖注入的一直实现方式。`Providers` 会从 context 中找到 provide 的实例的 state 通过 selector 筛选之后通过 props 传给你的组件。灵感上参考了很多 angular 的[依赖注入](https://angular.io/guide/dependency-injection)

```ts
@Providers([AStore, BStore], (aStore, bStore) => ({}))
```

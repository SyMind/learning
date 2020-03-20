```typescript
interface FiberRoot {
    // 根的类型（ledacy，batched，concurrent，等）
    tag: RootTag

    // 来自宿主的与此根关联的所有信息
    containerInfo: any
    // 仅用于持久更新
    pendingChildren: any
    // 当前活动的根 fiber。这是树的可变根。
    current: Fiber

    pingCache: WeakMap<Thenable, Set<ExpirationTime>>
        | Map<Thenable, Set<ExpirationTime>>

    finishedExpirationTime: ExpirationTime
    // 已经完成并可以提交的 work-in-progress HostRoot。
    finishedWork: Fiber
    // setTimeout 函数返回的 Timeout 的句柄。
    timeoutHandle: TimeoutHandle | NoTimeout
    // 顶层上下文对象，被 renderSubtreeIntoContainer 使用。
    context: Object
    pendingContext: Object
    // 确定是否需要在初始挂载时尝试 hydrate
    hydrate: boolean
    firstPendingTime: ExpirationTime
    firstSuspendedTime: ExpirationTime
    lastSuspendedTime: ExpirationTime
    nextKnownPendingLevel: ExpirationTime
    lastPingedTime: ExpirationTime
    lastExpiredTime: ExpirationTime
}

// Fiber 是在组件上需要完成或已经完成的工作。
// 每个组件可以有多个。
interface Fiber {
    // 这些先置的字段概念上属于 Instance。
    // 它以前被分为一个单独的类型，并与 Fiber 的其他字段交叉。
    // 但是 Flow 的交叉类型功能有 bug，我们只能将其合并为单个类型。

    // Instance 在所有版本的组件之间共享。我们可以很容易地将其分解为一个单独的对象，以避免将如此多的内容复制到树的其他版本。现在，我们将其放在单个对象上，以最小化在初始渲染期间创建的对象数。

    // Tag 确定 fiber 的类型
    tag: WorkTag

    // 该节点的唯一标识
    key: string

    // element.type 的值，用于在调度期间保存该节点的标识
    elementType: any

    // 与 fiber 相关的解析函数或类
    type: any

    // 与 fiber 相关的本地状态
    stateNode: any

    // 概念别名
    // parent : Instance -> return 父级恰好与 return fiber 相同，
    // 因为我们已经合并了 fiber 和 instance

    // 其余字段属于 Fiber。

    // 该节点处理完后返回至这个节点。
    return: Fiber

    // 单链表树结构（Singly Linked List Tree Structure）。
    child: Fiber
    sibling: Fiber
    index: number

    // ref 最后用于附加此节点。
    ref: (((handle: mixed) => void) & {_stringRef: ?string, ...})
        | RefObject

    // 输入是正在处理 fiber 的数据。Arguments。Props。
    pendingProps: any // 当我们重载了标签，此类型将更加具体。
    memoizedProps: any // 用于创建输出的 props。

    // 关于状态更新和回调函数的队列
    updateQueue: UpdateQueue

    // 用于创建输出的状态
    memoizedState: any

    // fiber 相关的依赖（contexts，events）
    dependencies: Dependencies
    mode: TypeOfMode

    // 副作用
    effectTag: SideEffectTag

    // 单链表中到达下一个具有副作用 fiber 的最短路径
    nextEffect: Fiber

    // 当前子树中第一个和最后一个具有副作用的 fiber。
    firstEffect: Fiber
    lastEffect: Fiber

    // 代表一个该工作必须要完成的未来时间。
    // 不包含在其子树中找到的工作。
    expirationTime: ExpirationTime

    // 这用于快速确定子树是否没有挂起的更改。
    childExpirationTime: ExpirationTime

    // 这是 Fiber 的汇总版本。每个获得更新的 fiber 最终都有一个。
    // 在某些情况下，我们可以清理它以节省内存。
    alternate: Fiber
}


//#region SideEffectTag

type SideEffectTag = number

const NoEffect = 0b0000000000000
const PerformedWork = 0b0000000000001

const Placement = 0b0000000000010
const Update = 0b0000000000100
const PlacementAndUpdate = 0b0000000000110
const Deletion = 0b0000000001000
const ContentReset = 0b0000000010000
const Callback = 0b0000000100000
const DidCapture = 0b0000001000000
const Ref = 0b0000010000000
const Snapshot = 0b0000100000000
const Passive = 0b0001000000000
const Hydrating = 0b0010000000000
const HydratingAndUpdate = 0b0010000000100

//#endregion
```

react 包中为所有全局 React API

createElement(type, config, children)
1.原生元素 type 为字符串
2.自定义组件为 class / function

\$\$typeof 是用来表示元素是什么类型的

## 创建更新方式

1. ReactDOM.render || hydrate
2. setState
3. forceUpdate

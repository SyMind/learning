# @tarojs/shared

Taro 内部使用的 utils。

```typescript
class BaseTemplate {
    private buildAttribute (attrs: Attributes, nodeName: string): string
    protected replacePropName (name: string, value: string, _componentName?: string): string
    protected createMiniComponents (components: Components): Components
    protected buildBaseTemplate (): string
    protected buildThirdPartyAttr (attrs: Set<string>): string
    protected buildComponentTemplate (comp: Component, level: number): string
    protected buildFocusComponentTemplte (comp: Component, level: number): string
    protected buildStandardComponentTemplate (comp: Component, level: number): string
    protected buildPlainTextTemplate (level: number): string
    protected buildThirdPartyTemplate (level: number, componentConfig: ComponentConfig): string
    protected buildContainerTemplate (level: number, restart = false): string
}
```

```typescript
class TaroRootElement extends TaroElement {
    private pendingUpdate = false
    private updatePayloads: UpdatePayload[] = []
    private pendingFlush = false
    private updateCallbacks = []
    public ctx = null

    public enqueueUpdate (payload: PudatePayload): void
    public performUpdate (initRender = false, prerender?: Function): void
    public enqueueUpdateCallback (cb: Function, ctx?: Record<string, any>): void
}

function createPageConfig (component: any, pageName?: string, data?: Record<string, unknow>, pageConfig?: PageConfig): PageInstance

interface Current {
    app: AppInstance | null,
    router: Router | null,
    page: PageInstance | null
}

const eventSource = new Map<string | undefined | null, TaroNode>()
```
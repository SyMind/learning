# vscode 源码阅读 - 依赖注入

## 依赖的定义

> 依赖（dependency）是指两个不同实体间的一种联系，如果没有其中一个，另一个就会缺少某些功能甚至会不存在。
> —— 引自《C#敏捷开发实践》

## 依赖注入

依赖注入是使用容器将接口和其实现类关联起来，并解析相关的依赖，在创建实例时所需的依赖注入其中。依赖注入容器有两个基本功能：

1. 注册接口及其实现类之间的映射关系
2. 解析实现类的所有相关依赖，并根据依赖关系构造实现类的实例

## vscode 中的实现

以 vscode 的实现来探究如何在 typescript 项目中实现依赖注入。

### ServiceIdentifier 函数接口

``` typescript
interface ServiceIdentifier<T> {
	(...args: any[]): void;
	type: T;
}
```

由于 typescript 编译为 javascript 后，将失去接口这个语法单位，所以定义了 ServiceIdentifier 函数接口，使用它的实例作为接口的标识。

在 vscode 的源码中创建该接口实例的唯一方式就是使用 createDecorator 函数，从如下代码可知该函数返回的 ServiceIdentifier 的实例为一个参数装饰器，该装饰器将使用在类的构造函数参数中，标注参数的接口类型，使得能够在容器构造实例时获取其依赖关系。

```typescript
export function createDecorator<T>(serviceId: string): ServiceIdentifier<T> {

	if (_util.serviceIds.has(serviceId)) {
		return _util.serviceIds.get(serviceId)!;
	}

	const id = <any>function (target: Function, key: string, index: number): any {
		if (arguments.length !== 3) {
			throw new Error('@IServiceName-decorator can only be used to decorate a parameter');
		}
		storeServiceDependency(id, target, index, false);
	};

	id.toString = () => serviceId;

	_util.serviceIds.set(serviceId, id);
	return id;
}
```

在 typescript 允许类型声明和变量声明同名，在 vscode 源码中将 createDecorator 函数返回的对象与关联的接口保持同名，使得模块引入时即可作为接口类型使用又可以作为参数装饰器使用。

### ServiceCollection 类

``` typescript
class ServiceCollection {
    private _entries: Map<ServiceIdentifier<any>, any>
    set<T>(id: ServiceIdentifier<T>, instanceOrDescriptor: T | SyncDescriptor<T>): T | SyncDescriptor<T>
    forEach(callback: (id: ServiceIdentifier<any>, instanceOrDescriptor: any) => any): void
    has(id: ServiceIdentifier<any>): boolean
    get<T>(id: ServiceIdentifier<T>): T | SyncDescriptor<T>
}
```

该类管理接口与其实现类之间的映射关系，提供 set 方法进行注册。对比 C# 中依赖注入容器一般提供的方法签名：

``` C#
void Register<TInterface, TImplementation>()
    where TImplementation : TInterface
```

由于 javascript 中不存在接口这个语法单位，所以第一个参数使用 ServiceIdentifier 的实例来作为接口标识。

### InstantiationService 类

该类继承自 IInstantiationService 接口，接口的定义如下：

``` typescript
interface IInstantiationService {
    _serviceBrand: undefined;

	createInstance<T>(descriptor: descriptors.SyncDescriptor0<T>): T;
	createInstance<A1, T>(descriptor: descriptors.SyncDescriptor1<A1, T>, a1: A1): T;
	createInstance<A1, A2, T>(descriptor: descriptors.SyncDescriptor2<A1, A2, T>, a1: A1, a2: A2): T;
	createInstance<A1, A2, A3, T>(descriptor: descriptors.SyncDescriptor3<A1, A2, A3, T>, a1: A1, a2: A2, a3: A3): T;
	createInstance<A1, A2, A3, A4, T>(descriptor: descriptors.SyncDescriptor4<A1, A2, A3, A4, T>, a1: A1, a2: A2, a3: A3, a4: A4): T;
	createInstance<A1, A2, A3, A4, A5, T>(descriptor: descriptors.SyncDescriptor5<A1, A2, A3, A4, A5, T>, a1: A1, a2: A2, a3: A3, a4: A4, a5: A5): T;
	createInstance<A1, A2, A3, A4, A5, A6, T>(descriptor: descriptors.SyncDescriptor6<A1, A2, A3, A4, A5, A6, T>, a1: A1, a2: A2, a3: A3, a4: A4, a5: A5, a6: A6): T;
	createInstance<A1, A2, A3, A4, A5, A6, A7, T>(descriptor: descriptors.SyncDescriptor7<A1, A2, A3, A4, A5, A6, A7, T>, a1: A1, a2: A2, a3: A3, a4: A4, a5: A5, a6: A6, a7: A7): T;
	createInstance<A1, A2, A3, A4, A5, A6, A7, A8, T>(descriptor: descriptors.SyncDescriptor8<A1, A2, A3, A4, A5, A6, A7, A8, T>, a1: A1, a2: A2, a3: A3, a4: A4, a5: A5, a6: A6, a7: A7, a8: A8): T;

	createInstance<Ctor extends new (...args: any[]) => any, R extends InstanceType<Ctor>>(t: Ctor, ...args: GetLeadingNonServiceArgs<ConstructorParameters<Ctor>>): R;
	createInstance<Services extends BrandedService[], Ctor extends new (...services: Services) => any, R extends InstanceType<Ctor>>(t: Ctor): R;

	invokeFunction<R, TS extends any[] = []>(fn: (accessor: ServicesAccessor, ...args: TS) => R, ...args: TS): R;
	createChild(services: ServiceCollection): IInstantiationService;
}
```

该实现类使用 ServiceCollection 收集来的接口与其实现类之间的映射关系，使用 ServiceIdentifier 类型的参数装饰器获取实现类的所有依赖关系，以创建指定类的实例。定义 createInstance 和 invokeFunction 方法便于外界使用容器中创建的实例。createChild 方法用于创建子容器，子容器继承父容器中管理的所有接口与其实现类的映射关系、实现类的依赖关系和创建的实现类实例。


#### 使用有向图对依赖关系建模

在外部调用容器中未创建的接口实现类实例时，将以该实现类作为起点，使用有向图对类的依赖关系进行建模。有向图中可会形成循环，代码中使用了循环次数不大于 150 次的启发式算法来判断是否存在循环依赖。

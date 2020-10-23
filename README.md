## Children

### Children.forEach
> Children.forEach(childrens, Mapfunction, mapcontent)
> 
对DOM树进行遍历;
```javascript
Mapfunction: function(child, index) {
  // this 指向mapcontent；
}
```
### Children.map
> Children.map(childrens, Mapfunction, mapcontent)
> 
对DOM树进行遍历并返回一个虚拟DOM组成的数组如果数组项还是数组，则进行递归调用
```javascript
Mapfunction: function(child, index) {
  // this 指向mapcontent；
  return ...;
}
```
### Children.count
> Children.count(children)
> 
对DOM树进行遍历需要的次数，最少为<font color=#e22>1</font>
### Children.toArray
> Children.toArray(children)
> 
将传入的children转化为符合react使用规范的数组，会转化children中的undefined、boolean等
### Children.only
> Children.only(children)
> 
判断children是否是react虚拟DOM对象，不是则抛错

## createRef

### createRef
用来生成一个`{ current: null }`对象

## Component&PureComponent

### Component
默认的Component拥有如下属性
```javascript
function Component(props, context, updater) {
  this.props = props;
  this.context = context;
  // If a component has string refs, we will assign a different object later.
  // emptyObject为一个空对象
  this.refs = emptyObject;
  // We initialize the default updater but the real one gets injected by the
  // renderer.
  this.updater = updater || ReactNoopUpdateQueue;
}
```
#### Component拥有如下原型

```javascript
Component.prototype.isReactComponent = {};

Component.prototype.setState = function (partialState, callback) {
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};

Component.prototype.forceUpdate = function (callback) {
  this.updater.enqueueForceUpdate(this, callback, 'forceUpdate');
};
```

## createContext

### createContext
创建一个react上下文

```typescript
function createContext<T>(
  defaultValue: T,
  calculateChangedBits: ?(a: T, b: T) => number,
): ReactContext;

type ReactContext<T> = {
  $$typeof: Symbol | number,
  Consumer: ReactContext<T>,
  Provider: ReactProviderType<T>,
  _calculateChangedBits: ((a: T, b: T) => number) | null,
  _currentValue: T,
  _currentValue2: T,
  _threadCount: number,
};
```

## createContext

### createContext
用来转发底层DOM节点的ref
```javascript
function forwardRef<Props, ElementType: React$ElementType>(
  render: (props: Props, ref: React$Ref<ElementType>) => React$Node,
) {
  return {
    $$typeof: REACT_FORWARD_REF_TYPE,
    render,
  };
}
```

## lazy

### lazy
```javascript
function lazy<T, R>(ctor: () => Thenable<T, R>): LazyComponent<T> {
  let lazyType = {
    $$typeof: REACT_LAZY_TYPE,
    _ctor: ctor,
    // React uses these fields to store the result.
    _status: -1,
    _result: null,
  };
  return lazyType;
}
```
Thenable为一个同Promise类似的方法

## Fragment&Profiler&StrictMode&Suspense

### Fragment
对子列表分组，无需添加额外的节点包裹

### Profiler
用来监控渲染一次react组件需要的各种开销

### StrictMode
严格模式，用来检测react中是否使用了过时的API或者错误使用了react生命周期等

### Suspense
异步加载数据，然后渲染react元素

## render

### render
render接受3个参数，`render(element, container, callback)`，一个react元素，一个容器，一个回调函数。
container必须是一个有效的html节点，否则会报错。
render会首先把container上的所有子节点remove掉。
render会在container上添加一个<font color=#e22>_reactRootContainer</font>属性，值为一个包含<font color=#e22>_internalRoot</font>对象一个<font color=#e22>render</font>方法和<font color=#e22>unmount</font>方法的对象。
```javascript
_internalRoot = {
  tag = 0;                          // 根节点表示，0|1|2，这里为0
  current = current;                // current是一个对象，对象内容如下
  containerInfo = container;        // 就是传入的html节点
  pendingChildren = null;
  pingCache = null;
  finishedExpirationTime = NoWork;
  finishedWork = null;
  timeoutHandle = noTimeout;
  context = null;
  pendingContext = null;
  hydrate = false;                   // 是否挂载在body节点，这里为false
  callbackNode = null;
  callbackPriority = NoPriority;
  firstPendingTime = NoWork;
  firstSuspendedTime = NoWork;
  lastSuspendedTime = NoWork;
  nextKnownPendingLevel = NoWork;
  lastPingedTime = NoWork;
  lastExpiredTime = NoWork;

  interactionThreadID = unstable_getThreadID();
  memoizedInteractions = new Set();
  pendingInteractionMap = new Map();

  hydrationCallbacks = null;
}

current = {
  // Instance
  tag: 0;                    // 这里为0
  key: null;
  this.elementType = null;
  this.type = null;
  this.stateNode = null;

  // Fiber
  this.return = null;
  this.child = null;
  this.sibling = null;
  this.index = 0;

  this.ref = null;

  this.pendingProps = null;
  this.memoizedProps = null;
  this.updateQueue = {
    baseState: null,
    baseQueue: null,
    shared: {
      pending: null,
    },
    effects: null,
  };
  this.memoizedState = null;
  this.dependencies = null;

  this.mode = mode;           // 这里应该为0（不确定）

  // Effects
  this.effectTag = 0b0000000000000;
  this.nextEffect = null;

  this.firstEffect = null;
  this.lastEffect = null;

  this.expirationTime = 0;
  this.childExpirationTime = 0;

  this.alternate = null;

  if (enableProfilerTimer) { // 非生产环境有效
    actualDuration = 0;
    actualStartTime = -1;
    selfBaseDuration = 0;
    treeBaseDuration = 0;
  }
}
```
render方法接收一个`ReactNodeList`类型的参数
之后，render会在container上添加一个__reactContainere${time}属性指向container.current



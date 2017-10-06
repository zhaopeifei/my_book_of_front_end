# React性能优化

## 单个React组件的性能优化

### shouldComponentUpdate

React利用Virtual DOM来提高渲染性能，但是Virtual DOM计算前后的区别仍然需要消耗时间。如果我们已经提前知道一个组件不应该更新，那么直接通过shouldComponentUpdate函数返回false，组件则不会进行接下来的update操作，也就不需要进行Virtual DOM的计算，可以节省很长的时间。

但是，做这种优化本来就需要额外的时间成本，那我们应该如果去决定做哪些组件的优化呢？一个是使用React Perf插件来进行组件的性能测量，找到性能瓶颈，另外一个就是对一些用的很多的组件进行性能优化，如果长列表的每个子组件等。

### pureRender

pure render的实现是重写shouldComponentUpdate函数。引入pure render后，组件在调用shouldComponentUpdate函数时，会对props和state做浅比较，然后根据浅比较结果返回true或false，决定是否render。pure render的使用，可以从一定程度上提高渲染性能。由于pure render是做浅比较来决定是否更新，所以需要务必保证props和state数据的扁平化结构，数据尽量不使用引用类型数据。

### 无状态组件

此外，还可以考虑将无状态组件写成函数形式，这样组件本身仅仅是一个函数，就省去了React标准组件生命周期函数执行等步骤，也可以大大提高组件的渲染效率。

## 多个React将组件的性能优化

React组件的挂载和卸载过程都是必须要经历的过程，可以使用上面的无状态组件来做优化，其他就没有很多的优化点。组件的优化更多的是关注更新的过程。

### React的调和\(Reconciliation\)过程

React的Reconciliation算法是用来更新React组件。当React要对比两个Virtual DOM的树形结构时，从根节点开始递归往下比对，然后根据节点类型做相应的操作。这种做法是性能和复杂度的最好折衷，该算法复杂度为O\(N\)

### 节点类型不同

如果树形结构的根节点类型不同，那就意味着改动太大，React会认为没有必要再做对比，会直接删除原有的树形结构，原有的组件会执行卸载过程，而新的组件会被添加到树形结构中，执行挂载过程。

```
<div>
    <Todos />
</div>
```

```
<span>
    <Todos />
</span>
```

如上面的两个组件，React组件在对比前后Virtual DOM时，会认为div和span类型不会，就直接会卸载上面的组件，然后装载下面的组件。这样会造成巨大的浪费。所以，在开发时，一定要避免作为包裹功能的节点类型的随意改变。

### 节点类型相同

节点类型相同时，React会进行更新过程，不会引发根节点的重新装载。在React中，节点类型有两种：DOM元素类型、React组件。对于DOM元素类型，React会保留节点对应的DOM元素，只对树形结构根节点上的属性和内容做一下对比，修改不同的部分。对于React组件，React会根据新节点props去更新原来根节点的组件实例，引发这个组件实例的更新过程，即一系列的更新过程生命周期函数。

在组件更新过程中，如果可以提前判断组件是否应该更新，在shouldComponentUpdate函数中提前返回false，会是一个性能优化方案。

### 多个子组件

```
<ul>
    <TodoItem text="one" />
    <TodoItem text="two" />
</ul>
```

假设有上面一个类型的列表，要在最上面加一项，得到下方的结构。

```
<ul>
    <TodoItem text="zero" />
    <TodoItem text="one" />
    <TodoItem text="two" />
</ul>
```

React组件会从上往下比较，会认为新结构中zero组件是旧结构中one组件的更新，认为新结构中one组件时旧结构中two组件的更新，而新结构中two组件则是新结构，要进行加载。要避免类似上述情形的浪费，需要给列表中的每一项制定一个唯一且稳定不变的key值（每个组件的唯一标识），这样，React就可以根据key值去区分同一类型的组件，避免出现上述的情形。

```
<ul>
{
    todos.map((item, index)) => (
        <TodoItem key={index} text={item.text} />
    )
}
</ul>
```

如上图的key的赋值方式便是错误的，因为不符合key值即唯一又稳定不变的要求。这种赋值方式仍然不能避免上面的情形的浪费，所以，一定要注意。正确的赋值方式可以如下：

```
<ul>
```

```
{
    todos.map((item)) => (
        <TodoItem key={item.id} text={item.text} />
    )
}
</ul>
```

## 数据性能优化

React和Redux是由数据驱动的，优化组件渲染是一大部分，而数据方面的性能优化也同样重要。

### reselect

reselect库的工作原理：只要相关状态没有改变，那就直接使用上一次的缓存结果。reselect是利用了缓存计算结果的方式，避免重复的大量结算，适用于要进行大量运算且重复度较高的场景。

### Immutable

在JavaScript中，无法通过 === 来判断两个对象是否相同，要判断两个对象是否相同需要做深比较，但是这样往往造成性能浪费。immutable表示不可变，它提供了一种存储方案，可以在使用了其库后，可以直接通过immutable来判断两个对象是否相等。

如果不使用immutable，要使用shouldComponentUpdate的话，需要对前后props和state做深比较，而使用后，直接通过 === 符号判断前后数据是否相等即可。

## 其他

### 直出

直出是目前常用的优化方案之一。一般情况下，页面需要先加载html，然后再加载js文件、css文件，这样的加载过程消耗巨大，所以React提供了一种服务器渲染的方案，即直出方案。React提供了服务器渲染的功能，即可以在服务器端就渲染后相应的DOM结构，用户拉取html后可以直接看到最终页面，节省了js、css文件的加载和渲染时间。相应的，服务器的压力也就变大了。




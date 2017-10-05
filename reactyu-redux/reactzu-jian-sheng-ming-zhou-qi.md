# React组价生命周期

React的组件的生命周期分为三个过程：

1. 装载过程\(Mount\)：第一次把组件渲染到DOM树的过程；
2. 更新过程\(Update\)：组件进行渲染更新的过程；
3. 卸载过程\(Unmount\)：组件从DOM树种删除的过程。

## 装载过程

装载过程主要进行如下操作：

1. constructor：构造函数，经常是为了初始化state或者绑定成员函数的this环境（建议直接使用箭头函数，则需要在构造函数中进行函数的bind操作）；
2. componentWillMount：预装载函数，不能进行修改state的操作，即使做了，也不会进行新数据状态的渲染。在该函数中做的操作，都可以提前到构造函数中，比较鸡肋。
3. render：渲染函数，唯一的一定不能省略的函数，必须有返回值，返回null或false表示不渲染任何DOM元素。它是一个仅仅用于渲染的纯函数，返回值完全取决于this.state和this.props，不能在函数中任何修改props、state、拉取数据等具有副作用的操作。render函数返回的是JSX的对象，该函数并不因为这渲染到DOM树，何时进行真正的渲染是有React库决定的。
4. componentDidMount：挂载成功函数。该函数不会再render函数调用完成之后立即调用，因为render函数仅仅是返回了JSX的对象，并没有立即挂载到DOM树上，而componentDidMount是在组件被渲染到DOM树之后被调用的。另外，componentDidMount函数在进行服务器端渲染时不会被调用。

在React 中，除了render函数之外，都有默认的函数实现，如果不要使用相应的生命周期函数则可以省略。constructor通常用于state的初始化操作，`this.state = {}; `函数绑定this建议在定义的时候直接使用箭头函数来实现，就不需要在constructor函数中进行this绑定操作了。componentWillMount用的很少，比较鸡肋。render函数必须实现，可以通过返回null来进行不渲染。componentDidMount通常用于服务器数据的拉取操作，之所以在componentDidMount中而不是在构造函数中进行数据拉取的原因在于：如果数据拉取回来了，即props已经有值了，但是组件还没有渲染出来，会报错。但是这里有一些把数据拉取提前到constructor函数的思路：在contructor函数中，通过promise来进行数据的拉取，并且绑定到this对象上，然后在componentDidMount中执行promise把数据更新到props上。

## 更新过程

当组件挂载到DOM树上之后，props/state被修改会导致组件进行更新操作。更新过程会以此调用如下的生命周期函数：

1. componentWillReceiveProps\(nextProps\)：该函数在组件进行更新以及**父组件render函数（不管数据是否发生了改变）**被调用后执行，this.props取得当前的props，nextProps传入的是要更新的props。
2. componentShouldUpdate\(nextProps, nextState\)：返回bool值，true表示要更新，false表示不更新，使用得当将大大提高React组件的性能，避免不需要的渲染。
3. componentWillUpdate：预更新函数。
4. render：渲染函数。
5. componentDidUpdate：更新完成函数。

相比装载过程的生命周期函数，更新过程的生命周期函数使用的相对来说要少一些。常用的是componentWillReceiveProps、componentShouldUpdate，前者经常用于根据前后两个数据去设置组件的状态，而后者则是常用于优化，避免不必要的渲染。

## 卸载过程

卸载过程只涉及一个函数componentWillUnmount，当React组件要从DOM树上删除前，会调用一次这个函数。这个函数经常用于去除componentDidMount函数带来的副作用，例如清楚计时器、删除componentDidMount中创造的非React元素。




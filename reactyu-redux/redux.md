# Redux基础

Redux有以下几个基本原则：

1. 单项数据流：数据流动方向可以跟踪，流动单一，追查问题更快捷。
2. 唯一数据源：应用的状态只应该存储在一个store上。
3. 保持状态只读：不能直接修改状态，只能通过触发action来进行状态的更新。
4. 数据改变只能通过纯函数来完成：只能通过reducer纯函数来获得最新的state，最新的state只由上次的state和触发的action来决定。

## Action

action表示一个动作，action实质上是一个JavaScript对象，通常情况下，一个action中有两个属性：type和data，type表示行为的类型，data表示这个行为中附带的数据。一般情况下，action由actionCreator函数来生成，其中的type一般也会被抽离出来，定义在另外一个js文件中。action的产生通常由actionCreator文件和actionType文件来生成。

## Reducer

reducer函数是纯函数，指明了要如何更新state。reduer的输入是上一次的state和本次的action，输出为新的state。

## Store

一个应用应该有一个单一的数据源，数据放置的地方即store。store中保存着一个应用的state。状态更新的过程为：一个store触发dispatch一个action，该store的reducer根据这个action进行state的更新，进而完成状态更新。

对于redux来说，在创造store的时候，需要制定初始状态下的state和状态的更新规则reducer。在有一个行为发生时，通过store.dispatch\(action\)来触发action，根据reducer规则来更新state。

一般情况下，store只支持同步数据流，如果要使用异步数据流，需要引入想要的异步数据流处理的库。如果使用redux-thunk的库来进行异步数据流操作，在组件中调用actionCreator函数时，会自动进行dispatch操作。但是，自动dispatch只适用于同步操作，如果是去服务器拉取数据等异步操作，需要在actionCreator函数中返回一个函数，然后手动执行dispatch操作。




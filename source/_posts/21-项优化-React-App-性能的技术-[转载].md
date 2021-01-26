---
title: "21 项优化 React App 性能的技术 [转载]"
categories: [ "JS" ]
tags: [ "react" ]
draft: false
slug: "twentyone-technologies-for-optimizing-react-app-performance"
date: "2019-07-17 16:32:00"
---

## 介绍

在 `React` 内部，`React` 会使用几项巧妙的小技术，来优化计算更新 `UI` 时，所需要的最少的更新 `DOM` 的操作。在大多数情况下，即使你没有针对性能进行专项优化，`React` 依然很快，但是仍有一些方法可以加速 `React` 应用程序。本文将介绍一些可用于改进 `React` 代码的有效技巧。


<!--more-->


## 1.使用不可变数据结构

数据不变性不是一种架构或者设计模式，它是一种编程思想。它会强制您考虑如何构建应用程序的数据流。在我看来，数据不变性是一种符合严格单项数据流的实践。

数据不变性，这一来自函数式编程的概念，可应用于前端应用程序的设计。它会带来很多好处，例如：

- 零副作用
- 不可变的数据对象更易于创建，测试，和使用；
- 利于解耦；
- 更加利于追踪变化；

在 `React` 环境中，我们使用 `Component` 的概念来维护组件内部的状态，对状态的更改可以导致组建的重新渲染。

`React` 构建并在内部维护呈现的UI（Virtual DOM）。当组件的 `props` 或者 `state` 发生改变时，`React` 会将新返回的元素与先前呈现的元素进行比较。当两者不相等时，`React` 将更新 DOM。因此，在改变状态时，我们必须要小心。

让我们考虑一个用户列表组件：

```javascript
state = {
   users: []
}

addNewUser = () =>{
   /**
    *  OfCourse not correct way to insert
    *  new user in user list
    */
   const users = this.state.users;
   users.push({
       userName: "robin",
       email: "email@email.com"
   });
   this.setState({users: users});
}
```

这里的关注点是，我们正在将新的用户添加到变量 `users` ，这里它对应的引用是 `this.state.users`。

**专业提示** ： 应该将 `React` 的状态视为不可变。我们不应该直接修改 `this.state`，因为 `setState()` 之后的调用可能会覆盖你之前的修改。

那么，如果我们直接修改 `state` 会产生什么问题呢？比方说，我们添加 `shouldComponentUpdate` ，并对比 `nextState` 和 `this.state` 来确保只有当数据改变时，才会重新渲染组件。

```
shouldComponentUpdate(nextProps, nextState) {
    if (this.state.users !== nextState.users) {
        return true;
    }
    return false;
}
```

即使用户的数组发生了改变，`React` 也不会重新渲染UI了，因为他们的引用是相同的。

避免此类问题最简单的方法，就是避免直接修改 `props` 和 `state`。所以，我们可以使用 `concat` 来重写 `addNewUser` 方法：

```javascript
addNewUser = () => {
   this.setState(state => ({
     users: state.users.concat({
       timeStamp: new Date(),
       userName: "robin",
       email: "email@email.com"
     })
   }));
};
```

为了处理 `React` 组件中 `props` 或者 `state` 的改变，我们可以考虑一下几种处理不可变的方法：

- 对于数组：使用 `[].concat` 或es6的 `[ ...params]`
- 对象：使用 `Object.assign({}, ...)` 或 es6的 `{...params}`

在向代码库引入不变性时，这两种方法有很长的路要走。

但是，最好使用一个提供不可变数据结构的优化库。以下是您可以使用的一些库：

- [Immutability Helper](https://github.com/kolodny/immutability-helper)：这是一个很好的库，他可以在不改变源的情况下，提供修改后的数据。
- [Immutable.js](https://facebook.github.io/immutable-js/) ：这是我最喜欢的库，因为它提供了许多持久不可变的数据，包括：[List](https://facebook.github.io/immutable-js/docs/#/List)，[Stack](https://facebook.github.io/immutable-js/docs/#/Stack)，[Map](https://facebook.github.io/immutable-js/docs/#/Map)，[OrderedMap](https://facebook.github.io/immutable-js/docs/#/OrderedMap)，[Set](https://facebook.github.io/immutable-js/docs/#/Set)，[OrderedSet](https://facebook.github.io/immutable-js/docs/#/OrderedSet) 和 [Record](https://facebook.github.io/immutable-js/docs/#/Record)。
- [Seamless-immutable](https://github.com/rtfeldman/seamless-immutable)：一个用于提供不可变 `JavaScript` 数据结构的库，他与普通的数组和对象向后兼容。
- [React-copy-write](https://github.com/aweary/react-copy-write)：一个不可变的React状态管理库，带有一个简单的可变API，memoized选择器和结构共享。

**专业提示**： React `setState` 方法是异步的。这意味着，`setState()` 方法会创建一个带转换的 `state`， 而不是立即修改 `this.state`。如果在调用`setState()` 方法之后去访问 `this.state` ，则可能会返回现有值。为防止这种情况，请`setState` 在调用完成后使用回调函数运行代码。

**其他资源：**

- [你真的需要不可变数据吗？](https://swizec.com/blog/immutable-data/swizec/7613)
- [不变性状态](https://medium.com/dailyjs/the-state-of-immutability-169d2cd11310)
- [使用React.js的不变性的优点和缺点](http://reactkungfu.com/2015/08/pros-and-cons-of-using-immutability-with-react-js/)
- [在React中处理状态：四种提供不变性的方法](https://medium.freecodecamp.org/handling-state-in-react-four-immutable-approaches-to-consider-d1f5c00249d5)

## 2.函数/无状态组件和 `React.PureComponent`

在 `React` 中，函数组件和 `PureComponent` 提供了两种不同级别的组件优化方案。

函数组件防止了构造类实例，
同时函数组件可以减少整体包的大小，因为它比类组件的的体积更小。

另一方面，为了优化UI更新，我们可以考虑将函数组件转换为 `PureComponent` 类组件（或使用自定义 `shouldComponentUpdate` 方法的类）。但是，如果组件不使用状态和其他生命周期方法，为了达到更快的的更新，首次渲染相比函数组件会更加复杂一些。

> 译注：函数组件也可以做纯组件的优化：React.memo(...) 是 React v16.6 中引入的新功能。 它与 React.PureComponent 类似，它有助于控制 函数组件 的重新渲染。 React.memo(...) 对应的是函数组件，React.PureComponent 对应的是类组件。React Hooks 也提供了许多处理这种情况的方法：useCallback, useMemo。推荐两个延伸阅读：
> [A Closer Look at React Memoize Hooks: useRef, useCallback, and useMemo](https://www.codebeast.dev/react-memoize-hooks-useRef-useCallback-useMemo/) ，[React Hooks API ⎯ 不只是 useState 或 useEffect](https://medium.com/@yanglin_68397/react-hooks-api-不只是-usestate-或-useeffect-57ebc46b3f61)

**我们应该何时使用 React.PureComponent？**

`React.PureComponent` 对状态变化进行浅层比较(shallow comparison)。这意味着它在比较时，会比较原始数据类型的值，并比较对象的引用。因此，我们必须确保在使用 `React.PureComponent` 时符合两个标准：

- 组件 `State` / `Props` 是一个不可变对象;
- `State` / `Props` 不应该有多级嵌套对象。

**专业提示：** 所有使用 `React.PureComponent` 的子组件，也应该是纯组件或函数组件。

## 3.生成多个块文件

> Multiple Chunk Files

您的应用程序始终以一些组件开始。您开始添加新功能和依赖项，最终您会得到一个巨大的生产文件。

您可以考虑通过利用 [CommonsChunkPlugin for webpack](https://webpack.js.org/plugins/commons-chunk-plugin/) 将供应商或第三方库代码与应用程序代码分开，生成两个单独的文件。你最终会得到 `vendor.bundle.js` 和 `app.bundle.js` 。通过拆分文件，您的浏览器会缓存较少的资源，同时并行下载资源以减少等待的加载时间。

**注意：** 如果您使用的是最新版本的webpack，还可以考虑使用 [SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)

### 4.在 Webpack 中使用 `Production` 标识生产环境

如果您使用 `webpack 4` 作为应用程序的模块捆绑程序，则可以考虑将 `mode` 选项设置为 `production` 。这基本上告诉 `webpack` 使用内置优化：

```javascript
module.exports = {
    mode: 'production'
};
```

或者，您可以将其作为 `CLI` 参数传递：

```
webpack --mode=production
```

这样做会限制对库的优化，例如缩小或删除开发代码。它不会公开源代码，文件路径[等等](https://medium.com/webpack/webpack-4-mode-and-optimization-5423a6bc597a)。

## 5.依赖优化

在考虑优化程序包大小的时候，检查您的依赖项中实际有多少代码被使用了，会很有价值。例如，如果您使用 `Moment.js` 会包含本地化文件的多语言支持。如果您不需要支持多种语言，那么您可以考虑使用 [moment-locales-webpack-plugin](https://www.npmjs.com/package/moment-locales-webpack-plugin) 来删除不需要的语言环境。

另一个例子是使用 `lodash` 。假设你使用了 100 多种方法的 20 种，那么你最终打包时其他额外的方法都是不需要的。因此，可以使用 [lodash-webpack-plugin](https://github.com/lodash/lodash-webpack-plugin) 来删除未使用的函数。

以下是一些使用 `Webpack` 打包时可选的[依赖项优化列表](https://github.com/GoogleChromeLabs/webpack-libs-optimizations)

### 6. `React.Fragments` 用于避免额外的 HTML 元素包裹

`React.fragments` 允许您在不添加额外节点的情况下对子列表进行分组。

```javascript
class Comments extends React.PureComponent{
    render() {
        return (
            <React.Fragment>
                <h1>Comment Title</h1>
                <p>comments</p>
                <p>comment time</p>
            </React.Fragment>
        );
    } 
}
```

等等！我们还可以使用更加简洁的语法代替 `React.fragments` ：

```javascript
class Comments extends React.PureComponent{
    render() {
        return (
            <>
                <h1>Comment Title</h1>
                <p>comments</p>
                <p>comment time</p>
            </>
        );
    } 
}
```

## 7.避免在渲染函数中使用内联函数定义

由于在 `JavaScript` 中函数就是对象（{} !== {}），因此当 `React` 进行差异检查时，内联函数将始终使 `prop diff` 失败。此外，如果在JSX属性中使用箭头函数，它将在每次渲染时创建新的函数实例。这可能会为垃圾收集器带来很多工作。

```javascript
default class CommentList extends React.Component {
    state = {
        comments: [],
        selectedCommentId: null
    }

    render(){
        const { comments } = this.state;
        return (
           comments.map((comment)=>{
               return <Comment onClick={(e)=>{
                    this.setState({selectedCommentId:comment.commentId})
               }} comment={comment} key={comment.id}/>
           }) 
        )
    }
}
```

您可以定义箭头函数，而不是为 `props` 定义内联函数。

```javascript
default class CommentList extends React.Component {
    state = {
        comments: [],
        selectedCommentId: null
    }

    onCommentClick = (commentId)=>{
        this.setState({selectedCommentId:commentId})
    }

    render(){
        const { comments } = this.state;
        return (
           comments.map((comment)=>{
               return <Comment onClick={this.onCommentClick} 
                comment={comment} key={comment.id}/>
           }) 
        )
    }
}
```

## 8. `JavaScript` 中事件的防抖和节流

**事件触发率**代表事件处理程序在给定时间内调用的次数。

通常，与滚动和鼠标悬停相比，鼠标点击具有较低的**事件触发率**。较高的事件触发率有时会使应用程序崩溃，但可以对其进行控制。

我们来讨论一些技巧。

首先，明确事件处理会带来一些昂贵的操作。例如，执行UI更新，处理大量数据或执行计算昂贵任务的XHR请求或DOM操作。在这些情况下，防抖和节流技术可以成为救世主，而不会对事件监听器进行任何更改。

### 节流

简而言之，节流意味着延迟功能执行。因此，不是立即执行事件处理程序/函数，而是在触发事件时添加几毫秒的延迟。例如，这可以在实现无限滚动时使用。您可以延迟 `XHR` 调用，而不是在用户滚动时获取下一个结果集。

另一个很好的例子是基于 `Ajax` 的即时搜索。您可能不希望每次按键时，都会请求服务器获取新的数据，因此最好节流直到输入字段处于休眠状态几毫秒之后，再请求数据。

节流可以通过多种方式实现。您可以限制触发的事件的次数或延迟正在执行的事件来限制程序执行一些昂贵的操作。

### 防抖

与节流不同，防抖是一种防止事件触发器过于频繁触发的技术。如果您正在使用 `lodash` ，则可以使用 `lodash’s debounce function` 来包装你的方法。

这是一个搜索评论的演示代码：

```javascript
import debouce from 'lodash.debounce';

class SearchComments extends React.Component {
 constructor(props) {
   super(props);
   this.state = { searchQuery: “” };
 }

 setSearchQuery = debounce(e => {
   this.setState({ searchQuery: e.target.value });

   // Fire API call or Comments manipulation on client end side
 }, 1000);

 render() {
   return (
     <div>
       <h1>Search Comments</h1>
       <input type="text" onChange={this.setSearchQuery} />
     </div>
   );
 }
}
```

如果您不使用 `lodash`，可以使用简单版的防抖函数。

```javascript
function debounce(a,b,c){var d,e;return function(){function h(){d=null,c||(e=a.apply(f,g))}varf=this,g=arguments;return clearTimeout(d),d=setTimeout(h,b),c&&!d&&(e=a.apply(f,g)),e}}
```

## 9.避免在 `map` 方法中使用 `Index` 作为组件的 `Key`

在渲染列表时，您经常会看到索引被用作键。

```
{
    comments.map((comment, index) => {
        <Comment 
            {..comment}
            key={index} />
    })
}
```

但是使用 `index`
作为 `key`， 被用在`React虚拟DOM元素`的时候，会使你的应用可能出现错误的数据 。当您从列表中添加或删除元素时，如果该 `key` 与以前相同，则 `React虚拟DOM元素`表示相同的组件。

始终建议使用唯一属性作为 `key`，或者如果您的数据没有任何唯一属性，那么您可以考虑使用`shortid module` 生成唯一 `key` 的属性。

```javascript
import shortid from  "shortid";
{
   comments.map((comment, index) => {
       <Comment 
           {..comment}
           key={shortid.generate()} />
   })
}
```

但是，如果数据具有唯一属性（例如ID），则最好使用该属性。

```javascript
{
   comments.map((comment, index) => {
       <Comment 
           {..comment}
           key={comment.id} />
   })
}
```

在某些情况下，将 `index` 用作 `key` 是完全可以的，但仅限于以下条件成立时：

- 列表和子元素是静态的
- 列表中的子元素没有ID，列表永远不会被重新排序或过滤
- 列表是不可变的

## 10.避免使用 `props` 来初始化 `state` （直接赋值）

我们经常需要将带有 `props` 的初始数据传递给 `React组件` 来设置初始状态值。

考虑一下这段代码：

```javascript
class EditPanelComponent extends Component {
    
    constructor(props){
        super(props);

        this.state ={
            isEditMode: false,
            applyCoupon: props.applyCoupon
        }
    }

    render(){
        return <div>
                    {this.state.applyCoupon && 
                    <>Enter Coupon: <Input/></>}
               </div>
    }
}
```

片段中的一切看起来都不错，对吧？

但是 `props.applyCoupon` 变化会发生什么？它会映射到 `state` 中嘛？ 如果在没有刷新组件的情况下，`props` 中的值被修改了，`props` 中的值，将永远不会分配给 `state` 中的 `applyCoupon`。这是因为构造函数仅在`EditPanel` 组件首次创建时被调用。

引用[React文档](https://react.docschina.org/docs/react-component.html#constructor)：

> 避免将 props 的值复制给 state！这是一个常见的错误：

```javascript
constructor(props) {
 super(props);
 // 不要这样做
 this.state = { color: props.color };
}
```

> 如此做毫无必要（你可以直接使用 this.props.color），同时还产生了 bug（更新 prop 中的 color 时，并不会影响 state）。**只有在你刻意忽略 `props` 更新的情况下使用。此时，应将 `props` 重命名为 `initialColor` 或 `defaultColor`。必要时，你可以修改它的 `key`，以强制“重置”其内部 `state`。请参阅博文：[你可能不需要派生 state](https://react.docschina.org/blog/2018/06/07/you-probably-dont-need-derived-state.html)

### 解决方法：

1. 直接使用 `props` 中的属性

```javascript
class EditPanelComponent extends Component {
    
    render(){
        return <div>{this.props.applyCoupon && 
         <>Enter Coupon:<Input/></>}</div>
    }
}
```

1. 当 `props` 改变时，您可以使用 `componentDidUpdate` 函数来更新 `state`。

```javascript
class EditPanelComponent extends Component {
    
    constructor(props){
        super(props);

        this.state ={
            applyCoupon: props.applyCoupon
        }
    }

    // reset state if the seeded prop is updated
    componentDidUpdate(prevProps){
        if (prevProps.applyCoupon !== this.props.applyCoupon) {
            this.setState({ applyCoupon: this.props.applyCoupon })
            // or do something
        }
    }

    render(){
        return <div>{this.state.applyCoupon && 
          <>Enter Coupon: <Input/></>}</div>
    }
}
```

## 11.在 DOM 元素上传递 Props

您应该避免将属性传播到 `DOM` 元素中，因为它会添加未知的 `HTML` 属性，这是不必要的，也是一种不好的做法。

```javascript
const CommentsText = props => {
    return (
      <div {...props}>
        {props.text}
      </div>
    );
};
```

您可以设置特定属性，而不是直接传递 Props：

```
const CommentsText = props => {
    return (
      <div specificAttr={props.specificAttr}>
        {props.text}
      </div>
    );
};
```

## 12.在使用 Redux Connect 时，同时使用 Reselect 来避免组件的频繁重新渲染

`Reselect` 是 `Redux` 的一个简单的选择器库，可用于构建记忆选择器。您可以将选择器定义为函数，为 `React` 组件检索 `Redux` 状态片段。

```javascript
const App = ({ comments, socialDetails }) => (
    <div>
      <CommentsContainer data={comments} />
      <ShareContainer socialDetails={socialDetails} />
    </div>
);
  
const addStaticPath = social => ({
    iconPath: `../../image/${social.iconPath}`
});
  
App = connect(state => {
    return {
        comments: state.comments,
        socialDetails: addStaticPath(state.social)
    };
})(App);
```

在这段代码中，每次评论数据在 `state` 中 变化时，`CommentsContainer` 和 `ShareContainer` 将会重新渲染。即使 `addStaticPath` 不进行任何数据更改也会发生这种情况，因为 `socialDetails` 由 `addStaticPath` 函数返回的的数据每次都是一个新的对象 （请记住{} != {}）。现在，如果我们用 `Reselect` 重写 `addStaticPath` ，问题就会消失，因为`Reselect` 将返回最后一个函数结果，直到它传递新的输入。

```javascript
import { createSelector } from "reselect";
const socialPathSelector = state => state.social;
const addStaticPath = createSelector(
  socialPathSelector,
  social => ({
    iconPath: `../../image/${social.iconPath}`
  })
);
```

### 14. 记忆化的 React 组件

[Memoization](https://en.wikipedia.org/wiki/Memoization)是一种用于优化程序速度的技术，主要通过存储复杂函数的计算结果，当再次出现相同的输入，直接从缓存中返回结果。`memoized` 函数通常更快，因为如果使用与前一个函数相同的值调用函数，则不会执行函数逻辑，而是从缓存中获取结果。

让我们考虑下面简单的无状态`UserDetails`组件。

```
const UserDetails = ({user, onEdit}) => {
    const {title, full_name, profile_img} = user;

    return (
        <div className="user-detail-wrapper">
            <img src={profile_img} />
            <h4>{full_name}</h4>
            <p>{title}</p>
        </div>
    )
}
```

在这里，所有的孩子 `UserDetails` 都基于 `props`。只要 `props` 发生变化，这个无状态组件就会重新渲染。如果 `UserDetails` 组件属性不太可能改变，那么它是使用组件的 `memoize` 版本的一个很好的选择：

```
const UserDetails = ({user, onEdit}) =>{
    const {title, full_name, profile_img} = user;

    return (
        <div className="user-detail-wrapper">
            <img src={profile_img} />
            <h4>{full_name}</h4>
            <p>{title}</p>
        </div>
    )
}

export default React.memo(UserDetails)
```

此方法将基于严格相等对组件的 `props` 和上下文进行浅层比较。

### 15.使用 CSS 动画代替 JS 动画

动画可以提供更加流畅优秀的用户体验。实现动画的方式有很多，一般来说，有三种方式可以创建动画：

- CSS transitions
- CSS animations
- JavaScript

我们选择哪一个取决于我们想要添加的动画类型。

#### 何时使用基于CSS的动画：

- 添加 “一次性” 的转换效果，比如切换 `UI` 元素的状态。
- 较小的自身包含状态的 UI 元素。例如，显示气泡提示，或者为菜单项增加悬停效果。

#### 何时使用基于JavaScript的动画：

- 当你想拥有高级效果时，例如弹跳，停止，暂停，倒带，减速或反转;
- 当你需要对动画进行重度控制时;
- 当你需要切换动画时，如鼠标悬停，点击等;
- 当使用 `requestAnimationFrame` 来变化视觉时。

例如，假设您希望 `div` 在鼠标悬停时分为 4 个状态设置动画。`div` 在旋转 90 度的过程中，四个阶段将背景颜色从红色变为蓝色，蓝色变为绿色，绿色变为黄色。在这种情况下，您需要结合使用**JavaScript动画**和**CSS转换**来更好地控制操作和状态更改。

### 16.使用CDN

CDN是一种将网站或移动应用程序中的静态内容更快速有效地传递给用户的绝佳方式。

CDN取决于用户的地理位置。最靠近用户的CDN服务器称为“边缘服务器”。当用户从您的网站请求通过CDN提供的内容时，他们会连接到边缘服务器并确保最佳的在线体验。

有一些很棒的CDN提供商。例如，CloudFront，CloudFlare，Akamai，MaxCDN，Google Cloud CDN等。

您也可以选择Netlify或Surge.sh在CDN上托管您的静态内容。Surge是一款免费的CLI工具，可将您的静态项目部署到生产质量的CDN。

### 17.在CPU扩展任务中使用 `Web Workers`

`Web Workers` 可以在Web应用程序的后台线程中运行脚本操作，与主执行线程分开。通过在单独的线程中执行费力的处理，主线程（通常是UI）能够在不被阻塞或减速的情况下运行。

在相同的执行上下文中，由于JavaScript是单线程的，我们需要并行计算。这可以通过两种方式实现。第一个选项是使用伪并行，它基于 `setTimeout` 函数实现的。第二种选择是使用 `Web Workers`。

`Web Workers` 在执行计算扩展操作时效果最佳，因为它在后台的单独线程中独立于其他脚本执行代码。这意味着它不会影响页面的性能。

我们可以利用`React`中的`Web Workers`来执行计算昂贵的任务。

这是不使用 `Web Workers` 的代码：

```javascript
// Sort Service for sort post by the number of comments 
function sort(posts) {
    for (let index = 0, len = posts.length - 1; index < len; index++) {
        for (let count = index+1; count < posts.length; count++) {
            if (posts[index].commentCount > posts[count].commentCount) {
                const temp = posts[index];
                posts[index] = users[count];
                posts[count] = temp;
            }
        }
    }
    return posts;
}

export default Posts extends React.Component{

    constructor(props){
        super(posts);
    }

    state = {
        posts: this.props.posts
    }

    doSortingByComment = () => {
        if(this.state.posts && this.state.posts.length){
            const sortedPosts = sort(this.state.posts);
            this.setState({
                posts: sortedPosts
            });
        }
    }

    render(){
        const posts = this.state.posts;
        return (
            <React.Fragment>
                <Button onClick={this.doSortingByComment}>
                    Sort By Comments
                </Button>
                <PostList posts={posts}></PostList>
            </React.Fragment>
        )
    }
}
```

当我们有 `20,000` 个帖子时会发生什么？由于 `sort` 方法时间复杂度 `O(n^2)` ，它将减慢渲染速度，因为它们在同一个线程中运行。

下面是修改后的代码，它使用 `Web Workers` 来处理排序：

```javascript
// sort.worker.js

// In-Place Sort function for sort post by number of comments
export default  function sort() {
    
    self.addEventListener('message', e =>{
        if (!e) return;
        let posts = e.data;
        
        for (let index = 0, len = posts.length - 1; index < len; index++) {
            for (let count = index+1; count < posts.length; count++) {
                if (posts[index].commentCount > posts[count].commentCount) {
                    const temp = posts[index];
                    posts[index] = users[count];
                    posts[count] = temp;
                }
            }
        }
        postMessage(posts);
    });
}

export default Posts extends React.Component{

    constructor(props){
        super(posts);
    }
    state = {
        posts: this.props.posts
    }
    componentDidMount() {
        this.worker = new Worker('sort.worker.js');
        
        this.worker.addEventListener('message', event => {
            const sortedPosts = event.data;
            this.setState({
                posts: sortedPosts
            })
        });
    }

    doSortingByComment = () => {
        if(this.state.posts && this.state.posts.length){
            this.worker.postMessage(this.state.posts);
        }
    }

    render(){
        const posts = this.state.posts;
        return (
            <React.Fragment>
                <Button onClick={this.doSortingByComment}>
                    Sort By Comments
                </Button>
                <PostList posts={posts}></PostList>
            </React.Fragment>
        )
    }
}
```

在这段代码中，我们sort在单独的线程中运行该方法，这将确保我们不会阻塞主线程。

您可以考虑使用 `Web Workers` 执行图像处理，排序，过滤和其他消耗高昂 CPU 性能的任务。

参考： [使用Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers)

## 18.虚拟化长列表

虚拟化列表或窗口化是一种在呈现长数据列表时提高性能的技术。此技术在任何时间内只展现列表的一部分，并且可以显著减少重新渲染组件所花费的时间，以及创建 DOM 节点的总数。

有一些流行的 `React` 库，如`react-window`和`react-virtualized`，它提供了几个可重用的组件来展示列表，网格和表格数据。

## 19.分析和优化您的 `Webpack` 打包

在生产部署之前，您应该检查并分析应用程序包以删除不需要的插件或模块。

您可以考虑使用`Webpack Bundle Analyzer`，它允许您使用可视化的树状结构来查看 `webpack` 输出文件的大小。

该模块将帮助您：

- 了解你的打包内容
- 找出最大尺寸的模块
- 找到哪些模块有错误
- 优化它！

最好的优点是什么？它支持压缩模块！他在解析他们以获得模块的真实大小，同时展示压缩大小！下面是使用 `webpack-bundle-analyzer` 的示例：

![19462-ksdqqn40a4.png](https://imgs.gnux.cn/usr/uploads/2019/07/996328578.png)

## 20.考虑服务端渲染

服务端渲染的好处之一是为用户提供更好的体验，相比客户端渲染，用户会更快接受到可查看的内容。

近年来，像沃尔玛和Airbnb会使用 `React` 服务端渲染来为用户提供更好的用户体验。然而，在服务器上呈现拥有大数据，密集型应用程序很快就会成为性能瓶颈。

服务器端渲染提供了性能优势和一致的SEO表现。现在，如果您在没有服务器端渲染的情况下检查React应用程序页面源，它将如下所示：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <link rel="shortcut icon" href="/favicon.ico">
    <title>React App</title>
  </head>
  <body>
    <div id="root"></div>
    <script src="/app.js"></script>
  </body>
</html>
```

浏览器还将获取`app.js`包含应用程序代码的包，并在一两秒后呈现整个页面。

![22987-2tk6bf7hx5i.png](https://imgs.gnux.cn/usr/uploads/2019/07/884849293.png)

我们可以看到客户端渲染，在到达服务器之前有两次往返，用户可以看到内容。现在，如果应用程序包含API驱动的数据呈现，那么流程中会有一个暂停。

让我们考虑用服务器端渲染来处理的同一个应用程序：

![97399-aadz09t46n6.png](https://imgs.gnux.cn/usr/uploads/2019/07/3001964305.png)

我们看到在用户获取内容之前，只有一次访问服务器。那么服务器究竟发生了什么？当浏览器请求页面时，服务器会在内存中加载`React`并获取呈现应用程序所需的数据。之后，服务器将生成的`HTML`发送到浏览器，立即向用户显示内容。

以下是一些为React应用程序提供SSR的流行解决方案：

- Next.js
- Gatsby

### 21.在Web服务器上启用Gzip压缩

`Gzip` 压缩允许 `Web` 服务器提供更小的文件大小，这意味着您的网站加载速度更快。`gzip` 运行良好的原因是因为`JavaScript`，`CSS`和`HTML`文件使用了大量具有大量空白的重复文本。由于`gzip`压缩常见字符串，因此可以将页面和样式表的大小减少多达`70％`，从而缩短网站的首次渲染时间。

如果您使用的是 `Node / Express` 后端，则可以使用 `Gzipping` 来解决这个问题。

```
const express = require('express');
const compression = require('compression');
const app = express();

// Pass `compression` as a middleware!
app.use(compression());
```

### 结论

有许多方法可以优化`React`应用程序，例如延迟加载组件，使用 `ServiceWorkers` 缓存应用程序状态，考虑`SSR`，避免不必要的渲染等等。也就是说，在考虑优化之前，值得了解`React`组件如何工作，理解 `diff` 算法，以及在`React` 中 `render` 的工作原理。这些都是优化应用程序时需要考虑的重要概念。

我认为没有测量的优化几乎都是为时过早的，这就是为什么我建议首先对性能进行基准测试和测量。您可以考虑使用 `Chrome`时间线分析和可视化组件。这使您可以查看卸载，装载，更新哪些组件以及它们相对于彼此的时间。它将帮助您开始性能优化之旅。

> 原文：[21 Performance Optimization Techniques for React Apps](https://www.codementor.io/blog/react-optimization-5wiwjnf9hj)
> 作者：Nishant
> 译者：[博轩](https://segmentfault.com/a/1190000019685362)

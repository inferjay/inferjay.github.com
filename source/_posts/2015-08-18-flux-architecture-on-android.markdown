---
layout: post
title: "Flux Architecture on Android"
date: 2015-08-18 22:23:42 +0800
comments: true
categories: Flux Architecture
---

找到一个好的 Android Application 架构并非易事。Google 似乎并不是非常关心这个，所以没有官方推荐模式之外的活动生命周期管理。

Finding a good architecture for Android applications is not easy. Google seems to not care much about it, so there is no official recommendation on patterns beyond Activities lifecycle management.

但是为你的应用定义一个架构是很重要的。不管你喜不喜欢，**每一个应用都应该有一个架构**。So you'd better be the one defining it than let it just emerge.

But defining an architecture for your application is important. Like it or not, **every application is going to have an architecture**. So you'd better be the one defining it than let it just emerge.

## Today: Clean Architecture

当前的趋势是适应 **[Clean Architecture][clean-architecture]**，在2012年Bob大叔为Web应用提出的建议。

Current trend is to adapt **[Clean Architecture][clean-architecture]**, a 2012 Uncle Bob proposal for web applications.

我发现在大多数 Android 应用中 Clean Architecture 都有点被**过渡设计**了。

I find Clean Architecture a little bit **over-engineered** for most of the Android apps out there.

典型的移动应用相比 Web 应用活的更短。移动App技术正在如此之快的不断发展，今天发布的任何应用程序会在十二个月之后被完全抛弃。

<!--more-->

Typically **mobile apps live shorter than web apps**. Mobile technology is evolving so fast that any app released today is going to be completely deprecated in twelve months. 

移动应用通常都做的很少。数据消耗的使用场景占有很高的比例。从 API 获取数据，然后显示给用户。读的多，写的少。

Mobile apps usually **do very little**. A very high percent of use cases are just for data consuming. Get data from API, show data to user. Lot of reads, very little writes. 

因此它的**业务逻辑并不复杂**。至少不会像后端应用程序那么复杂。那么你必须处理一些平台的问题：内存，存储，暂停，恢复，网络，位置等，但是这些并不是你的App的业务逻辑。在每一个App中都会有这些。

As a result its **business logic is not complex**. At least not as complex as backend apps. Well you have to deal with platform issues: memory, storage, pause, resume, network, location, etc. But that is not your app business logic. You have all of that in every app.

如此看来，大部分的App将不会从复杂分层或作业优先执行队列中获益。

So it seems that most of the apps out there will not benefit from things like  complex layer divisions or job priority execution queues.

他们可能只需要一个简单的方法来轻松地组织代码，高效地协同工作和更容易的找到Bug。

They may just need a **simple way to organise code, work together efficiently and find bugs easily**.

## Introducing Flux Architecture（Flux架构简介）

**[Flux Architecture][flux-arch]**被 Facebook 用来构建他们自己的客户端的 Web 应用。像 _Clean Architecture_ 它并不太适合移动应用，但是它的功能和简单非常适合应用在 Android Projects。

**[Flux Architecture][flux-arch]** is used by Facebook to build their client- side web applications. Like _Clean Architecture_ it is not intended for mobile apps, but its features and simplicity will allow us to adapt it very well to Android projects.

![image](/images/notes_images/flux-graph-simple.png)

理解 Flux 的两个关键特性：

* 数据流总是单向的。
	
	单向的数据流是 Flux Architecture 的核心，这也使得它非常容易学习。

* 应用被分成三个主要部分：

	-  **View**: 应用接口. 它创建响应用户交互的动作。
	- **Dispatcher**: 所有动作的控制中枢，它的职责是保证所有动作能够成功到达每个 Store。
	- **Store**: 维持应用在一个特定领域的状态。根据当前的状态作出相应的动作，执行业务逻辑以及当业务逻辑完成时发出 _change_ 事件。该事件用于更新其视图界面。
	
There are two **key features** to understand Flux:

* The data flow is always **unidirectional**.

	An [unidirectional data flow][unidirectional] is the **core** of the Flux architecture and is what makes it so easy to learn. 
It also provides great advantages when testing the application as discussed below.

* The application is divided into **three main parts**:

	-  **View**: Application interface. It create actions in response to user interactions.
	- **Dispatcher**: Central hub through which pass all actions and whose responsibility is to make them arrive to every Store.
	- **Store**: Maintain the state for a particular application domain. They respond to actions according to current state, execute business logic and emit a _change_ event when they are done. This event is used by the view to update its interface.

这三部分通过 **Actions** 进行通讯：这个 Aciton 包含了简单的原始对象，明确的类型以及与它关联的数据。

This three parts communicate through **Actions**: Simple plain objects, identified by a type, containing the data related to that action.


## Flux Android Architecture(Flux Android架构)

在开发 Android 应用过程中使用 Flux 原则的主要目标是构建一个在简单性和易扩展性和测试之间有着良好平衡的应用架构。

The main target of using Flux principles on Android development is to build an architecture with a good balance between simplicity and ease of scale and test. 

首先是把 **Flux 的元素跟 Android 应用的组建做一个映射**。

First step is to **map Flux elements with Android app components**.

下面这2个元素是很容易理解和实现的。

- **View**: Activity or Fragment
- **Dispatcher**: 一个事件总线. 在我的示例中我将使用 Otto，但是任何其他实现也是可以的。

Two of this elements are very easy to figure out and implement.

- **View**: Activity or Fragment
- **Dispatcher**: An event bus. I will use Otto in my examples but any other implementation should be fine. 

### Actions(动作)

Acitons 相对与其他两个元素也不是很复杂。它将被用以下2个主要属性实现为一个简单的 POJOs：

- Type: 一个 `String` 代表事件的类型.
- Data: 一个 `Map` 用来承载这个action.

Actions are not complex either. They will be implemented as simple POJOs with two main attributes:

- Type: a `String` identifying the type of event.
- Data: a `Map` with the payload for this action.

例如，一个典型的显示用户详情的 action，它看起来像是这样：

```java
Bundle data = new Bundle();
data.put("USER_ID", id);
Action action = new ViewAction("SHOW_USER", data);
```

For example, a typical action to show some User details will look like this:

```java
Bundle data = new Bundle();
data.put("USER_ID", id);
Action action = new ViewAction("SHOW_USER", data);
```

### Stores(商店)

这或许是 Flux 里最难理解的一个概念。

This is perhaps the **most difficult** to get Flux concept. 

此外，如果你之前有使用过 Clean Architecture，接受起来也不会感到不舒服的，因为 Stores 将承担先前分层结构中不同层次的责任。

Also if you have worked with Clean Architecture before it also will be uncomfortable to accept, because Stores will assume responsibilities that were previously separated into different layers.

Stores 包含应用的状态和业务逻辑。它类似 _rich data models_ 但是它可以管理**各种各样的对象**的状态，不仅仅如此。

Stores contain the **status of the application and its business logic**. They are similar to _rich data models_ but they can manage the status of **various objects**, not just one. 

Stores 通过 Dispatcher 来发出一个 Actions，执行业务逻辑以及代表结果的 _change_ 事件。

Stores **react to Actions emitted by the Dispatcher**, execute business logic and emit a change event as result.

Stores 仅仅输出这个单一事件： _change_ .任何其他组件感兴趣商店内部状态必须监听这个事件并使用它来得到所需要的数据。

Stores only output is this single event: _change_. Any other component interested in a Store internal status must listen to this event and use it to get the data it needs. 

没有其他系统组件需要知道任何与应用有关的状态。

No other component of the system should need to know anything about the status of the application.

最后，Stores 必须暴漏一个用来获取应用状态的接口。通过这种方式，视图元素可以询问 Stores 以及更新响应到应用UI。

Finally, stores must **expose an interface** to obtain application Status. This way, view elements can query the Stores and update application UI in response.

![image](/images/notes_images/flux-store.png)

例如，在一个酒馆发现应用的一个 SearchStore 将被用来记录搜索到的条目，搜索结果和搜索历史。在这个应用的 ReviewedStore 里将包含一个reviewed的酒馆列表和必要的业务逻辑，例如，按照review进行排序。

For example, in a Pub Discovery App a SearchStore will be used to keep track of searched item, search results and the history of past searches. In the same application a ReviewedStore will contain a list of reviewed pubs and the necessary logic to, for example, sort by review.

然后有一个重要的概念要牢记：**Stores 不是一个仓库**。他们的责任不是从外部数据源获取数据而是仅仅记录可以提供数据的actions。

However there is one important concept to keep in mind: **Stores are not Repositories**. Their responsibility is *not* to get data from an external source (API or DB) but only keep track of data provided by actions. 

那么Flus应用是怎么获取数据的呢？

So how Flux application obtain data?

### Network requests and asynchronous calls(网络请求和异步调用)

在最初的架构图中我故意跳过了这一部分：**网络调用**。下面的架构图在最初的架构图之上添加了更多的细节：

In the initial Flux graph I intentionally skipped one part: **network calls**. Next graph completes first one adding more details:

![image](/images/notes_images/flux-graph-complete.png)

异步的网络调用是从 **Actions Creator** 被触发的。一个Network Adapter使用异步调用相应的API并将结果返回给 Actions Creator。

Asynchronous network calls are triggered from an **Actions Creator**.
A Network Adapter makes the asynchronous call to the corresponding API and returns the result to the Actions Creator. 

最后Actions Creator分发相应类型的 Action 和返回的数据。

Finally the Actions Creator dispatch the corresponding typed Action with returned data.

所有的网络请求和异步调用都工作在 Stores之外有2个主要优点：

- **你的 Stores 完全是同步的**: 这使得 Stores 内部的业务逻辑非常容易理解。Bugs也非常容易定位。而且，由于**所有的状态改变都是同步的**，测试一个 Stores 也变成了一件非常容易的工作：运行 actions 并 断言 预期的最终状态。

- **所有的 actions 都从一个Action Creator 被触发**: 在同一个点上创建和运行所有用户 actions极大的降低了查找错误的困难。忘记在类里探究找出一个 action 是从哪里发出的吧。**一切都从这里开始**。而且，由于异步调用发出 _之前_ ，一切出自于 Action Creator 都是同步的。这是一个巨大的成功，这显著的提高了代码的可追溯性和可测试性。

Having all the network and asynchronous work out of the Stores has has **two main advantages**:

- **Your Stores are completely synchronous**: This makes the logic inside a Store very easy to follow. Bugs will be much easier to trace. And since **all state changes will be synchronous** testing a Store becomes an easy job: launch actions and assert expected final state.

- **All actions are triggered from an Action Creator**: Having a single point at which you create and launch all user actions greatly simplifies finding errors. 
Forget about digging into classes to find out where an action is originated. **Everything starts here**. And because asynchronous calls occur _before_, everything that comes out of ActionCreator is synchronous. This is a huge win that significantly improves traceability and testability of the code.

## Show me the code: To-Do App(展示我的代码：To-Do App)

[在这个例子中][android-app]你会发现一个典型的在Android里使用Flux架构实现的**To-Do App**。

[In this example][android-app] you will find a classical **To-Do App** implemented on Android using a Flux Architecture. 

我试图保持项目尽可能简单，只是为了展示这种架构如何能产生**非常良好的应用程序组织架构**。

关于这一实现的一些评论：

* `Dispatcher` 是使用 Otto 总线实现的。任何总线实现都可以工作。

* 有一个 `ActionsCreator` 类来帮助我们创建 `Actions` 并发布到 `Dispatcher`。这是一个在Flux里相当常见的让事情组织有序的模型。

* `Actions` 的类型是 `String` 常量。它可能不是最好的实现但是是最快速和帮助保持事情简单的实现。

Same thing with `Actions` data: 它们仅仅是一个用 `String` 作为键和 `Object` 作为值的 `HashMap`。这也迫使Stores构造和提取实际数据看起来很丑陋。当然，这也不是类型安全的，但再一次保持了例子可以很容易理解。

I tried to keep the project as simple as possible just to show how this architecture can produce **very well organised apps**. 

Some comments about implementation:

  * The `Dispatcher` is implemented using Otto Bus. Any bus implementation will mostly work. There is a **Flux restriction** on events I’m not applying here. On original Flux definition dispatching an event before previous one has finish is forbidden and will throw an exception. To keep the project simple I’m not implementing that here.

  * There is an `ActionsCreator` class to help creating `Actions` and posting them into the `Dispatcher`. It is a pretty common pattern in Flux which keeps things organised.
  
  *  `Actions` types are just `String` constants. It is probably not the best implementation but is quick and helps keeping things simple.
    
  Same thing with `Actions` data: they are just a `HashMap` with a `String` key and `Object` as a value. This forces ugly castings on Stores to extract actual data. Of course, this is not type safe but again, keeps the example easy to understand.

## Conclusion(总结)

不可能有最佳的Android应用程序架构。还有就是最好的架构是相对于当前的应用程序来说的。它是让你与你的队友轻松协作，保质保量的按时完成项目并且尽可能少的产生bugs。

There is **no such thing as the Best Architecture for an Android app**. 
There _is_ the Best Architecture for your current app. And it is the one that let you collaborate with your teammates easily, finish the project on time, with quality and as less bugs as possible.

我相信Flux对于所有这一切都是是非常好的。
I believe Flux is very good for all of that.


## Sample source code(示例代码)

**[https://github.com/lgvalle/android-flux-todo-app][android-app]**


## Further Reading(补充资料):
* [Facebook Flux Overview][flux-arch]
* [Testing Flux Applications](https://facebook.github.io/flux/docs/testing-flux-applications.html#content)
* [Flux architecture Step by Step](http://blogs.atlassian.com/2014/08/flux-architecture-step-by-step/)
* [Async Requests and Flux] (http://www.code-experience.com/async-requests-with-react-js-and-flux-revisited/)
* [Flux and Android](http://armueller.github.io/android/2015/03/29/flux-and-android.html)


[clean-architecture]: https://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html
[flux-arch]: https://facebook.github.io/flux/docs/overview.html
[unidirectional]: https://www.youtube.com/watch?v=i__969noyAM

[flux-graph-simple]: https://raw.githubusercontent.com/lgvalle/lgvalle.github.io/master/public/images/flux-graph-simple.png 

[flux-graph-complete]: https://raw.githubusercontent.com/lgvalle/lgvalle.github.io/master/public/images/flux-graph-complete.png

[flux-graph-store]: https://raw.githubusercontent.com/lgvalle/lgvalle.github.io/master/public/images/flux-store.png

[android-app]: https://github.com/lgvalle/android-flux-todo-app

## Thanks

特别感谢我的同事[Michele Bertoli](https://twitter.com/MicheleBertoli)花时间给我介绍 Flux 以及校对这篇文章。

Special thanks to my colleague [Michele Bertoli](https://twitter.com/MicheleBertoli) for taking the time to introduce me to Flux and for reviewing this post.

原文地址:[http://lgvalle.xyz/2015/08/04/flux-architecture/](http://lgvalle.xyz/2015/08/04/flux-architecture/)

关于本文

由于本人英语水平比较烂，翻译的时候有可能会曲解原作者的意思，建议英语好的大牛飘过此文去看原文，欢迎大家吐槽和拍砖，觉得译文中有那些地方我翻译的不妥的地方欢迎回复指正，我们相互学习～～～

----
版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)
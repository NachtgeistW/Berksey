---
title: 实体组件系统常见问题 / Entity Component System FAQ
draft: true
date: 2023-12-23 12:26:35
tags:
---


This FAQ is for anyone interested in ECS & modern, high performance game development. The goal is for answers to be short & correct, but not necessarily complete. The [Resources section](#Resources) contains more in-depth articles.

If you find anything missing or incorrect in the FAQ, feel free to create an issue or PR!

<!-- more -->

译者注：校对前我把老舍的《吃莲花的》读了三遍，才开始调整行文。若有任何翻译错误、语句不顺等处，恳请批评指正！

## 关于我
I'm the author of [Flecs](https://github.com/SanderMertens/flecs), an Entity Component System for C & C++. I'm always experimenting with better ways to implement ECS features, and write about it if I can. If you're interested in discussing ECS, [join the Discord](https://discord.gg/BEzP5Rgrrp)!

我是 Flecs 的作者。我一直在探索更好的方法以改善 ECS 功能，并尽力将其落地。如果您也想讨论 ECS，请[加入 Discord](https://discord.gg/BEzP5Rgrrp)！

## General Questions
- [关于我](#关于我)
- [General Questions](#general-questions)
- [How-to](#how-to)
- [Data Oriented Design Questions](#data-oriented-design-questions)
- [Glossary](#glossary)
- [ECS frameworks](#ecs-frameworks)
  - [Inactive projects](#inactive-projects)
- [Resources](#resources)
- [一般性问题](#一般性问题)
  - [什么是 ECS？](#什么是-ecs)
  - [什么才算是 ECS？](#什么才算是-ecs)
  - [为什么要使用 ECS？](#为什么要使用-ecs)
  - [谁在使用 ECS？](#谁在使用-ecs)
  - [ECS 与 OOP 的不同点在哪？](#ecs-与-oop-的不同点在哪)
  - [ECS 与实体-组件框架有何不同？](#ecs-与实体-组件框架有何不同)
  - [ECS 难学吗？](#ecs-难学吗)
  - [ECS 是更低级别的抽象吗？](#ecs-是更低级别的抽象吗)
  - [Does ECS require writing more code?](#does-ecs-require-writing-more-code)
  - [ECS 会增加代码编写量吗？](#ecs-会增加代码编写量吗)
  - [ECS 适用于低级代码吗？](#ecs-适用于低级代码吗)
  - [什么语言都能实现 ECS 吗？](#什么语言都能实现-ecs-吗)
  - [我要不要写一个自己的 ECS？](#我要不要写一个自己的-ecs)
  - [ECS 快吗？](#ecs-快吗)
  - [ECS 的代码是不是更方便复用？](#ecs-的代码是不是更方便复用)
  - [ECS 适用于多线程吗？](#ecs-适用于多线程吗)
  - [ECS 能用在游戏外吗？](#ecs-能用在游戏外吗)
  - [我要怎么开始使用 ECS？](#我要怎么开始使用-ecs)
  - [如何设计 ECS ？](#如何设计-ecs-)
  - [实现 ECS 的方法有哪些？](#实现-ecs-的方法有哪些)
    - [原型（又称“密集型 ECS”或“基于表的 ECS”）](#原型又称密集型-ecs或基于表的-ecs)
    - [稀疏集 ECS（又称“稀疏 ECS”）](#稀疏集-ecs又称稀疏-ecs)
    - [位集 ECS](#位集-ecs)
    - [响应式 ECS](#响应式-ecs)
  - [组件是如何修改的 ？](#组件是如何修改的-)
  - [实体是怎么与系统匹配的 ？](#实体是怎么与系统匹配的-)
  - [什么是实体关系 ？](#什么是实体关系-)
- [How-to](#how-to-1)
  - [How to create a hierarchy in ECS?](#how-to-create-a-hierarchy-in-ecs)
- [如何](#如何)
  - [如何在 ECS 里实现层次结构？](#如何在-ecs-里实现层次结构)
  - [How to store spatial data in ECS?](#how-to-store-spatial-data-in-ecs)
- [Data Oriented Design](#data-oriented-design)
  - [What is Data Oriented Design](#what-is-data-oriented-design)
  - [Is ECS the same as DoD?](#is-ecs-the-same-as-dod)
  - [What is Random Access Memory?](#what-is-random-access-memory)
  - [What is a CPU cache?](#what-is-a-cpu-cache)
  - [What is a cache line?](#what-is-a-cache-line)
  - [What is locality of reference?](#what-is-locality-of-reference)
  - [What is SIMD?](#what-is-simd)
  - [What is vectorization?](#what-is-vectorization)
  - [What is false sharing?](#what-is-false-sharing)
  - [What is AoS?](#what-is-aos)
  - [What is SoA?](#what-is-soa)
  - [What is branch prediction?](#what-is-branch-prediction)
- [术语表](#术语表)
  - [Entity](#entity)
  - [Component](#component)
  - [Tag](#tag)
  - [System](#system)
  - [Query](#query)
  - [World](#world)
  - [Registry](#registry)
  - [Archetype](#archetype)
  - [Table](#table)
  - [Sparse set](#sparse-set)

## How-to
- [How to create a hierarchy in ECS?](#how-to-create-a-hierarchy-in-ecs)
- [How to store spatial data in ECS?](#how-to-store-spatial-data-in-ecs)

## Data Oriented Design Questions
- [What is Data Oriented Design?](#what-is-data-oriented-design)
- [Is ECS the same as DoD?](#is-ecs-the-same-as-dod)
- [What is Random Access Memory?](#what-is-random-access-memory)
- [What is a CPU cache?](#what-is-a-cpu-cache)
- [What is a cache line](#what-is-a-cache-line)
- [What is locality of reference?](#what-is-locality-of-reference)
- [What is SIMD?](#what-is-simd)
- [What is vectorization?](#what-is-vectorization)
- [What is false sharing?](#what-is-false-sharing)
- [What is AoS?](#what-is-aos)
- [What is SoA?](#what-is-soa)
- [What is branch prediction?](#what-is-branch-prediction)

## Glossary
- [Entity](#entity)
- [Component](#component)
- [Tag](#tag)
- [System](#system)
- [Query](#query)
- [World](#world)
- [Registry](#registry)
- [Table](#table)
- [Archetype](#archetype)

## ECS frameworks
The current list includes both open and closed source ECS implementations, and engines that have adopted ECS pattern. Projects that had no activity in the past year are not included.

- [AFRAME](https://aframe.io) (HTML 5/JS, MIT)
- [Arch](https://github.com/genaray/Arch) (C#, Apache)
- [Arche](https://github.com/mlange-42/arche) (Go, MIT)
- [Bevy ECS](https://github.com/bevyengine/bevy) (Rust, MIT)
- [Dominion](https://github.com/dominion-dev/dominion-ecs-java) (Java, MIT)
- [Ecsact](https://ecsact.dev) (MIT)
- [EntityX](https://github.com/alecthomas/entityx) (C++11, MIT)
- [Entitas](https://github.com/sschmid/Entitas-CSharp) (C#, MIT)
- [EnTT](https://github.com/skypjack/entt) (C++17, MIT)
- [Esper](https://github.com/benmoran56/esper) (Python, MIT)
- [Fireblade](https://github.com/fireblade-engine/ecs) (Swift, MIT)
- [Flecs](https://github.com/SanderMertens/flecs) (C/C++11, [C#](https://github.com/flecs-hub/flecs-cs), [Rust](https://github.com/jazzay/flecs-rs), [Zig](https://github.com/prime31/zig-flecs), [Lua](https://github.com/flecs-hub/flecs-lua), MIT)
- [Fleks](https://github.com/Quillraven/Fleks) (Kotlin Multiplatform, MIT)
- [Hecs](https://github.com/Ralith/hecs) (Rust, Apache/MIT)
- [LeoEcsLite](https://github.com/Leopotam/ecslite) (C#, MIT)
- [Mach ECS](https://github.com/hexops/mach) (Zig, MIT, Apache)
- [Nu](https://github.com/bryanedds/Nu) (F#, MIT)
- [Photon Quantum](https://doc.photonengine.com/en-us/quantum/current/getting-started/quantum-intro) (C#, Commercial license)
- [Apple RealityKit](https://developer.apple.com/documentation/realitykit) (Swift, Commercial license)
- [Shipyard](https://github.com/leudz/shipyard) (Rust, Apache/MIT)
- [Specs](https://github.com/amethyst/specs) (Rust, Apache/MIT)
- [Svelto](https://github.com/sebas77/Svelto.ECS) (C#, MIT)
- [Unity DOTS](https://unity.com/dots) (C#, Commercial license)
- [Unreal Mass](https://docs.unrealengine.com/5.0/en-US/overview-of-mass-entity-in-unreal-engine/) (C++, Commercial license)
- [Zig ECS](https://github.com/prime31/zig-ecs) (Zig, MIT)
- [luaecs](https://github.com/cloudwu/luaecs) (C/Lua, MIT)
- [Morpeh](https://github.com/scellecs/morpeh) (C#, MIT)
- [ME.ECS](https://github.com/chromealex/ecs) (C#, MIT)
- [DefaultEcs](https://github.com/Doraku/DefaultEcs) (C#, MIT-0)

### Inactive projects
- [Artemis](https://github.com/junkdog/artemis-odb) (Java, custom license)
- [Entitas-Redux](https://github.com/jeffcampbellmakesgames/Entitas-Redux) (C#, MIT)
- [Legion](https://github.com/amethyst/legion) (Rust, MIT)
- [tiny-ecs](https://github.com/bakpakin/tiny-ecs) (Lua, MIT)
- [LeoECS](https://github.com/Leopotam/ecs) (C#, MIT)

## Resources
- [Taking the Entity-Component-System Architecture Seriously](https://www.youtube.com/watch?v=VpiprNBEZsk) (@alice-i-cecile, Youtube)
- [Overwatch Gameplay Architecture and Netcode](https://www.youtube.com/watch?v=W3aieHjyNvw) (Blizzard, GDC)
- [Data-Oriented Design and C++](https://www.youtube.com/watch?v=rX0ItVEVjHc) (Mike Acton, CppCon)
- [CPU caches and why you care](https://vimeo.com/97337258) (Scott Meyers, NDC)
- [Building a fast ECS on top of a slow ECS](https://youtu.be/71RSWVyOMEY) (UnitOfTime, Youtube)
- [Culling the Battlefield: Data Oriented Design in Practice](https://www.gdcvault.com/play/1014491/Culling-the-Battlefield-Data-Oriented) (DICE, GDC)
- [Interactive app for browsing systems of Cities Skylines 2](https://captain-of-coit.github.io/cs2-ecs-explorer/#) (Captain-Of-Coit)
- [Game Engine Entity/Object systems](https://www.youtube.com/watch?v=jjEsB611kxs) (Bobby Anguelov, presentation)
- [Understanding Data Oriented Design for Entity Component Systems](https://www.youtube.com/watch?v=0_Byw9UMn9g) (Unity, GDC)
- [Understanding Data Oriented Design](https://learn.unity.com/tutorial/part-1-understand-data-oriented-design?courseId=60132919edbc2a56f9d439c3&signup=true&uv=2020.1) (Unity, Tutorial)
- [Data Oriented Design](https://www.dataorienteddesign.com/dodbook/dodmain.html) (Richard Fabian, book)
- [Entities, Components and Systems](https://medium.com/ingeniouslysimple/entities-components-and-systems-89c31464240d) (Mark Jordan, blog)
- [Awesome Entity Component System](https://github.com/jslee02/awesome-entity-component-system) (Jeongseok Lee, link collection)
- [Why Vanilla ECS is not enough](https://ajmmertens.medium.com/why-vanilla-ecs-is-not-enough-d7ed4e3bebe5) (Sander Mertens, blog)
- [Formalisation of Concepts behind ECS and Entitas](https://medium.com/@icex33/formalisation-of-concepts-behind-ecs-and-entitas-8efe535d9516) (Maxim Zaks, blog)
- [Entity Component System and Rendering](https://ourmachinery.com/post/ecs-and-rendering/) (Our Machinery, blog)
- [Specs and Legion, two very different approaches to ECS](https://csherratt.github.io/blog/posts/specs-and-legion/) (Cora Sherrat, blog)
- [Where are my Entities and Components](https://ajmmertens.medium.com/building-an-ecs-1-where-are-my-entities-and-components-63d07c7da742) (Sander Mertens, blog)
- [Archetypes and Vectorization](https://medium.com/@ajmmertens/building-an-ecs-2-archetypes-and-vectorization-fe21690805f9) (Sander Mertens, blog)
- [Building Games with Entity Relationships](https://ajmmertens.medium.com/building-games-in-ecs-with-entity-relationships-657275ba2c6c) (Sander Mertens, blog)
- [Why it is time to start thinking of games as databases](https://ajmmertens.medium.com/why-it-is-time-to-start-thinking-of-games-as-databases-e7971da33ac3) (Sander Mertens, blog)
- [A Roadmap to Entity Relationships](https://ajmmertens.medium.com/a-roadmap-to-entity-relationships-5b1d11ebb4eb) (Sander Mertens, blog)
- [Making the most of Entity Identifiers](https://ajmmertens.medium.com/doing-a-lot-with-a-little-ecs-identifiers-25a72bd2647) (Sander Mertens, blog)
- [Why Storing State Machines in ECS is a Bad Idea](https://ajmmertens.medium.com/why-storing-state-machines-in-ecs-is-a-bad-idea-742de7a18e59) (Sander Mertens, blog)
- [ECS back & forth](https://skypjack.github.io/2019-02-14-ecs-baf-part-1/) (Michele Caini, blog series)
- [Sparse Set](https://www.geeksforgeeks.org/sparse-set/) (Geeks for Geeks)
- [Hibitset](https://docs.rs/hibitset/0.6.3/hibitset/) (DOCS. RS)

## 一般性问题

### 什么是 ECS？

ECS（"Entity Component System"）描述了一种设计方法，它分离数据和行为，以促进代码复用性。数据通常存储成缓存友好的方式，从而提高性能。ECS 具有以下特征：

- 它有实体（entity），这些实体是唯一的标识符。
- 它有组件（component），这些是没有行为的纯数据类型。
- 实体可以包含零个或多个组件。
- 实体可以动态更改组件。
- 它有系统（system），这些系统是与具有某个组件集的实体相匹配的函数。

ECS 设计模式通常需要通过框架来实现。术语 "Entity Component System" 通常用于指示该设计模式的特定实现。

### 什么才算是 ECS？

根据上一个问题中的定义，对 ECS 最严格的解释是指具有实体、组件和系统的东西。

在实际应用中，ECS 的使用较为宽松。有些 ECS 框架可能没有系统，仅提供查询实体的方法。其他框架可能允许向实体添加非组件的东西。尽管有这些差异，许多人仍然认为这些实现是 ECS。

如果一个框架允许你向实体添加“东西”，而且想查询实体拥有某些东西但没有其他东西时，它也有提供一种方法，那通常认为它是 ECS。

### 为什么要使用 ECS？

ECS 在游戏开发者中日益流行的原因有下列几点：

- ECS 通常能支持更多的游戏对象。
- ECS 代码往往更具复用性。
- ECS 代码更容易扩展新功能。
- ECS 允许更动态的代码风格。

### 谁在使用 ECS？

许多商业项目和引擎已经用上了 ECS，或是以前用过。如果你知道其他使用 ECS 的项目，请告诉我！

- [Unity DOTS](https://unity.com/dots) (Engine)
- [Unreal (Sequencer)](https://www.unrealengine.com/en-US/tech-blog/performance-at-scale-sequencer-in-unreal-engine-4-26) (Engine)
- [Our Machinery](https://ourmachinery.com/) (Engine)
- [Photon Quantum](https://doc.photonengine.com/en-us/quantum/current/getting-started/quantum-intro) (Engine)
- [Bevy](https://bevyengine.org/) (Engine)
- [Amethyst](https://amethyst.rs/) (Engine)
- [Overwatch](https://playoverwatch.com/en-us/) (Game, uses custom ECS)
- [Xenonauts](https://store.steampowered.com/app/223830/Xenonauts/) (Game, uses custom ECS)
- [Survival City](https://play.google.com/store/apps/details?id=com.playstack.survivalcity&hl=en&gl=US) (Game, uses Entitas)
- [Penko Park](https://store.steampowered.com/app/852090/Penko_Park) (Game, uses Entitas)
- [Doomsday Vault](https://apps.apple.com/us/app/doomsday-vault/id1457773760) (Game, uses Entitas)
- [Phantom Brigade](https://www.youtube.com/watch?v=uHsh9kWQeDc&ab_channel=BraceYourselfGames) (Game, uses Entitas)
- [Minecraft Bedrock](https://minecraft.gamepedia.com/Bedrock_Edition) (Game, uses EnTT)
- [War of Rights](https://store.steampowered.com/app/424030/War_of_Rights/) (Game, uses EnTT)
- [ArcGIS](https://developers.arcgis.com/arcgis-runtime/) (Framework, uses EnTT)
- [Aether Engine](https://hadean.com/spatial-simulation/) (Framework, uses EnTT)
- [FastSuite](https://www.fastsuite.com/) (Simulation, uses EnTT)
- [RoboCraft](https://robocraftgame.com/) (Game, uses Svelto)
- [GameCraft](https://store.steampowered.com/app/1078000/Gamecraft/) (Game, uses Svelto)
- [CardLife](http://cardlifegame.com/) (Game, uses Svelto)
- [Tempest Rising](https://store.steampowered.com/app/1486920/Tempest_Rising/) (Game, uses Flecs)
- [Territory Control](https://store.steampowered.com/app/690290/Territory_Control_2/) (Game, uses Flecs)
- [The Forge](https://github.com/ConfettiFX/The-Forge) (Rendering Engine, uses Flecs)
- [Bebylon](https://bebylon.world/) (Game, uses Flecs)
- [Sol Survivor](https://nicok.itch.io/sol-survivor-demo) (Game, uses Flecs)

### ECS 与 OOP 的不同点在哪？

ECS 通常描述成面向对象编程（OOP）的一种替代方法。尽管 ECS 和 OOP 有重合之处，但二者的确有影响到应用的设计方式的差异：

- 在 OOP 中，继承是一等公民，而在 ECS 中，组合是一等公民。
- OOP 鼓励封装数据，而 ECS 鼓励暴露 POD 对象。
- OOP 将数据与行为放在一起，而 ECS 将数据从行为分离。
- 在 OOP 中，对象实例是单一静态类型的，而在 ECS 中，实体可以拥有多个、动态变化的组件。

值得注意的是，有人认为 ECS 符合*面向对象设计*的特征（请参阅[本文](https://www.gamedev.net/blogs/entry/2265481-oop-is-dead-long-live-oop/)），因此应该被视为其子集。

然而，在实践中，ECS 应用的设计过程与大多数人认识的 OOP 设计过程有显著差异。因此，将其视为一种独立的设计方法，起码还是有用的。

### ECS 与实体-组件框架有何不同？

ECS 和实体-组件框架（Entity-Component，EC）不一样。这点经常把人搞懵。一般来说，游戏引擎里找得到的 EC 框架基本都与 ECS 类似，都允许创建实体和组合组件。然而，在 EC 框架中，组件是包含数据和行为的类，行为直接在组件上执行。

一个简单的 EC 框架可能长这样：

```cpp
class IComponent {
public:
    virtual void update() = 0;
};

class Entity {
    vector<IComponent*> components;
public:
    void addComponent(IComponent *component);
    void removeComponent(IComponent *component);
    void updateComponents();
};
```

在 EC 框架中构建功能通常意味着继承 `IComponent` 接口，并从多个组件组合实体。Unity 的 `GameObject` 系统就是 EC 在实践中的一个例子。

### ECS 难学吗？

ECS 的概念和规则相对较少，一般比较好学。但是，需要多加练习才能正确应用这些概念和规则。ECS 设计的某些方面与直觉相悖，尤其是在 OOP 背景下。

不过也有人说一旦跟 ECS 对上电波后，编写、复用和扩展代码都会变得更简单。

### ECS 是更低级别的抽象吗？

不一定。虽然一些 ECS 设计可以更充分地利用低级的机器，但为 ECS 编写的代码不一定比其他方法更低级或更高级。

### Does ECS require writing more code?
There is not a single answer to this, and highly depends on the ECS framework and engine that is used. 

When an ECS framework is integrated with an engine, it can result in pretty compact and concise code that can be even shorter than non-ECS alternatives. Examples of engines with integrated ECS are Bevy, Amethyst and Our Machinery.

When ECS is not integrated with an engine, the additional glue-code to bridge between the native engine types and the ECS can cause an application to have to write more code.

Having said that, the time spent on writing ECS code is offset by time savings as the result of a more maintainable code base.

### ECS 会增加代码编写量吗？

不一定，取决于所使用的 ECS 框架和引擎。

当 ECS 框架集成到引擎里时，写出来的代码可能相当简短又简练，甚至可能比非 ECS 的替代方案还短。Bevy、Amethyst 和 Our Machinery 是一些 ECS 与引擎集成的例子。

而当 ECS 未集成到引擎里时，需要额外写胶水代码来桥接原生引擎类型和 ECS，也就是说可能需要为应用程序写更多代码。

话虽如此，花费在编写 ECS 代码上的时间会省回来，因为代码库会更容易维护。

### ECS 适用于低级代码吗？

对于低级引擎代码，如渲染和物理，可能希望利用底层硬件的高级特性——比如矢量化——的同时优化缓存局部性。一些 ECS 框架相对于其他框架更适合这种需求。

一般来说，当一个 ECS 框架提供对原始组件数组的访问时，它更适合进行低级优化。另一个决定性因素是系统多线程化的难易程度，这点在现代游戏中尤为重要。

### 什么语言都能实现 ECS 吗？

都能。

### 我要不要写一个自己的 ECS？

构建一个功能齐全的 ECS 并不难，毕竟概念和规则都只有三言两语。建一个自己的 ECS 有许多好处，比如说你可以自由添加新功能，还能仅构建实际需要的功能。

但是，自己去写实现的话，应该提前打好“它可能比不过已有的实现”的预防针。随着时间的发展，早有许多技巧发明了出来，以在 ECS 操作中提供平衡的性能。要保持对新发展的掌握，需要不断地学习、尝试和迭代。

学会编写一个 ECS 很容易，但难以精通，跟很多事情都一样。

### ECS 快吗？

通常是对的，虽然这肯定会取决于你在测什么和 ECS 的实现。不同的实现会做出不同的权衡，因此，在一个框架中非常快的操作，换到另一个框架可能就相当慢了。

ECS 实现一般擅长以线性查询和遍历实体集，或是在运行时动态更改组件。它们通常不擅长需要高度专业化数据结构的查询或操作，比如二叉树或空间结构。了解一个实现的权衡并充分利用其设计，可以确保从 ECS 中获得最佳的性能。

### ECS 的代码是不是更方便复用？

没错。原因在于行为在 ECS 中是与一组组件相匹配的，而不像 OOP，行为与类紧密耦合。一些影响随之而来。

第一个显而易见。因为行为不与单个类绑定，所以它可以在不同类的实体之间重复使用。典例是一个"Move" 系统，能匹配任何具有 "Position" 和 "Velocity" 组件的实体。

其他更像 OOP 风格的设计中也可以实现这种功能，但这通常依赖于基于类的继承。继承的问题众所周知，比如难以重构类层次结构，或是底层基类随时间推移积累废弃代码之类。

然而，EC 框架也可以提供类似的可重用性水平，只需把组件添加到游戏实体中即可。 (参见 [ECS 与实体-组件框架有何不同](#how-is-ecs-different-from-entity-component-frameworks))。

ECS 在这里的巨大优势是，可以在开发的任何阶段引入新的系统，且无论旧的新的实体，只要拥有正确的组件，系统就会自动与之匹配。这就促进了设计：把系统开发成单一责任的小型功能单元，进而轻松地部署到不同项目中。

### ECS 适用于多线程吗？

一般来说是的。数据和行为的分离使得识别个体系统、它们的依赖关系以及调度方式更为容易。因 ECS 实现不同，多线程的处理方法可能有所不同，但大多数都会让多线程代码更容易实现。

### ECS 能用在游戏外吗？

可以。它可以用于（也已用于）游戏外的项目了。

### 我要怎么开始使用 ECS？

我特别推荐去读跟 ECS 有关的已有资源，并去试一下里边描述的方法。阅读示例 ECS 项目的代码也是快速了解 ECS 应用程序编写方式的好方法。

### 如何设计 ECS ？

设计一个 ECS 应用程序始于创建包含游戏数据的组件（数据结构）。需要考虑的重要事项包括：

- 数据的实体数量
- 数据被访问的频率
- 数据发生变化的频率
- 什么时候需要访问/更改数据
- 哪些数据一起被访问/更改
- 数据的基数是多少

设计单一责任的组件和系统是良好的实践。这使得它们更容易在项目之间重用，并使得代码重构更加容易。

### 实现 ECS 的方法有哪些？

实现 ECS 的方式多种多样，每种都各有权衡。这个不完全列表列举了一些比较流行的实现：

#### 原型（又称“密集型 ECS”或“基于表的 ECS”）

原型 ECS （Archetypes ECS）将实体存储在表里，其中组件是列，实体是行。原型实现可以快速查找、遍历。

原型实现的例子有 [Flecs](https://github.com/SanderMertens/flecs)、[Our Machinery](https://ourmachinery.com/)、[Unity DOTS](https://unity.com/dots)、[Unreal Sequencer](https://www.unrealengine.com/en-US/tech-blog/performance-at-scale-sequencer-in-unreal-engine-4-26)、[Unreal Mass](https://docs.unrealengine.com/5.0/en-US/overview-of-mass-entity-in-unreal-engine/)、[Bevy ECS](https://bevyengine.org/)、[Legion](https://github.com/amethyst/legion) 和 [Hecs](https://github.com/Ralith/hecs)。

#### 稀疏集 ECS（又称“稀疏 ECS”）

基于稀疏集的 ECS（Sparse set ECS）将每个组件存储在自己的稀疏集中，实体 ID 是稀疏集的键。稀疏集实现可以快速添加/删除。

稀疏集实现的例子有 [EnTT](https://github.com/skypjack/entt) 和 [Shipyard](https://github.com/leudz/shipyard)。

#### 位集 ECS

基于位集的 ECS（Bitset based ECS）将组件存储在数组中，将实体 ID 用作索引，并使用一个位集表明实体是否具有特定组件。基于位集的实现有多种版本。一种方法是让每个组件都持有一个数组，外加一个位集来指示哪些实体拥有该组件。另一种方法是使用 [hibitset](#hibitset) 数据结构（见链接）。

位集实现的例子有 [EntityX](https://github.com/alecthomas/entityx) 和 [Specs](https://github.com/amethyst/specs)。

#### 响应式 ECS

响应式 ECS（reactive ECS）使用由实体变化产生的信号来追踪哪些实体与系统/查询匹配。

响应式 ECS 的例子是 [Entitas](https://github.com/sschmid/Entitas-CSharp)。

### 组件是如何修改的 ？

通常，ECS 允许以两种方式修改组件，一种是修改单个实体上的组件，另一种是通过系统修改许多实体的组件值。

第一种方法的示例：

```cpp
entity. Set<Position>({10, 20});
```

第二种方法的示例：

```cpp
system<Position, Velocity>(). Each (
    [](entity e, Position& p, Velocity & v) {
        p.x += v.x;
        p.y += v.y;
    });
```

第二种方法通常更快，因为它查找数更少，还能从高效的组件存储方法中受益。

### 实体是怎么与系统匹配的 ？

有三种常用的实现方法。

1. 在原型 ECS 中，查询会存储一个匹配表的列表（一个表可以包含许多实体）。这种方法的优点是，由于表很快就会稳定下来，查询评估开销平均会降至零。
2. 在稀疏集 ECS 中，查询会遍历所查询的一个组件（通常是实体最少的组件）中的所有实体，并测试每个后续组件中是否有实体。位集 ECS 也使用类似的方法。
3. 在响应式 ECS 中，系统通过监听可能让实体匹配的信号，收集拥有正确组件集的实体。

### 什么是实体关系 ？

实体关系（entity relationships）是 ECS 模型的扩展，除了添加组件外，还可以将一对事物添加到一个实体中。一个简单的关系示例可能长这样：

```c
alice.Add<Likes>(bob);
```

在这个例子中，"likes, bob" 是一对关系，"Likes" 是关系种类，"bob" 是关系目标。"Alice" 和 "bob" 都是普通实体。有关实体关系的更多信息，请参阅[本文](https://ajmmertens.medium.com/building-games-in-ecs-with-entity-relationships-657275ba2c6c)。

## How-to

### How to create a hierarchy in ECS?
There are several ways to implement a hierarchy in ECS, and it depends on an ECS implementation which ones are available to an application. One approach that works in any implementation is to store the hierarchy in components like so:

```c
// Store the parent entity on child entities
Struct Parent {
    Entity parent;
};

// Store all children of a parent in a component with a vector
Struct Children {
    vector<entity> children;
};

// Store children in linked list
Struct ChildList {
    Entity first_child; // First child of entity
    Entity prev_sibling; // Previous sibling
    Entity next_sibling; // Next sibling
};
```

The disadvantage of this approach is that it relies on component lookups, which can slow down systems that iterate a hierarchy. While flexible, this approach is not ideal for low-level systems, such as applying transforms.

An approach that works especially well if an application just needs to iterate a hierarchy top-down is to sort entities based on their depth in the hierarchy. This has as advantage that it is fast to iterate. A disadvantage is that it requires frequent sorting.

Archetype ECS frameworks may allow splitting up subtrees across different tables. Tables/subtrees can be sorted according to their depth. This has as advantage that iteration is fast, and that sorting is infrequent. The disadvantage of this approach is that it can create a lot of small tables, which can degrade performance.

## 如何

### 如何在 ECS 里实现层次结构？

在 ECS 中实现层次结构有好几种方法，应用程序使用哪种方法取决于 ECS 的实现。一种方法是在组件中存储层次结构，它在任何实现中都适用，如下所示：

```c
// Store the parent entity on child entities
Struct Parent {
    Entity parent;
};

// Store all children of a parent in a component with a vector
Struct Children {
    vector<entity> children;
};

// Store children in linked list
Struct ChildList {
    Entity first_child; // First child of entity
    Entity prev_sibling; // Previous sibling
    Entity next_sibling; // Next sibling
};
```

这种方法的缺点是依赖于组件查找，这会降低遍历层次结构的系统的运行速度。尽管它很灵活，但对于低级系统（比如应用变换）来说并不是最优解。

如果应用只需要自上而下地遍历层次结构的话，有一种方法特别有效，就是根据实体在层次结构中的深度进行排序。这种方法的优点是迭代速度快。缺点是需要频繁排序。

原型 ECS 框架允许在不同的表中拆分子树。表/子树可以根据其深度进行排序。这种方法的优点是迭代速度快，排序不频繁。缺点是会产生大量小表，从而降低性能。

### How to store spatial data in ECS?
Spatial data structures like quadtrees and octrees are usually not directly stored in an ECS, as their layout does not match well with the typical ECS layout.

One approach that works well for narrow-phase spatial queries in combination with an ECS is to create a query that iterates relevant entities and stores them in a spatial structure at the beginning (or end) of each frame.

For broad-phase spatial queries an application could leverage runtime tags (if the ECS supports it) where a tag corresponds with a cell in a spatial grid. Combined with queries that match the tag, an application can quickly discard large groups of entities that are not in a certain area.

如何在 ECS 中存储空间数据？
空间数据结构（四叉树和八叉树等）一般不能直接存储在 ECS 中，因为它们的布局与典型的 ECS 布局不匹配。

有一种方法能与 ECS 有效结合，做精细检测的空间查询：创建一个查询，迭代相关实体，并在每帧开始（或结束）时将其存储在空间结构中。

对于粗略检测的空间查询，应用程序可以利用运行时标签（如果 ECS 支持的话），其中标签与空间网格中的单元格相对应。结合与标签相匹配的查询，应用程序可以快速丢弃特定区域外的大组实体。
## Data Oriented Design

### What is Data Oriented Design
Wikipedia defines Data Oriented Design as:

> ... A program optimization approach motivated by efficient usage of the CPU cache, used in video game development. The approach is to focus on the data layout, separating and sorting fields according to when they are needed, and to think about transformations of data.

Data oriented design is an umbrella term for a large collections of techniques and conditions under which those techniques should be used. In general the goal is to analyse the access patterns of the different kinds of data in an application, and select data structures that _for those access patterns_ optimally leverage the underlying hardware. Hardware optimizations are often related (but not limited) to optimizing usage of the CPU cache, limit loading/storing to RAM (cache misses) and usage of SIMD instructions.

A comprehensive overview of Data oriented design is the "Data oriented design" book by Richard Fabian:
[https://www.dataorienteddesign.com/dodbook/](https://www.dataorienteddesign.com/dodbook/)

什么是面向数据的设计

维基百科将面向数据设计定义为

...... 一种程序优化方法，出于有效利用 CPU 缓存的动机，并用于视频游戏开发。这种方法侧重于数据布局，根据需要它们的时机对字段进行分离和排序，并考虑数据的转换。

面向数据的设计是一个总括术语，包含大量的技术和使用这些技术的条件。一般来说，其目标是分析应用程序中不同类型数据的访问模式，并*为这些访问模式*选择能最佳利用底层硬件的数据结构。硬件优化通常涉及（但不限于）优化 CPU 缓存的使用、限制加载/存储到 RAM（缓存缺失）以及 SIMD 指令的使用。

理查德-法比安（Richard Fabian）撰写的《面向数据的设计》一书全面概述了面向数据的设计： https://www.dataorienteddesign.com/dodbook/ 。
### Is ECS the same as DoD?
No. It is possible to write code that uses DoD principles without it being ECS, and it is possible to create an ECS that does not leverage DoD.

The ECS pattern does lend itself well towards DoD, which is why many ECS frameworks (though not all) have a storage design that allows applications to leverage the optimizations enabled by DoD.

If an ECS iterates contiguous component arrays, it allows for leveraging DoD principles and optimizations.

ECS 跟 DoD 一样吗？

并不是。使用 DoD 原则编写的代码有可能不属于 ECS；不利用 DoD 原则就创建出 ECS，这种事也有可能。

ECS 模式与 DoD 相性极佳，这也是许多 ECS 框架（不过不是全部）都有存储设计的原因，毕竟这样就允许应用程序利用 DoD 实现优化。

如果 ECS 要遍历连续的组件数组，就可以利用 DoD 原则和优化。
### What is Random Access Memory?
RAM is the kind of memory that computers typically have lots of, and is where the entire state of applications and their code is stored. A CPU interfaces with RAM when it executes application code. 

While RAM is incredibly fast in absolute terms, the bus between a CPU and RAM can become a bottleneck in data-heavy applications. This is why in data oriented design, techniques are employed to minimize the number of loads from RAM.

什么是随机存取内存？

RAM 是计算机通常拥有的大量内存，是存储应用程序及其代码全部状态的地方。CPU 在执行应用程序代码时与 RAM 连接。

虽然 RAM 的绝对速度快得惊人，但在数据量大的应用程序中，CPU 和 RAM 之间的总线可能会成为瓶颈。这就是为什么在面向数据的设计中，要采用各种技术来尽量减少来自 RAM 的负载数量。
### What is a CPU cache?
A CPU cache is a kind of memory that is much faster, but also _much_ smaller than RAM. When a CPU loads data from RAM it is stored in a cache tier, where lower tiers are faster and larger tiers are slower.

Data oriented design employs techniques to utilize a CPU cache as efficiently as possible, so that the number of loads from RAM are minimized.

什么是 CPU 缓存？
CPU 缓存是一种比 RAM 更快但也更小的内存。当 CPU 从 RAM 中加载数据时，数据会存储在缓存层中，其中较低的缓存层速度较快，而较大的缓存层速度较慢。

面向数据的设计采用了尽可能高效利用 CPU 缓存的技术，从而将从 RAM 中加载数据的次数降到最低。
### What is a cache line?
A cache line represents the amount of data that is retrieved from RAM in a single load. When an application requests, say 4 bytes from RAM, a CPU will actually load 64 bytes, starting from the requested address.

An application can reduce the number of loads from RAM by storing data in close proximity, which increases the chance that data required for future operations is already loaded in the cache.

什么是缓存行？

缓存行代表一次加载从 RAM 中获取的数据量。当应用程序请求从 RAM 中读取 4 个字节时，CPU 将从请求的地址开始实际读取 64 个字节。

应用程序可以通过就近存储数据来减少从 RAM 中加载数据的次数，这就增加了未来操作所需的数据已经加载到缓存中的机会。
### What is locality of reference?
Locality refers to either temporal or spatial locality. Temporal locality refers to the reuse of data within a short amount of time. Spatial locality refers to the proximity of storage locations. High locality in either category increases the efficiency of caching, as a CPU is better able to predict access patterns.

什么是参照位置？

本地性指的是时间或空间上的本地性。时间位置性是指数据在短时间内的重复使用。空间位置性是指存储位置的邻近性。无论是哪种类型的高定位性，都能提高缓存的效率，因为 CPU 能够更好地预测访问模式。
### What is SIMD?
SIMD, or Single Instruction Multiple Data, refers to a set of instructions or instruction families that can process multiple values in the time it takes to do a single instruction.

什么是 SIMD？

SIMD，即单指令多数据，是指一组指令或指令系列，可以在执行单条指令的时间内处理多个值。
### What is vectorization?
Vectorization (or automatic vectorization) is the process whereby code that meets certain criteria uses SIMD instructions to improve performance. As a result of using these optimized instructions, vectorized code can run multiple times faster than regular code.

The conditions for vectorized code are:
- Data must be stored contiguously (in arrays)
- The code should contain no branches or function calls

Compilers are generally able to vectorize loops that meet the above conditions. It depends on the compiler however which scenarios will be automatically vectorized. [This page](https://llvm.org/docs/Vectorizers.html) provides an overview of scenarios that the clang compiler is able to automatically vectorize.

什么是矢量化？
矢量化（或自动矢量化）是指符合特定标准的代码使用 SIMD 指令来提高性能的过程。由于使用了这些优化指令，矢量化代码的运行速度比普通代码快数倍。

矢量化代码的条件是
- 数据必须连续存储（在数组中）
- 代码不应包含分支或函数调用

编译器通常可以对满足上述条件的循环进行矢量化。至于哪些情况会自动矢量化，则取决于编译器。本页概述了 clang 编译器能够自动矢量化的情况。
### What is false sharing?
False sharing occurs when different threads attempt to load and alter two values that are different, but in the same cache line. This causes a cache sync, which can degrade performance.

False sharing is avoided by ensuring that data accessed by different threads is not in close enough proximity for it to be loaded in a single cache line.

什么是错误共享？

当不同的线程试图加载和更改两个不同但在同一缓存行中的值时，就会出现错误共享。这会导致缓存同步，从而降低性能。

避免假共享的方法是确保不同线程访问的数据不在同一缓存行中加载。
### What is AoS?
AoS, or "array of structs" refers to a memory layout where a struct containing multiple fields is stored in an array. An example of AoS is:

```c
Struct AoS {
  Int m_1;
  Int m_2;
};

AoS values[1000];
```

An advantage of AoS is that data is stored in arrays which benefits cache locality. A potential disadvantage of AoS is that when code only requires a subset of members in a struct, more data is loaded into the cache than is strictly necessary.

In the context of ECS, AoS usually refers to a memory layout where all components are stored in the same array.

什么是 AoS？
AoS 或 "结构体数组 "是指一种内存布局，其中包含多个字段的结构体存储在一个数组中。AoS 的示例如下：
```c
Struct AoS {
  Int m_1;
  Int m_2;
};

AoS values[1000];
```
AoS 的优点是数据存储在数组中，有利于缓存定位。AoS 的一个潜在缺点是，当代码只需要结构体中的一个子集成员时，加载到缓存中的数据会多于实际需要。
在 ECS 中，AoS 通常是指所有组件都存储在同一个数组中的内存布局。
### What is SoA?
SoA, or "struct of arrays" refers to a memory layout where a struct contains multiple arrays, one for each field. An example of SoA is:

```c
Struct SoA {
  Int m_1[1000];
  Int m_2[1000];
};

SoA values;
```

Like AoS, data is stored in arrays which benefits cache locality. An additional advantage of SoA is that when code only needs a subset of members in a struct, the other members are not loaded in cache. A potential disadvantage of SoA is that if code randomly needs to access other members, it incurs more cache misses than AoS.

In the context of ECS, SoA usually refers to a memory layout where components are stored in separate arrays.

什么是 SoA？
SoA 或 "数组结构 "是指一种内存布局，其中一个结构包含多个数组，每个字段一个数组。SoA 的示例如下：

```c
Struct SoA {
  Int m_1[1000];
  Int m_2[1000];
};

SoA values;
```

与 AoS 类似，数据存储在数组中，这有利于缓存定位。SoA 的另一个优点是，当代码只需要结构体中的一个子集成员时，其他成员不会加载到缓存中。SoA 的一个潜在缺点是，如果代码随机需要访问其他成员，则会比 AoS 产生更多的缓存缺失。
在 ECS 中，SoA 通常指的是将组件存储在独立数组中的内存布局。
### What is branch prediction?
When a CPU executes a set of instructions, it tries to predict which path the code will take, by taking an educated guess at how conditional statements (like if-else, switch) will be evaluated. These instructions are then preloaded into the instruction cache, and can even be executed in advance.

When code contains many unpredictable branches, the branch predictor may often have to discard the precomputed results, which results in measurably slower code.

什么是分支预测？

当 CPU 执行一组指令时，它会通过对条件语句（如 if-else、switch）的评估方式进行有根据的猜测，尝试预测代码将采取的路径。然后将这些指令预载入指令缓存，甚至可以提前执行。

当代码包含许多不可预测的分支时，分支预测器可能经常不得不放弃预先计算的结果，从而导致代码速度明显降低。
## 术语表

### Entity
An entity in ECS represents a single "thing" in a game and is generally represented as a unique integer value.

实体
ECS 中的实体代表游戏中的单个“事物”，通常以唯一的整数值表示。
### Component
A component is a datatype that can be added to or removed from entities. Components in ECS are generally plain data types and not encapsulated.

组件
组件是一种数据类型，可以添加到实体或从实体中移除。ECS 中的组件通常是普通数据类型，没有封装。
### Tag
A tag is a component that has no data.

标签

标签是一种没有数据的组件。
### System
A system is an executable object that is matched with all entities that have a certain set of components. 

系统

系统是一个可执行对象，它与所有具有特定组件集的实体相匹配。
### Query
A query is similar to a system, but cannot be executed by itself.

查询
查询与系统类似，但不能由它自身执行。
### World
A world is the container for all ECS data. ECS frameworks often allow a single application to have multiple ECS worlds.

世界
世界是所有 ECS 数据的容器。ECS 框架通常允许一个应用程序拥有多个 ECS 世界。
### Registry
Same as world.

注册表
与世界相同。
### Archetype
A data structure that stores entities for a specific set of components. Components are stored as columns in contiguous arrays. 

原型
一种为特定组件集存储实体的数据结构。组件作为列存储在连续数组中。
### Table
Often used interchangeably with archetype. In some ECS implementations a table refers to an archetype that stores "dense" components (contiguous arrays with aligned indices), where an archetype stores "sparse" components (components not contiguously stored, or not stored with aligned indices) and indexes into a table.

表
经常与原型互换使用。在某些 ECS 实现中，表指的是存储“密集”组件（具有对齐索引的连续数组）的原型，而原型指的是存储“稀疏”组件（非连续存储，或不存储对齐索引的组件）和索引到表中的原型。
### Sparse set
A data structure that provides fast iteration, lookup, insertion and removal times. Similar to a hashmap, but better suited for sequential identifiers.

稀疏集
一种可快速迭代、查找、插入和删除的数据结构。与哈希表类似，但更适合顺序标识符。
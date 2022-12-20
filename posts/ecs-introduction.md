---
title: "Entity Component System: Introduction"
description: "Let's learn about the Entity Component System (ECS) paradigm with the Rust game engine Bevy."
date: "2022-12-15"
---

If you use [Unity](https://unity.com/) or [Bevy](https://bevyengine.org/), you certainly heard about the Entity Component System (ECS) paradigm. Some video game engines like Bevy come with ECS by default, where Unity only published it recently as a separate package [^unity-ecs].

[^unity-ecs]: Unity ECS is still in preview and they have made several implementation attempts to be compatible with gameobjects and meet the needs of their users. You can learn more on Unity Blog with their article [Games Focus: Expanded scale for ambitious games, November 23, 200](https://blog.unity.com/technology/games-focus-expanded-scale-for-ambitious-games).

In this blog post you will learn about ECS, when you need it, and I will also provide some examples for both Unity and Bevy Engine. This is only an introduction, and I will write more in-depth content in the future (so be sure to subscribe from the [homepage](/)).

## A matter of design and trade-off

Object-oriented design (OOD) or data-oriented design (DOD)? The choice depends on three major points in my opinion :

- The complexity of your interactions (and in general your game)
- Performance needs
- Architecture decisions

Regarding the last point, it is not necessarily obvious which architecture / design to chose when you start. This is completely normal since object-oriented programming (OOP) is a very popular way to model and encapsulate the data. When you start a project you don't see all the performance and optimization needs, and implementing a specific solution for that could turns out to be premature optimization. However with time and experience you will see the pitfalls of some designs for specific areas of your code or even in your infrastructure (Have you ever tried to deal with physics in a multiplayer game?). Just note that each solution comes with drawbacks and at the end you will have to sacrifice some advantages for others.

We will explore both designs to understand the main differences then we will talk about Entity Component System (ECS).

### Object oriented design

Most of the time game development is aborded through an object-oriented design where your your entities are modeled as objects implementing behaviors (interfaces, traits) and extending classes (inheritance). You also have the concept of polymorphism which is a powerful way to provide different implementations of a method depending on the type of an object. OOD works very well with small to medium-sized games in terms of architectural and data complexity.

Object oriented programming comes with design patterns that are formalized in the popular book _Design Patterns: Elements of Reusable Object-Oriented Software_, also known as _The Gang of Four (GoF)_. Design patterns are solutions to known problems helping you to architecture your code correctly. Some of them are very popular: Factory, Singleton, Builder, Observer, Adapter, Proxy. For most of your games OO design is well suited; many games are developed with this paradigm.

As a side note I also recommend you to read [Game Programming Patterns](http://gameprogrammingpatterns.com) which is way more specific about game development.

<figure>
    <img class="mx-auto max-size-lg" src="/images/blog/ecs-introduction/class_diagram_general.svg" alt="Class diagram representing inheritance between entities such as Player and Ai inheriting Humanoid class that also inherits Collider and GameObject.">
    <figcaption>Class diagram representing the concept of inheritance with OOD. We can observe a tree representation.</figcaption>

</figure>

While OOD provides many advantages, at some point your codebase will grow and you will end up with a very large and complex tree representation of your entities. It will become challenging to maintain the codebase while ensuring good performance and efficient data processing. If you implement game mechanics involving an important volume of entities such as a massive battlefield with a sandbox environment, you will need to design your game quite differently to keep good performance, which is an important goal in video games as it can directly impact the user experience.

### Data-oriented design

If your game has a large amount of data, needs large-scale streaming and a better way to organise and request data, then you should think about a data-oriented design. You will have more control over your data. As _Robert Nystrom_ wrote in his chapter _Data Locality_ [^data-locality] :

[^data-locality]: Game programming Patterns, Optimization Patterns : [Data locality](http://gameprogrammingpatterns.com/data-locality.html)

> the way you organize data directly impacts performance

The idea is that you can use specific data structures (like arrays) in a way you benefit from CPU caching.

<figure>
    <img class="mx-auto max-size-lg" src="/images/blog/ecs-introduction/data_cache_access_flow_chart.svg" alt="Flow chart diagram describing the concept of cache hit and cache miss."/>
    <figcaption>Flowchart describing how cache data is accessed and the concept of cache hit and cache miss.</figcaption>
</figure>

When there is a _cache hit_, the CPU successfuly reads the data from the cache. But when the data isn't available then there is a _cache miss_ and the memory block is fetched from the RAM and all the while the CPU is waiting. Now if you follow the _Data Locality_ pattern, you can store chunks of memory in cache in a way blocks are adjacent to each other so you can improve performance.

With this example we start to see how data-oriented design can help to gain control over data. Ideally you want to organize your information with components grouped by types so you can process the data in an efficient way compared to OO design that involves tree representations.

<figure>
    <img class="mx-auto max-size-lg" src="/images/blog/ecs-introduction/data_sequence.svg" alt="Different sequences of data processed in parallel."/>
    <figcaption>Different data sequences processed in parallel.</figcaption>
</figure>

When you organize your data in contiguous blocks that you can process sequentially, you see some advantages emerging:

- **Cache ready**: Your data is already laid out to be effectively cached. A good use case is the data locality we talked about, where blocks are adjacent to each other.
- **Ease of parallelization**: As you can see in the diagram above, data of type `A` can be organized in different chunks/sequences and passed to functions that will process the data and transform it to an `Output A`. You can use multiple threads to process the data faster (divide and conquer). Contiguous blocks of homogeneous entities/types are naturally easier to process.
- **Better architecture**: You follow the principle of data-first, which leads you to naturally organize your code around it and treat information as streams / sequences. The functions processing the data are loosely coupled from the rest of your system (like game mechanics), making it easier to maintain and test in isolation. Your data is not constrained by an object representation that sometimes doesn't fit.

While there are many advantages, it definetely involves a more in-depth understanding of data structures and memory management that can be completely different from one operating system to another (e.g PC versus consoles). Some optimisations for one platform may not apply to another. Data-oriented design is not a silver bullet but can be very useful for specific data-centric video games [^data-centric] and/or specific areas of your code. The complexity can however be mitigated by out-of-the-box solutions such as engines, frameworks or libraries/packages.

[^data-centric]: Data-centric is not, to my knowledge, an official term. I use this term to give my own definition of a video game where _data_ plays an important role in game design (which ultimately impacts architecture decisions). A good example would be a game where the world is procedurally generated and involves many AI-controlled entities. This term is, to me, different from _data-driven_ (although completely compatible together) where the data drives what happens in the game world.

## Entity Component System (ECS)

Based on the previous explanations, we can define ECS as a paradigm based on a data-oriented design with three principal parts :

- Entities
- Components
- Systems

### Unity ECS example

### Bevy example

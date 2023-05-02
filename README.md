# Singleton

Ensure a class has one instance, and provide a global point of access to it.

### Why We Use It

- **It doesnt create the instance if no one uses it.** Saving memory and CPU cycles is always good. Since the singleton is initialized only when it’s first accessed, it won’t be instantiated at all if the game never asks for it.

- **Its initialized at runtime.** A common alternative to Singleton is a class with static member variables. I like simple solutions, so I use static classes instead of singletons when possible, but there’s one limitation static members have: automatic initialization. The compiler initializes statics before main() is called. This means they can’t use information known only once the program is up and running (for example, configuration loaded from a file). It also means they cant reliably depend on each other  — the compiler does not guarantee the order in which statics are initialized relative to each other.

- **Lazy initialization solves both of those problems.** The singleton will be initialized as late as possible, so by that time any information it needs should be available. As long as they dont have circular dependencies, one singleton can even refer to another when initializing itself.

- **You can subclass the singleton.** This is a powerful but often overlooked capability. Lets say we need our file system wrapper to be cross-platform. To make this work, we want it to be an abstract interface for a file system with subclasses that implement the interface for each platform.

### Why We Regret Using It

In the short term, the Singleton pattern is relatively benign. Like many design choices, we pay the cost in the long term. Once we've cast a few unnecessary singletons into cold hard code, heres the trouble we've bought ourselves:

#### Its a global variable

- When games were still written by a couple of guys in a garage, pushing the hardware was more important than ivory-tower software engineering principles. Old-school C and assembly coders used globals and statics without any trouble and shipped good games. As games got bigger and more complex, architecture and maintainability started to become the bottleneck. We struggled to ship games not because of hardware limitations, but because of productivity limitations.

- They make it harder to reason about code. Say we're tracking down a bug in a function someone else wrote. If that function doesn’t touch any global state, we can wrap our heads around it just by understanding the body of the function and the arguments being passed to it.

- They encourage coupling. The new coder on your team isnt familiar with your game’s beautifully maintainable loosely coupled architecture, but hes just been given his first task: make boulders play sounds when they crash onto the ground. You and I know we don’t want the physics code to be coupled to audio of all things, but he’s just trying to get his task done. Unfortunately for us, the instance of our AudioPlayer is globally visible. So, one little #include later, and our new guy has compromised a carefully constructed architecture.

The [Service Locator](https://github.com/LeGustaVinho/service-locator "Service Locator") pattern does make an object globally available, but it gives you more flexibility with how that object is configured.
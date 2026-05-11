![](images/978-1-4842-8695-1_CoverFigure.jpg)

ISBN 978-1-4842-8694-4e-ISBN 978-1-4842-8695-1 [https://doi.org/10.1007/978-1-4842-8695-1](https://doi.org/10.1007/978-1-4842-8695-1) © Andrés Ibañez Kautsch 2023 This work is subject to copyright. All rights are solely and exclusively licensed by the Publisher, whether the whole or part of the material is concerned, specifically the rights of translation, reprinting, reuse of illustrations, recitation, broadcasting, reproduction on microfilms or in any other physical way, and transmission or information storage and retrieval, electronic adaptation, computer software, or by similar or dissimilar methodology now known or hereafter developed. The use of general descriptive names, registered names, trademarks, service marks, etc. in this publication does not imply, even in the absence of a specific statement, that such names are exempt from the relevant protective laws and regulations and therefore free for general use. The publisher, the authors, and the editors are safe to assume that the advice and information in this book are believed to be true and accurate at the date of publication. Neither the publisher nor the authors or the editors give a warranty, expressed or implied, with respect to the material contained herein or for any errors or omissions that may have been made. The publisher remains neutral with regard to jurisdictional claims in published maps and institutional affiliations.

This Apress imprint is published by the registered company APress Media, LLC, part of Springer Nature.

The registered company address is: 1 New York Plaza, New York, NY 10004, U.S.A.

*To my mother Renata and to my brother Gastón, for all the support and patience they have always shown and for everything they have always done for me.*

Preface

Concurrency is a topic a lot of developers find scary. And for good reason. Concurrency is probably one of the most complicated topics in the world of programming. When you look at it from a very high level, concurrency allows us to do a lot of work at the same time. Sometimes related, sometimes unrelated. The definition is simple, but as a programmer, it is hard to reason about concurrent programs. We learn to write code that can be read from top to bottom, and code usually makes sense when you read it in such a manner. In fact, the way developers reason about code is not too different to how non-programmers reason about a cooking recipe. Humans can understand anything they read if it is structured and makes sense.

But the very nature of concurrency breaks this top-to-bottom reading flow. Not only do we need to reason about concurrency differently, but we also need to keep in mind other factors that make it harder, such as shared mutable data, locks, threads…. Concurrency is naturally very complicated, especially when dealing with lower-level tools.

Luckily, the new async/await system, introduced in 2021, makes concurrency easier to reason about because it abstracts a lot of the complexity behind language-integrated features that are easy to use and hard to misuse. If you have written concurrent code before with anything other than async/await in any other platform (and that includes but is not limited to iOS, macOS, and other Apple platforms), you do not have to concern yourself with mutexes, semaphores, or any other low-level concurrency primitives. If you have never written concurrent code before, you can write amazing multithreaded software in a way that makes sense without ever having to concern yourself with the complexities of traditional concurrency. This new system is very powerful and easy to use for both developers familiar with concurrency as well as those who have never written concurrent code before.

## Who This Book Is For

This book is aimed at intermediate iOS developers and above. You should find this book to be of your skill level if you have been writing Apple apps a bit over a year. Having experience with other concurrency tools in either Apple platforms or anything else may help you grasp this book easier, but previous concurrency knowledge is by no means necessary. You should be familiar with the basic process of writing and maintaining an iOS app to take advantage of this book.

## How This Book Is Organized

I tried my best to organize this book in a way that makes sense. There were topics that recursively required the knowledge of other topics before they could be properly understood. For those situations, I spent a little bit more time explaining some concepts at a higher level so you could get by before they got properly introduced.

**Chapter** [**1**](#532302_1_En_1_Chapter.xhtml) introduces concurrency and its traditional problems when trying to implement it. It discusses low-level concurrency primitives and how they can be used. It also discusses the traditional problems you will find when you try to implement a concurrency system without using higher-level tools such as async/await.

**Chapter** [**2**](#532302_1_En_2_Chapter.xhtml) formally introduces “async” and “await” as keywords of the Swift language. These two keywords are essential to understand to use the concurrency system effectively. Every single topic makes use of async/await, so this chapter is completely dedicated to these keywords.

**Chapter** [**3**](#532302_1_En_3_Chapter.xhtml) introduces Continuations, a tool that helps you migrate closure-based or even delegate-based code to use async/await. This can help you “bridge” such code into the async/await world, making them easier to write and understand. You will also learn how to backport concurrent code to iOS 14 and 13.

**Chapter** [**4**](#532302_1_En_4_Chapter.xhtml) introduces the concept of Structured Concurrency. You will write your first concurrent code here. Structured concurrency helps you write multithreaded code that is easy to read and write.

**Chapter** [**5**](#532302_1_En_5_Chapter.xhtml) introduces the concept of Unstructured Concurrency, a topic that will help you write streamlined concurrent code with a little bit more of flexibility than Structured Concurrency.

**Chapter** [**6**](#532302_1_En_6_Chapter.xhtml) introduces the concept of Actors. Actors are reference types that isolate their own state, so they are useful when you need to write concurrent code that deals with shared mutable state. It helps you answer questions such as “What happens if two processes write to this variable at the same time?”

**Chapter** [**7**](#532302_1_En_7_Chapter.xhtml) is all about Sendable types, which are objects that can be used safely in concurrent code, either because they have built-in protection (such as actors) or because the developers took special care of these types to make them usable concurrently (like classes that implement their own synchronization mechanism).

**Chapter** [**8**](#532302_1_En_8_Chapter.xhtml) discusses Global Actors, a tool to help you write concurrent code that is spread out across different files and even frameworks. It also discusses the Main Actor, a global actor that you use when you need to update your app’s UI.

**Chapter** [**9**](#532302_1_En_9_Chapter.xhtml) is all about async sequences. These sequences can help you receive values over time in an asynchronous context, helping you eliminate the usage of closures and delegates in some scenarios.

**Chapter** [**10**](#532302_1_En_10_Chapter.xhtml) , the final chapter, covers the usage of a property wrapper called @TaskLocal, which you can use to share data down a concurrent tasks tree.

## Before You Get Started

While Apple managed to backport the new concurrency system to iOS 13 and iOS 14, it is recommended you study this system with iOS 15\. There are no native APIs that use async/await in lower iOS versions, and you would need to provide an alternative to them every time you are interested in using them.

It is recommended you have at least Xcode 13, but you should have the latest version if possible. At the time of this writing, the latest Xcode version is 13.4.1\. The exercises and sample code were tested on this Xcode version. This implies your Mac will need to run macOS Monterey as Xcode 13 cannot run on anything lower than Monterey.

Acknowledgments

Whenever you decide to buy a technical book, you usually see one or two author names, and maybe a reviewer attached to it, but there are a lot of people involved in a single book, both directly and indirectly. It will not be possible to acknowledge everyone who has helped make this book possible, but I’d like to single out a few.

First, I’d like to give a big kudos to Alexis Marechal. Alexis has, in short, saved my career as a software developer. Alexis is not only a great teacher, but also a great friend. His teaching style has greatly inspired mine, and I hope I can do justice to that in this book. A big thank-you to Carlos Anibarro and Jose Luis Vera for their teachings and words of encouragement as well.

Writing a book is something I have always wanted to do, but Ernesto Campohermoso is the first person who ever asked me, “When are you going to write a book?” That single question was a great push for this book to come into existence.

Ivan Galarza is the first person I mentored in iOS development, and he became a very reliable iOS engineer in a short time thanks to all the talent he has. He was the ideal candidate to review a book in progress. His input helped me make sure everything makes sense and that this book will be something people would like to read.

A big thank-you to all the folks at Apress for the process that made writing this book possible. The entire experience has been a joy, from being contacted to write a book to turning in the final chapters. The final form of this book would have not been possible without their tenacity and amazing work ethic.

Finally, I’d like to give a big thanks to all the readers of my blog, andyibanez.com. The comments and feedback I received in the original tutorial series for this topic helped me greatly improve this book. I cannot reply to every message I get, but I am thankful to all my readers. Thank you all.

About the Author About the Technical Reviewer

# 1. Introduction

Programmers are used to writing programs that are executed in a linear fashion. As you write, test, and execute your program, you expect your instructions to run in the order that you wrote them. In Listing [1-1](#532302_1_En_1_Chapter.xhtml#PC2), you have a program that will first assign a variable `a` to the number `2`. It will then assign a variable `b` to the number `3`, followed by assigning a variable `sum,` the sum of `a + b`, and it will finally print a result to the console. There is no way this program will work if you try to *print(sum)* before you even managed to calculate the value for `sum`.

```
let a = 2
let b = 3
let sum = a + b // This variable depends on the values for a and b, but the variables themselves can be assigned in any order.
print(sum) // We can only print a variable that we have the value of.
Listing 1-1
A simple program that runs from top to bottom
```

This is called *procedural programming* , because you write simple statements, and they are executed from top to bottom. Even if you add statements that can alternate the execution flow, it’s still easy to follow. If you a call function when working with procedural programming , your program will “jump” to a different place in memory and execute its contents, but the execution of these lines will also be done procedurally in the same order they were written until control flow is returned to the caller.

Even people who are not programmers can follow any instruction set if they are written in a specific order and *if they are doing one thing at a time*. Someone following a cooking recipe, or someone building a doghouse from an online tutorial may not be a programmer, but people are naturally good at doing something if they have the steps clearly laid down.

But computer programs grow and become more complex. While it is true that a lot of complex software can be written that follows such a linear execution flow , often programs will need to start doing more than one thing at once; rather than having a clear code execution path that you can follow with your bare eyes, your program may need to execute in such a way that it’s not obvious to tell what’s going on at a simple glance of the source code. Such programs are multithreaded, and they can run multiple (and often – but not always – unrelated) code paths at the same time.

In this book, we will learn how to use Apple’s `async/await` implementation for asynchronous and multithreaded programming. In this chapter, we will also talk about older technologies Apple provides for this purpose, and how the new `async/await` system is better and helps you to not concern yourself with traditional concurrency pitfalls.

## Important Concepts to Know

Concurrency and asynchronous programming are very wide topics. While I’d love to cover everything, it would go out of the scope of this book. Instead, we will define four important concepts that will be relevant while we explore Apple’s `async/await` system , introduced in 2021\. We will define them with as few words as possible, because it’s important that you keep them in mind while you work through the chapters of this book. The concepts of the new system itself will be covered in the upcoming chapters.

Note

Apple is not the original creator of the `async/await` system . The technology has been used in other platforms in the past. Microsoft announced C# would get `async/await` support in 2011, and C# with these features was officially released to the public in 2012.

### Threads

The concept of *Thread* can vary even when talked about in the same context (in this case, concurrency, and asynchronous programming ). In this book, we will treat a *thread* as a unit of work that can run independently. In iOS, the *Main Thread* runs the UI of your app, so every UI-related task (updating the view hierarchy, removing and adding views) must take place in the main thread. Attempting to update the UI outside of the main thread can result in unwanted behavior or, even worse, crashes.

In low-level multithreading frameworks , multithreading developers will manually create threads and they need to manually synchronize them, stop them, and do other thread management operations on their own. Manually handling threads is one of the hardest parts of dealing with multithreading in software.

### Concurrency and Asynchronous Programming

Concurrency is the ability of a thread (or your program) to deal with multiple things at once. It may be responding to different events, such as network handlers, UI event handlers, OS interruptions , and more. There may be multiple threads and all of them can be concurrent.

There are different APIs throughout Apple’s SDKs that make use of concurrency. Listing [1-2](#532302_1_En_1_Chapter.xhtml#PC3) shows how to request permission to use Touch ID or Face ID, depending on the device.

```
func requestBiometricUnlock() {
let context = LAContext()
var error: NSError? = nil
let canEvaluate = context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error)
if canEvaluate {
if context.biometryType != .none {
// (1)
context.evaluatePolicy(
.deviceOwnerAuthenticationWithBiometrics,
localizedReason: "To access your data") { (success, error) in
// (2)
if success {
// ...
}
}
}
}
}
Listing 1-2
Biometric unlock is an asynchronous task
```

`(1)` calls `context.evaluatePolicy`, which is a *concurrent call* . This will ask the system to suspend your app so it can take over. The system will request permission to use biometrics while your app is suspended. The thread your app was running on may be doing something entirely different and not even related to your app while the system is running `context`. `evaluatePolicy` . When the user responds to the prompt, either accepting or rejecting the biometric request, it will deliver the result to your app. The system will wait for an appropriate time to notify your app with the user’s selection. The selection will be delivered to your app in the completion handler (also called a *callback* ) on `(2)`, at which point your app will be in control of the thread again. The selection may be delivered in a different thread than the one which launched the `context.evaluatePolicy` call – this is important to know, because if the response updates the UI, you need to do that work on the main thread . This is also called a *blocking mechanism* or *interruption* , as `evaluatePolicy` is a *blocking* call for the thread. If you have done iOS for at least a few months now, you are familiar with this way of dealing with various events. `URLSession`, image pickers, and more APIs make use of this mechanism.

People often think that asynchronous programming is the act of running multiple tasks at once. This is a different concept called *Multithreading* , and we will talk about it in the next point.

Note

If you are thinking on implementing biometric unlock to resources within your app, please don’t use the code above. It has been simplified to explain how concurrency works, and it doesn't have the right safety measures to protect your user's data.

Multithreading is the act of running multiple tasks at once. Multiple threads (hence its name – multithreading) are usually involved. Many tasks can be running at the same time in the context of your app. Downloading multiple images from the internet at the same time or downloading a file from your web browser while you open some tabs are some examples of multithreading. This allow us to run tasks in *parallel* and is sometimes called *parallelism* .

### Multithreading Pitfalls

Concurrency and multithreading are traditionally hard problems to solve. Ever since their introduction in the computer world, developers have had to develop paradigms and tools to make dealing with concurrency easier. Because programmers are used to thinking procedurally, writing code that executes at unexpected times is hard to get right.

In this section we will talk about some of the problems developers who write low-level multithreading code often face, and the models they have created to work with them. It is important you understand this section because these traditional problems are real, but their solutions are already implemented in the `async/await` system . It will also help you decide which technology you should use next time you need to implement concurrency or multithreading in your programs.

#### Deadlocks

In the context of multithreading, a *deadlock* occurs when two different processes are waiting on the other to finish, effectively making it impossible for any of them to finish to begin with.

This can happen when both processes are sharing a resource. If *Thread B* wants a resource that *Thread A* is holding, and *Thread A* wants a resource that *Thread B* has, both processes will be waiting for each other to finish, sitting in a perpetual deadlock state. Figure [1-1](#532302_1_En_1_Chapter.xhtml#Fig1) illustrates how this might occur.

![](images/532302_1_En_1_Chapter/532302_1_En_1_Fig1_HTML.jpg)

A schematic diagram depicts the execution flow of Threads A and B, which consists of resource C with the correct symbol at Thread A and resource D with the incorrect symbol and vice versa of symbols at Thread B.

Figure 1-1

Deadlocking in action

Figure [1-1](#532302_1_En_1_Chapter.xhtml#Fig1) illustrates how Thread A may try to access Resource C and Resource D, one after the other, and how Thread B may try to do the same but in different order. In this example, the deadlock will happen rather soon, because Thread A will get hold of Resource C while Thread B gets hold of Resource D. Whoever needs each other’s resource first will cause the deadlock .

Despite how simple this problem looks, it is the culprit of a lot of bugs in a lot of multithreaded software. The simple solution would be to prevent each process from attempting to access a busy resource. But how can this be achieved?

##### Solving the Deadlocking Problem

The deadlock problem has many established solutions. *Mutex* and *Semaphores* being the most used ones. There is also *Inter-process communication through pipes*, but we will not talk about it because it goes beyond a single program.

###### Mutex

*Mutex* is short for *Mutually exclusive lock* (or *flag*). A mutex will signal other processes that a process is currently using some resource by adding a *lock* to it and preventing other processes from grabbing until that lock is freed. Ideally, a process will acquire a lock to all the resources it will need at once, even before using them. This way, if *Thread A* needs *Resource C* and *Resource D*, it can lock them before *Thread B* tries to access them. *Thread B* will wait until all the locks are freed before attempting to access the resources itself. Figure [1-2](#532302_1_En_1_Chapter.xhtml#Fig2) illustrates how this is done.

![](images/532302_1_En_1_Chapter/532302_1_En_1_Fig2_HTML.png)

A schematic diagram depicts the execution flow of Thread A and B, which consists of Resources C and D with the lock symbol, continue execution with locked resources, and wait for resources to get unlocked with the refresh symbol.

Figure 1-2

Using a mutex

Keep in mind that in this specific situation, it means that *Thread A* and *Thread B*, while multithreaded, will not run strictly at the same time, because *Thread B* needs the same resources as *Thread A* and it will wait for them to be free. For this reason, it is important to identify tasks that can be run in parallel before designing a multithreaded system.

###### Semaphores

A *Semaphore* is a type of lock, very similar to a mutex . With this solution, a task will acquire a lock to a resource. Any other tasks that arrive and need that resource will see that the resource is busy. When the original thread frees the resources, it will *signal* interested parties that the resource is free, and they will follow the same process of locking and signaling as they interact with the resource.

Mutex and Semaphores sound similar, but the key difference lies in what kind of resource they are locking, and if you ever find yourself in a situation in which you need to decide what to protect (you won’t find such a case when using `async/await`), it is important to think about what makes sense in your use case. In general, a mutex can be used to protect anything that doesn’t have any sort of execution on its own, such as files, sockets, and other filesystem elements. Semaphores can be used to protect execution in your program itself such us shared code execution paths. These can be mutating functions or classes with shared state. There may be cases in which executing a function by more than one thread will have unintended consequences, and semaphores are a good tool for that. For example, a function that writes a log to disk may be protected with a semaphore because if multiple processes write to the log at the same time, the log will become corrupted for the end user. Whoever has a semaphore to the logging function needs to signal the other interested parties that it is free so they can continue their execution.

A rule of thumb is to always add a timeout whenever a semaphore is acquired and let processes timeout if they take too long. If this is acceptable in the context of your program (no data corruption can occur, or there can be no other unintentional consequences to canceling tasks), consider adding a timeout so the semaphore is free again after a certain period.

The use of semaphores will allow threads to *synchronize*, as they will be able to coordinate the resources amongst each other so they both have their fair usage of them.

#### Starvation

The *Starvation* problem happens when a program is stuck in a state of perpetually waiting for resources to do some work but never receiving them. Figure [1-3](#532302_1_En_1_Chapter.xhtml#Fig3) illustrates this problem.

![](images/532302_1_En_1_Chapter/532302_1_En_1_Fig3_HTML.png)

A circular model represents the five people symbol outside the circle, which resembles a dining table with five forks and plates.

Figure 1-3

The dining philosophers problem

Figure [1-3](#532302_1_En_1_Chapter.xhtml#Fig3) describes what is known as the *dining philosophers problem*, and it is a classical representation of the starvation problem .

The problem illustrates five philosophers who want to eat at a shared table. All of them have their own dishes, and there are five forks. However, each philosopher needs two forks to eat. This means that only two philosophers get to eat, and only the ones who are not sitting directly next to each other. When a philosopher is not eating, he puts both forks down. But there can be a situation in which a philosopher decides to overeat, leaving another to starve.

Luckily for low-level multithreading developers, it’s possible to use the *semaphores* we talked about above to solve this problem. The idea is to have a semaphore that keeps track of the used forks, so it can signal another philosopher when a fork is free to be used.

#### Race Conditions

Likely the multithreading problem most developers are familiar with, *Race Conditions* are very similar to deadlocks , with the exception that they can cause data or memory corruption that will lead to other unpleasant consequences. When we are dealing with multithreading, it isn’t inherently a bad thing that two processes are reading data at the same time. If the resource is read-only, there is no harm. However, if the processes can modify or update the resource in any way, the processes will continually overwrite the data another process just wrote, and this will eventually lead to reading and writing corrupted data. If the resource in question is user data, we will have unhappy users. If the resource is provided by the OS, we can cause other unwanted consequences and eventually reach one of the multithreading issues such as deadlocks . Figure [1-4](#532302_1_En_1_Chapter.xhtml#Fig4) illustrates this pitfall.

![](images/532302_1_En_1_Chapter/532302_1_En_1_Fig4_HTML.jpg)

A schematic diagram depicts the history log in the center, with Thread A and Thread B pointing towards it.

Figure 1-4

Multiple threads writing to the same file at the same time

If a history log is being written to by any threads with no control, events may be registered in a random order, and one thread may override the line another thread just wrote. For this reason, the History Log should be locked at the writing operation. A thread writing to it will get a *mutex* to the log, and it will free it after it’s done using it. Anyone else needing to write to the log will be waiting until the lock is freed.

Deadlocks and race conditions are very similar. The main difference, in deadlocks, is we have multiple processes waiting for the other to finish, whereas in race conditions we have both processes writing data and corrupting it because no processes know that the resource in question is being used by someone else. This means that the solutions for deadlocking also apply to race conditions , so just block your resource with a mutex or semaphore to guarantee exclusive access to the resource by one process only. This is called an *Atomic Operation*.

#### Livelocks

There is a saying in life that goes “*you can have too much of a nice thing*.” It is important to understand that throwing locks willy-nilly is not a solution to all multithreading problems. It may be tempting to identify a multithreading issue, throw a mutex at it, and call it a day. Unfortunately, it’s not so easy. You need to understand your problem and identify where the lock should go.

Similarly, this problem can occur when processes are too lenient with the conditions under which they can release and attain resources. If *Thread A* can ask *Thread B* for a resource and *Thread B* complies without any thought, and *Thread B* can do the same to *Thread A*, it may happen that eventually they will not be able to ask each other for the resource back because they can’t even get to that point of the execution.

This is a classic example in real life. Suppose you are walking towards a closed door and someone else arrives to it at almost the same time as you. You may want to let the other person go in first, but they may tell you that you should go first instead. If you are familiar with this awkward feeling, you know what a livelock feels like for our multithreaded programs.

### Existing Multithreading and Concurrency Tools

Apple offers some lower-level tools to write multithreaded and concurrent code. We will show examples for some of them. They are listed from lower level to higher level. The lower the level, the harder it is to use that system correctly, and the less likely it is that you will encounter it in the real world anyway. You should be aware of these tools so you can be prepared to work with them if you ever need to.

#### pthreads

*pthreads* (POSIX Threads) are the implementation of a standard defined by the IEEE.^([¹](#fn1)) The *POSIX* part of their name (Portable Operating System Interface) tells us that they are available in many platforms, and not only on Apple’s operating systems. Traditionally, hardware vendors used to sell their products offering their own, proprietary multithreading APIs. Thanks to this standard you can expect to have a familiar interface in multiple POSIX systems. The great advantage of pthreads is that they are available in a wide array of systems if they are POSIX compliant, including some Linux distributions.

The disadvantage is that pthreads are purely written in C. C is a very low-level programming language, and it is a language that is slowly fading from the knowledge base of many developers. The younger they are, the less likely they are to know C. While I do not expect C to disappear any time soon, the truth is that it’s very hard to find iOS developers who know the C programming language, let alone use it correctly. pthreads are the lowest-level multithreading APIs we have available, and as such they have a very steep learning curve. If you opt to use pthreads , it’s because you have highly specific needs to control the entire multithreading and concurrency flow, though most developers will not need to drop down to these levels often, if at all. You will be launching threads and managing resources such as mutex more manually. If you are familiar with pthreads because you worked with them in another platform, you can use your knowledge here too, but be aware future programmers who will maintain your code may not be familiar with either pthreads or the C language itself.

#### NSThreads

*NSThread* is a Foundation object provided by Apple. They are low level, but not as low level as pthreads. Since they are foundation objects, they offer an Objective-C interface . Offering this interface will expose the tool to many more developers, but as time goes on, less iOS developers are likely to know Objective-C. In fact, it’s entirely possible to find iOS developers with some years of experience who have never had to use this language before, although it can be used in Swift as well.

If you want to work with multithreading, you will end up creating multiple `NSThread` objects, and you are responsible for managing them all. Each `NSThread` object has properties you can use to check the status of the thread, such as `executing`, `cancelled`, and `finished`. You can set the priority of each thread which is a very real use case in multithreading. You can even get the main thread back and ask if the current thread is the main thread to avoid doing expensive work on it.

Just like pthreads , most developers will not have to drop down to this level often, if at all.

#### The Grand Central Dispatch (GCD)

We will now talk about the first high-level tool for high-level concurrency and multithreading on Apple platforms. The Grand Central Dispatch (simply called GCD from here on) is so ubiquitous throughout Apple’s SDKs that you have likely used it even without knowing. I’d be surprised if I met a developer who has never written a line like Listing [1-3](#532302_1_En_1_Chapter.xhtml#PC4).

```
DispatchQueue.main.async {
nameLabel.text = userResponse.username
}
Listing 1-3
Calling code on the main thread
```

Looks familiar? This is one of the most famous calls on the GCD because it allows you to defer some work quickly and painlessly to be done on the main thread . This call is likely to be found after a subclass of `URLSessionTask` finishes some work and you want to update your UI, for example.

The GCD , being high level, saves you from doing a lot of work than `pthread` s and `NSThread` s . With the GCD, you will never concern yourself with managing threads manually. It truly is a high-level framework in that sense, but it carries its own baggage as well. Listing [1-4](#532302_1_En_1_Chapter.xhtml#PC5) shows a classic issue some developers have when working with the GCD .

```
func fetchUser() {
userApi.fetchUserData { userData in
DispatchQueue.main.async {
self.usernameLabel.text = userData.username
self.userApi.fetchFavoriteMovies(for: userData.id) { movies in
DispatchQueue.main.async {
self.userMovies = movies
}
}
}
}
}
Listing 1-4
The pyramid of doom
```

Listing [1-4](#532302_1_En_1_Chapter.xhtml#PC5) shows hypothetical code that would retrieve a user’s data and their favorite movies from somewhere. This usage is very typical with the GCD . When your work requires you to do more than one call that depends on the task of another, your code starts looking like a *pyramid of doom* . There are ways to solve this (like creating different functions for fetching the user data and their movies), but it’s not entirely elegant.

Despite its drawbacks, the GCD is a very popular way to create concurrent and multithreaded code. It has a lot of protections in place already for you, so you don’t have to be an expert in the theory of multithreading to avoid making mistakes. The samples we saw here only show a very tiny bit of functionality it offers. While the tool is high level enough to save you from many headaches, it exposes a lot of lower level functionality, and it gives you the ability to directly interact with some primitives such as semaphores .

Finally, this technology is open source, so it’s possible to find in platform outside of anything Apple develops. The GCD is big, and talking about its features would go outside the scope of this book, but be aware that it has been in use for a very long time and it’s possible you are going to see some advanced techniques with it in your career.

#### The NSOperation APIs

Sitting at a higher level than the GCD , the `NSOperation` APIs are a high-level tool to do multithreading. Despite the fact they are not as widely known as the GCD, they found their place in some parts of the SDK. For example, the CloudKit APIs make use of this technology.

This one truly abstracts a lot of the pains of multithreading for you, but it loses a lot of flexibility. You will never have to manage locks or semaphores with it, and in turn the API is simple. This tool abstracts so many details to the point that you don’t even know if tasks you run with it are running on different threads. If the system thinks it makes sense to run your tasks in the same thread, it will do that. Otherwise, it may honor your intention of running in multiple threads. It may even choose to run your task in the main thread if it sees it’s not worth it to launch another thread for its work. Listing [1-5](#532302_1_En_1_Chapter.xhtml#PC6) creates two different tasks that count from 1 to 10 and from 10 to 20 in different queues.

```
func startCounting() {
/// We will need a queue for this.
let operationQueue = OperationQueue()
/// You can give your queue an optional name, if you need to identify it later.
operationQueue.name = "Counting queue"
/// This will just count from 1 to 10...
let from1To10 = BlockOperation {
for i in (1 ... 10) {
print(i)
}
}
/// ... and this from 11 to 20
let from11To20 = BlockOperation {
for i in (11 ... 20) {
print(i)
}
}
/// Add the operations to the queue
operationQueue.addOperation(from1To10)
operationQueue.addOperation(from11To20)
/// To ensure the program doesn't exit early while the operations are running.
operationQueue.waitUntilAllOperationsAreFinished()
}
Listing 1-5
Multithreaded usage of the NSOperation APIs
```

Using these APIs is very simple. You begin by creating an `OperationQueue`. This queue’s responsibility is to execute any task you add to it. You can also give it a name to refer to it later, or if you need to search for it.

In *Listing* [*1-5*](#532302_1_En_1_Chapter.xhtml#PC6) , we create two tasks (in this case, instances of `BlockOperation` – the original API is in Objective-C, so in Swift, it’s a closure), `from1To10` and `from11To20`. They begin executing as soon as they are submitted to the queue via the `OperationQueue.addOperation` call. In this example, you will see that the numbers get printed almost in an interleaved manner. The results you get are going to be different each time you run the program.

It’s very easy to run multiple tasks at once, but what if you want to run one task after the other because one of them depends on the data of another, or simply because it makes sense to do so?

In that case, `BlockOperation` has a method that allows you to define an operation as a dependency of another. If you make `from1to10` a dependency of `from11to20`, then the number sequence will be printed in the order you expect. Listing [1-6](#532302_1_En_1_Chapter.xhtml#PC7) modifies a portion of Listing [1-5](#532302_1_En_1_Chapter.xhtml#PC6) to print the numbers in order by creating a dependency.

```
// Ensure the numbers print in order. We do this before adding the operations to the queue.
from11To20.addDependency(from1To10)
/// Add the operations to the queue
operationQueue.addOperation(from1To10)
operationQueue.addOperation(from11To20)
Listing 1-6
Adding operations as dependencies of other operations
```

It’s worth to note that while you don’t have thread management abilities with this framework, you can check the status of each *operation* (`isCancelled`, `isFinished`, etc.), and you can cancel your operations at any time (by calling `cancel` on it). If an operation gets cancelled, other operations that depended on it will also be cancelled.

Using the `NSOperation` APIs is simple, as you have evidenced. It can still be a great tool when you have simple multithreaded needs.

### Introducing async/await

`async/await` is a high-level system to write concurrent and multithreaded code. By using it, you don’t have to think about manual thread management or deadlocks . The system will take care of all those details and more for you under the hood. By using very simple constructs, you will be able to write safe and powerful concurrent and multithreaded code. It is worth noting that this system is very high level. It’s hard to use it incorrectly. While the system won’t give you fine-grain control over the concurrency and multithreading primitives, it will give you a whole set of abstractions to think of multithreaded code very differently. Semantically, it’s hard to compare `async/await` with any of the other systems we have explored before, because `async/await` is deeply integrated into Swift, the language, itself. It’s not an add-on in the form of a framework or a library. The primitives this system exposes are easier to understand thanks to Swift’s focus on readability.

To give you a quick look of what we will be studying for the rest of this book, we will rewrite Listing [1-4](#532302_1_En_1_Chapter.xhtml#PC5) in Listing [1-7](#532302_1_En_1_Chapter.xhtml#PC8) using `async/await` .

```
@MainActor
func fetchUser() async {
let userData = await userApi.fetchUserData()
usernameLabel.text = userData.username
let movies = userApi.fetchMovies(for: userData.id)
userMovies = movies
}
Listing 1-7
async/await in action
```

Listing [1-7](#532302_1_En_1_Chapter.xhtml#PC8) has gotten rid of a lot of code in Listing [1-4](#532302_1_En_1_Chapter.xhtml#PC5). You can see that the code that defers the results to the main thread is gone. You can also see that the code can be read from top to bottom – just like a normal procedural programming style would do! This version of the user data fetching API is simpler to read and simpler to write. `@MainActor` is what’s known as a *global actor*. We will explore actors and global actors later in the book. For now, know that the `@MainActor` will ensure that member updates within a function or class marked with it will run in the main thread .

By understanding when to use the system’s keywords directly (Listing [1-7](#532302_1_En_1_Chapter.xhtml#PC8) shows that `async` and `await` are keywords themselves) and the rest of the system’s primitives, you will be able to write concurrent and multithreaded code that any programmer will be able to understand. And to make things even better, if a developer is new to Apple platform development but they have experience with another technology that makes use of `async/await` , they will be able to quickly become familiar with the system. Your codebase will be cleaner and welcoming for new developers in your team.

Using `async/await` will prevent you from having to write low-level concurrent code. You will never manage threads directly. Locks are also not your responsibility at all. As you saw, this new system allows us to write expressive code, and pyramids of doom are a thing of the past.

It is important to remember that as much as a high-level system this is, you may find the extremely peculiar case in which you need a lower-level tool. That said, most developers will be able to go through their careers without finding a single use case to put `async/await` aside.

## Requirements

To follow along, you will need to download at least Xcode 13 from the App Store. If you want to use `async/await` in iOS 14 and iOS 13, you will need Xcode 13.3\. You should be comfortable reading and writing Swift code. Some examples will be written in SwiftUI so as to prevent the UI code distracting you from the actual contents of each chapter.

## Summary

Concurrency and multithreading are traditional computing problems. Doing concurrency at the lower level and managing resources yourself can be a challenge because they are easy to misuse. Misusing these resources and not getting your multithreaded code right will result in bugs in the best case and user data corruption in the worst case. Because of this, many developers, including Apple, have designed multiple tools to abstract away the details and provide easier interfaces. In the case of Apple, they have given developers the following tools (sorted from lower level to higher level):

*   `pthread` s

*   `NSThread` s

*   The Grand Central Dispatch (GCD)

*   `NSOperation` and related APIs

*   `async/await`

Most developers will never need to go down to the `pthread` or `NSThread` level . The GCD , and `NSOperation` APIs provide higher-level abstractions, whereas “async/await” not only will provide abstractions for the multithreading primitives, but it will also provide a whole system that most developers can use without even knowing what the components underneath are. This new system allows us to write shorter code that is easier to read and easier to write. It’s worth studying this new system in detail, and that’s what this book is about.

## Exercises

Answer the Questions

1.  When using traditional concurrency tools, what are some pitfalls you can fall into if you don’t use them correctly?

2.  Prior to `async/await`, what were some concurrency tools available on Apple’s platforms?

3.  What advantages does `async/await` have over other, lower-level concurrency tools?

Footnotes [1](#532302_1_En_1_Chapter.xhtml#Fn1_source)  

# 2. Introducing async/await

In the last chapter, we talked about some common problems people run into when writing concurrent and multithreaded code. We also saw some of the options Apple has for developers for this job, from lower-level alternatives that are hard to use correctly, to higher-level alternatives that are easier to use, but have less flexibility.

The rest of this book will focus on the new concurrency system which we will simply call `async/await`. In this chapter, we will explore the basic building of this new system – the `async` and `await` keywords. We will not delve into multithreading just yet. Once you understand how to use these keywords and how they behave with the APIs in the SDK you already know, you will have the right tools to learn everything this new system can do. But before we move on, let’s talk a bit more about closures and callbacks (or blocks, as they are called in Objective-C).

## Closures and Their Place in Concurrency and Multithreading

To work through this chapter, I recommend you get the sample project “Chapter [2](#532302_1_En_2_Chapter.xhtml) - Social Media App ”. First, we will talk about the existing code in the app that makes use of closure-based concurrency , and later we will modify it to use `async/await` instead.

The sample app consists of two different views.

The first view shows a simple button you can tap to authenticate with biometrics , as shown in Figure [2-1](#532302_1_En_2_Chapter.xhtml#Fig1).

![](images/532302_1_En_2_Chapter/532302_1_En_2_Fig1_HTML.jpg)

A screenshot of a smartphone depicts the time, a WIFI signal, a fully charged battery, and the word Authenticate.

Figure 2-1

Entry point of the Social Media App

And the second screen, Figure [2-2](#532302_1_En_2_Chapter.xhtml#Fig2), shows a profile view once successfully authenticated:

![](images/532302_1_En_2_Chapter/532302_1_En_2_Fig2_HTML.jpg)

A screenshot of the smartphone depicts time, a WIFI signal, a fully charged battery, and a photo of a man named Andybanez's profile with zero followers and followings.

Figure 2-2

Main profile view of the Social Media App

Upon authenticating, the app will consume two different web services : One to retrieve the user profile, and one to fetch their followers and following users counts.

Note

If you run this app in the simulator, you will need to enroll to Face ID. To do this, open the simulator, go to Feature menu ➤ FaceID, and ensure *Enrolled* is ticked. Rerun the app afterwards.

Closure-based concurrency is, at the time of this writing, present in most apps that use concurrency. Open the file *UserAPI.swift* and you will see the code in Listing [2-1](#532302_1_En_2_Chapter.xhtml#PC1), which looks all too familiar.

```
class UserAPI {
func fetchUserInfo(
completionHandler: @escaping (_ userInfo: UserInfo?, _ error: Error?) -> Void
) {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_profile.json")!
let session = URLSession.shared
let dataTask = session.dataTask(with: url) { data, response, error in
if let error = error {
completionHandler(nil, error)
} else if let data = data {
do {
let userInfo = try JSONDecoder().decode(UserInfo.self, from: data)
completionHandler(userInfo, nil)
} catch {
completionHandler(nil, error)
}
}
}
dataTask.resume()
}
//...
}
Listing 2-1
Downloading some data from a server and parsing it to show it to the user
```

This code may look intimidating to newcomers, but seasoned developers can tell what’s going on. The code above will create a `URLSessionDataTask` , which is the Foundation object we have for downloading data from the network via HTTP. We need to call `resume()` on the newly defined `dataTask` in order to actually perform a download. Once the task is finished, we check if we have an error. If we do, we call the function’s `completionHandler` by not giving it any `userInfo` and giving it an `Error` instead. If we do have some data, we will try to parse it into a `UserInfo` object. If there is an error in the parsing process, we will once again call the handler with an error. If it succeeds, we got what we wanted, and we call the handler with the newly downloaded object.

You may have seen variations of this function. For example, some developers may choose to define the function as taking two closures where one only takes the `userInfo` object and the other one only takes an error, so the closures called are different for each case. This will allow you to get rid of the optionals, but if there is common code that needs to run regardless of the case, you will have longer code. Listing [2-2](#532302_1_En_2_Chapter.xhtml#PC2) shows how this would be done.

```
func fetchUserInfo(
success: @escaping (_ userInfo: UserInfo) -> Void,
failure: @escaping (_ error: Error) -> Void
) {
//...
}
Listing 2-2
An alternative way to deal with completion handlers
```

Note

To keep the book short, we will take some shortcuts such as force-unwrapping optionals. Remember to always work with your optionals safely and responsibly.

Now, we want to download this user info and update a variable with it. Open the file `UserProfileViewModel.swift` and find the `fetchUserInfo()` function . Listing [2-3](#532302_1_En_2_Chapter.xhtml#PC3) shows what the call site looks like.

```
private func fetchUserInfo() {
let userApi = UserAPI()
print("Beginning data download")
print("Downloading User info")
self.loadingStatus = .loading
userApi.fetchUserInfo { userInfo, error in
DispatchQueue.main.async {
print("User info downloaded")
if let error = error {
self.loadingStatus = .error(error)
} else if let userInfo = userInfo {
self.loadingStatus = .idle
self.userInfo = userInfo
}
}
}
print("Ending function")
}
Listing 2-3
Calling the fetchUserInfo method
```

The code above will call the `fetchUserInfo(_)` function from our API object. It will set a `userInfo` variable from our view model if it succeeds and set an error if otherwise.

Run the app and check the print statements.

New developers may expect the console to print:

```
Beginning data download
Downloading User info
User info downloaded
Ending function
```

But reality is different, and you will see this output instead:

```
Beginning data download
Downloading User info
Ending function
User info downloaded
```

This happens because the moment the concurrent task is launched, the thread is still executing your code. It will begin printing the statements before the `fetchUserInfo` call, and because the data download is a *concurrent* call, execution for `dataTask.resume` is suspended, so the thread will execute the contents *after* the function call before the download itself finishes. When the download finishes, the contents of `dataTask` will continue executing, printing `User info downloaded` after everything else. Hypothetically, if your data download was faster than the execution time of the rest of the thread – which is impossible because you would be expecting a network call to be faster than a local CPU call – there is a chance it would print the statements in the expected order, but even then, there is no guarantee that would be the case.

Now, suppose you want to download the user info only after the user has authenticated with biometrics. Your function will look similar to Listing [2-4](#532302_1_En_2_Chapter.xhtml#PC6).

```
func authenticateAndFetchProfile() {
authenticateWithBiometrics { success in
if success {
self.isAuthenticated = true
let userApi = UserAPI()
userApi.fetchUserInfo { userInfo, error in
DispatchQueue.main.async {
if let error = error {
self.loadingStatus = .error(error)
} else if let userInfo = userInfo {
self.loadingStatus = .idle
self.userInfo = userInfo
}
}
}
}
}
}
Listing 2-4
A pyramid of doom
```

Since we depend on the biometric read status to decide whether we should download the profile or not, we need to wait for it to respond before proceeding to the user profile call. Once we get a response, our closure will execute, and the user call will work.

To make matters worse, we are not even done consuming our endpoints. We still need to consume the method that downloads the following and followers’ data. The `UserAPI.swift` file has a method `fetchFollowingAndFollowers(_)` that we can use to do just that. Listing [2-5](#532302_1_En_2_Chapter.xhtml#PC7) modifies the `authenticateAndFetchProfile`() method to download the followers and following users after fetching the main profile.

```
func authenticateAndFetchProfile() {
authenticateWithBiometrics { success in
if success {
self.isAuthenticated = true
let userApi = UserAPI()
userApi.fetchUserInfo { userInfo, error in
DispatchQueue.main.async {
if let error = error {
self.loadingStatus = .error(error)
} else if let userInfo = userInfo {
self.userInfo = userInfo
userApi.fetchFollowingAndFollowers { followingFollowers, error in
if let error = error {
self.loadingStatus = .error(error)
} else {
self.followersFollowing = followingFollowers
}
}
}
}
}
}
}
}
Listing 2-5
An even bigger pyramid of doom
```

You might think that the code in Listing [2-5](#532302_1_En_2_Chapter.xhtml#PC7) is an exaggeration, but there really are codebases like that. Some developers go around it by using tricks with semaphores to force the API calls to get called one after the other. Other developers may opt to use `NSOperationQueue` for this. Some will use the GCD. Others may just find it pragmatic to just separate this into multiple function calls, but separating it into different function calls will make it hard to keep track of the code if certain operations depend on others.

With the new `async/await` system, you will be able to scratch closures completely for this specific purpose, and you will be able to write code that more closely resembles procedural programming. It will also help you and the rest of your team define guidelines for function signatures so there are no mismatches like there are between the function signatures of Listing [2-1](#532302_1_En_2_Chapter.xhtml#PC1) and Listing [2-2](#532302_1_En_2_Chapter.xhtml#PC2).

## Getting Started with async/await

Recall that in procedural programming, code is executed from top to bottom, in a logical order. If you go back to Listing [2-3](#532302_1_En_2_Chapter.xhtml#PC3), you will realize that’s not what’s happening here thanks to the order the `print` statements are called in.

Wouldn’t it be great if instead of passing data through closures we could pass it via standard `return` statements from functions? This is exactly what the new system allows us to do. By getting rid of the closures , you can write natural code that will allow you to express your intentions easier, making it clearer for future developers (including your future self) to better grasp what the codebase is doing.

Thinking procedurally, we can summarize the tasks of our Social Media App as a three-step program:

1.  `First authenticate the user with biometrics.`

2.  `Then fetch their user profile.`

3.  `Finally, fetch the user's followers and following users.`

With closures, this is spaghetti code. With async/await, we can write those three statements as-is, almost literally. We will convert our closure-based code into async/await, but before we do, let’s tackle some theory.

### The async keyword

The core of the system are the `async` and `await` keywords . It is crucial to understand how to use them to use the rest of the system.

Functions that can be run asynchronously, or that can run at the same time alongside other functions, have the `async` keyword before the return type of a function. This will tell the compiler and other programmers that your function is asynchronous. Listing [2-6](#532302_1_En_2_Chapter.xhtml#PC8) shows what an `async` declaration looks like.

```
func fetchUserInfo() async throws -> UserInfo { /*...*/ }
Listing 2-6
An async function declaration
```

The declaration on Listing [2-6](#532302_1_En_2_Chapter.xhtml#PC8) will be the base of migrating our closure-based code into `async/await` . We have not even written much code yet, and we can already see some benefits. The completion handler is completely gone. If your function can throw errors, as is the case of our original `fetchUserInfo` method, you can mark the `async` function as `throws`, which will make error handling more natural. Finally, this function will return a `UserInfo`, which is not an optional, so you save yourself from having to unwrap the object and potentially other checks.

In order to call an `async` function you need to be inside a *concurrent context* . There are a few ways to create such contexts:

*   The body of an `async` function is a concurrent context by itself. If a function is not marked as `async`, it cannot call `async` functions directly. Listing [2-7](#532302_1_En_2_Chapter.xhtml#PC9) will not compile.

```
func authenticateAndFetchProfile() {
//...
let userProfile = try await fetchUserInfo()
//...
}
Listing 2-7
Attempting to call an async function outside an async context
```

Marking the function as async will allow it to compile. Listing [2-8](#532302_1_En_2_Chapter.xhtml#PC10) fixes the code to get it to compile.

```
func authenticateAndFetchProfile() async {
//...
let userProfile = try await fetchUserInfo()
//...
}
Listing 2-8
Creating an async context with the async keyword
```

But what happens if we don’t have a concurrent context ? Somewhere up there in your call hierarchy, you will find yourself in a situation in which there’s no concurrent context at all to run code in. Luckily the other method to create async contexts will help us deal with this problem.

*   By creating a `Task` object. `Task` is a very powerful object, and it will allow us to do multithreading later. We will also be able to have some control over them, such as cancelling them. We will explore such features in later chapters. For now, we will just use `Task` to create a concurrent context . `Task` has an initializer with a closure, and that closure itself is a concurrent context. Listing [2-9](#532302_1_En_2_Chapter.xhtml#PC11) shows how to use `Task`.

```
func authenticateAndFetchProfile() {
Task {
//...
let userProfile = try await fetchUserInfo()
//...
}
}
Listing 2-9
Creating an async context with Task
```

Even though `authenticateAndFetchProfile()` is not marked as async, creating a Task within it will allow us to run async functions.

### The await keyword

Every call to an async function must be paired up with the `await` keyword at some point. `await` itself has special semantics, and it’s not just syntax to call `async` functions.

If during your code’s execution the `await` keyword is encountered, your program can choose to *suspend* the currently executing function. For this reason, the `await` keyword is also commonly known as a *suspension point* . This is a decision the system will make, and you have no control over it. When we say it may *suspend*, we mean that the current execution will be paused and the `async` function will continue executing somewhere else. No code will be executed in your current function if it is suspended.

To better understand suspension , Listing [2-10](#532302_1_En_2_Chapter.xhtml#PC12) will modify Listing [2-8](#532302_1_En_2_Chapter.xhtml#PC10) to add some `print` statement before and after the `await` ed call.

```
func authenticateAndFetchProfile() async {
//...
print("Fetching user info")
let userProfile = try await fetchUserInfo()
print("User info fetched")
//...
}
Listing 2-10
Adding print statements will cause them to print their contents in order
```

Listing [2-10](#532302_1_En_2_Chapter.xhtml#PC12) will print `Fetching user info`, and after a little while, it will print `User info fetched`. This is actually the order the statements were written in, and you didn’t have to dabble with any closures or anything to get that output. Nice!

What’s happening during an `await` call that suspends is that you are returning control to the system, and as such, you have no control over when you will get control back. The same thread that called `fetchUserInfo()` will be free to do other work, and that other work is none of your concern since it’s handled by the system. It could be another task within your app, or it would be outside of your realm. Until control is returned to you, you cannot continue executing statements. For this reason, the code in Listing [2-10](#532302_1_En_2_Chapter.xhtml#PC12) will print the statements in the order they are written in, but it may take a little bit to print `User info fetched` as you have handled the thread to the system, and downloading the user info is a lengthy task. When the user info has finished download, a `return` statement will be reached within the `fetchUserInfo` call, and this statement is what will return control back to you. Note that it is up to the system to decide when it will give you back control, and it doesn’t happen immediately after hitting the `return` statement. The system may be executing other concurrent tasks and it needs to sort out its priorities before coming back to you.

In summary, after hitting a suspension point (`await` keyword ), if the system chooses to suspend, nothing underneath it will be executed until control is given back to you. While suspension takes place, the system is using that thread to do other work.

Anything that happens after an `await` call is a *continuation* . We need to continue doing the work we were doing before we were suspended. This is important, because it’s possible that when we get control back, the statements in our code underneath the `await` may run in a different thread. In Listing [2-10](#532302_1_En_2_Chapter.xhtml#PC12), it means that the first `print` statement may run in a thread, and the second `print` statement may run in an entirely different thread. Using `async/await` does not save you from having to do UI work on the main thread, so if you need to update your UI, you need to defer that work to the main thread. In practice, this is done through a system-wide global actor called `@MainActor`. We will talk more about global actors in a later chapter.

With all this theory in place, we can now get our feet wet. We will rewrite the closure-based code to use `async/await` instead.

### Using async/await

All of Apple’s closure-based concurrency APIs have an `async/await` version you can use on iOS 15 and up. While with Xcode 13.3 you can use `async/await` on iOS versions as low as iOS 13 , Apple’s native APIs with `async/await` support are only available on iOS 15 and above.

We will now begin migrating all our closure-based code into `async/await`. You can download the finished project from “Chapter [2](#532302_1_En_2_Chapter.xhtml) - Social Media App (Async-Await)”.

Let’s start with the Shared ➤ API ➤ UserAPI.swift file.

We will first migrate the `fetchUserInfo` `(completionHandler:)`. So create a new function with the signature in Listing [2-11](#532302_1_En_2_Chapter.xhtml#PC13).

```
func fetchUserInfo() async throws -> UserInfo {
}
Listing 2-11
Basic signature for the async/await version of fetchUserInfo
```

The function in Listing [2-11](#532302_1_En_2_Chapter.xhtml#PC13) can be marked as `throws`. In general, if you are modeling code that can throw errors or return a valid value, you can simply mark your method as `throws` and then assign the result type as the object you would get back if the call was successful. Essentially, the contents of the would-be completion handler closure have been moved around in the function signature.

Right off the bat, you can tell that `async/await` helps us write more semantic code. In Swift, we can throw errors from functions, yet surprisingly many developers don’t write code like this. This is because most non-trivial apps are concurrent by nature (e.g., consuming network calls), and because we can’t handle errors from a closure in an idiomatic way, our only solution is to return errors as part of closures . With `async/await`, our function signatures are more readable and hence more self-documented.

We will now make use of the first `async/await` API Apple provides us with iOS 15 .

All the `URLSession` methods that return data have `async/await` variants. In Listing [2-12](#532302_1_En_2_Chapter.xhtml#PC14), we will implement the logic that will return us the user info JSON as `Data`.

```
func fetchUserInfo() async throws -> UserInfo {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_profile.json")!
let session = URLSession.shared
let (userInfoData, response) = try await session.data(from: url)
}
Listing 2-12
Network calls with async/await
```

So far so good, but this code won’t compile yet because we are not returning anything. I want you to appreciate the fact that the call to `session.data(from:)` does not take a closure at all. You also don’t need to call `resume()` anymore – as soon as you call the async version of `data(from:)`, the task will begin.

We now need to parse the JSON into an object that our API consumers can use. Listing [2-13](#532302_1_En_2_Chapter.xhtml#PC15) will complete the method with the JSON parsing and return a value.

```
func fetchUserInfo() async throws -> UserInfo {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_profile.json")!
let session = URLSession.shared
let (userInfoData, response) = try await session.data(from: url)
let userInfo = try JSONDecoder().decode(UserInfo.self, from: userInfoData)
return userInfo
}
Listing 2-13
Consuming a network request, parsing its results as JSON, and returning an object procedurally from ‘async/await’ code
```

And that’s it. That’s the entire `async` version of `fetchUserInfo`. We have turned a 15-line code block into a mere 5 (although it’s hard to appreciate in the book!). Thanks to Listing [2-13](#532302_1_En_2_Chapter.xhtml#PC15), we can really appreciate the elegance of `async/await`.

Let’s take this chance to recap everything we have learned about `async/await` so far using this example.

First, we declared the `fetchUserInfo` method as `async`. Note that since Swift supports method overloading, both `fetchUserInfo()` and `fetchUserInfo(completionHandler:)` can exist side by side. By marking the function as `async`, we have created a concurrent context where we can call other `async` functions, and callers to our function need to be within concurrent contexts themselves (either they need to be marked as `async` themselves, or within a `Task {}` call).

When execution reaches the `let (userInfoData, response) = try await session.data(from: url)` line, our code is suspended, and the thread it’s executing on is free to do other work – possibly unrelated to our app. Thanks to this suspension , the line that follows, which converts the data into an object, will not execute since we depend on the data from the line above. When `session.data(from:)` has finished downloading its contents, it will let the system know it’s ready to return to the caller. The system will eventually give your app a heads-up by returning and assigning the `(userInfoData, response)` variables. After the system returns and those variables have values, the code will continue executing in the order it was written, by parsing the JSON, and later returning that object to the caller. It is procedural programming in its purest form.

Naturally, it is also possible that the code will throw an error. The device may not have internet connectivity, or the server may be down. If an error occurs, you can intuitively tell what will happen because it’s exactly the same as if the code was not asynchronous: An error (an object that conforms to `Error`) will be thrown (hence we marked the function as `throws)` by `session.data(from:)`, and all the lines underneath will not be executed. For this reason, in this example, the caller will have to call `fetchUserInfo()` with a `try`, `try?`, or `try!` call (in this case, `try!` is not recommended at all).

If you understand all that, feel free to continue. It’s important to understand how `async/await` works at the most basic level to make use of all its features, so feel free to take a break if you need it.

You may notice the code has a warning at this point, on the following line:

```
let (userInfoData, response) = try await session.data(from: url)
```

`session.data(from:)` returns a tuple, so we can decompose it into two different variables. Since you are not using the `response` variable, you can replace it with an underscore (`_`), but the reason I put it there is to show that, if the API has other requirements (it requires you to inspect headers, or to work with HTTP status codes in different ways), you can use this variable, which is of type `URLResponse`. You would need to cast it to `HTTPURLResponse` to get anything meaningful out of it.

Moving on, Listing [2-14](#532302_1_En_2_Chapter.xhtml#PC17) is the `async/await` version of the method that fetches the followers/following info.

```
func fetchFollowingAndFollowers() async throws -> FollowerFollowingInfo {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_followers.json")!
let session = URLSession.shared
let (data, _) = try await session.data(from: url)
let followingFollowers = try JSONDecoder().decode(FollowerFollowingInfo.self, from: data)
return followingFollowers
}
Listing 2-14
async/await version of the method that fetches followers and following users
```

Note

There are wonderful ways to abstract away and avoid code reuse in these calls, but they are out of the scope of this book.

Now, let’s go to Shared ➤ Views ➤ UserProfileView ➤ UserProfileViewModel.swift.

Listing [2-15](#532302_1_En_2_Chapter.xhtml#PC18) will begin by adding the `@MainActor` attribute to the class.

```
@MainActor
class UserProfileViewModel: ObservableObject {/*..*/}
Listing 2-15
Adding the @MainActor global actor to a class declaration
```

We have had brief discussions on Global Actors before, and Listing [2-15](#532302_1_En_2_Chapter.xhtml#PC18) uses the `@MainActor` global actor in the real world for the first time. This will ensure that whenever a property is updated, it will happen on the main thread. This is important, because SwiftUI uses Observables to update the UI, and thus updating them needs to be done on the main thread.

The easiest method to migrate is `authenticateWithBiometrics(completionHandler:),` so we will start with that. Apple also provides `async` versions of these APIs. Listing [2-16](#532302_1_En_2_Chapter.xhtml#PC19) shows how to use the async version of `authenticateWithBiomatrics` .

```
private func authenticateWithBiometrics() async -> Bool {
let context = LAContext()
do {
let success = try await context.evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, localizedReason: "To fetch your profile")
return success
} catch {
return false
}
}
Listing 2-16
async version of authenticateWithBiometrics
```

This call may throw an error, but it may not make sense for the consumers of our API to concern themselves with those details. In this case, if any errors occur, they will be handled internally, returning `false` to our caller. Therefore, we don’t mark the method as `throws`.

Listing [2-17](#532302_1_En_2_Chapter.xhtml#PC20) will create an async version of fetchUserInfo.

```
private func fetchUserInfo() async {
do {
let userApi = UserAPI()
self.loadingStatus = .loading
print("Beginning data download")
print("Downloading User info")
self.userInfo = try await userApi.fetchUserInfo()
print("User info downloaded")
self.loadingStatus = .idle
} catch {
self.loadingStatus = .error(error)
}
print("Ending function")
}
Listing 2-17
async version of fetchUserInfo
```

Listing [2-17](#532302_1_En_2_Chapter.xhtml#PC20) is a very interesting piece of code. I’m keeping the print statements here just to show that they will be printed in the order you wrote them. Once again, a tiny shoutout to procedural programming.

It is also worth noting that we do not need to do anything special to go back to the main thread. It is important that the assignment of the `self.userInfo` and `self.loadingStatus` run on the main thread, because they are directly linked to your UI. Because we marked the class as `@MainActor` , all the updates to the properties will run on the main thread. If you don’t mark the entire class as `@MainActor`, you can mark this method only.

Let’s continue working through. We will now write the async version of `fetchFollowingFollowers()`. Listing [2-18](#532302_1_En_2_Chapter.xhtml#PC21) offers such implementation .

```
private func fetchFollowingFollowers() async {
do {
let userApi = UserAPI()
self.loadingStatus = .loading
self.followingFollowersInfo = try await userApi.fetchFollowingAndFollowers()
self.loadingStatus = .idle
} catch {
self.loadingStatus = .error(error)
}
}
Listing 2-18
async version of fetchFollowingFollowers()
```

This piece of code is very similar to `fetchUserInfo`, so there shouldn’t be anything new here.

Finally, we will create the async version of `authenticateAndFetchProfile()` which does everything for us, one step after the other: Authenticate, and if successful, retrieve the user profile, followed by the follower and following info. Listing [2-19](#532302_1_En_2_Chapter.xhtml#PC22) provides an initial implementation of it.

```
func authenticateAndFetchProfile() async {
isAuthenticated = await authenticateWithBiometrics()
if isAuthenticated {
await fetchUserInfo()
await fetchFollowingFollowers()
}
}
Listing 2-19
authenticateAndFetchProfile() calls multiple async methods
```

This new method is much shorter than the original `authenticateAndFetchProfile(completionHandler)` method. Not only can you appreciate the shortness, once again the lack of completion handlers makes our code very easy to read. We also no longer have the pesky `DispatchQueue.main.async` calls that nested our code even further because the `@MainActor` will ensure that the property changes that trigger UI updates run on the main thread.

Our method is now `async`, but doing this implies that in SwiftUI we will need to use a `Task` when our “Authenticate” button is pressed. Open Shared ➤ Views ➤ UserProfileView ➤ UserProfileView.swift, find the “Authenticate” button (line 52), and wrap the call to `viewModel.authenticateAndFetchProfile()` in a `Task`. Listing [2-20](#532302_1_En_2_Chapter.xhtml#PC23) shows how this is done.

```
Button {
Task {
await viewModel.authenticateAndFetchProfile()
}
} label: {
if viewModel.availableBiometricType == .none {
Text("Authenticate")
} else {
Label {
Text("Authenticate")
} icon: {
Image(systemName: imageNameForBiometricType(viewModel.availableBiometricType))
}
}
}
.buttonStyle(.borderedProminent)
Listing 2-20
It’s possible to use Task within SwiftUI
```

Because SwiftUI itself does not run in an async context, it is important that we create one for each functionality that requires it. In this case, `authenticateAndFetchProfile()` is an async task.

We are almost done, but before we wrap up, you may have noticed that our ‘async’ versions of `fetchUserProfile()` and ‌`fetchFollowingFollowers()` are very similar. In this particular case, we can get rid of even more code by getting rid of these two functions entirely and moving their contents to

`authenticateAndFetchProfile()` `.` Listing [2-21](#532302_1_En_2_Chapter.xhtml#PC24) provides a new version for `authenticateAndFetchProfile()` which will be slightly longer than the original, but it will make more sense to us as it has gotten rid of two methods that were superfluous.

```
func authenticateAndFetchProfile() async {
isAuthenticated = await authenticateWithBiometrics()
if isAuthenticated {
do {
let userApi = UserAPI()
loadingStatus = .loading
userInfo = try await userApi.fetchUserInfo()
followingFollowersInfo = try await userApi.fetchFollowingAndFollowers()
self.loadingStatus = .idle
} catch {
loadingStatus = .error(error)
}
}
}
Listing 2-21
A different implementation for authenticateAndFetchProfile()
```

The lack of completion handlers allows us to have one-liners for fetching the user profile and following and followers data. Because of this, we can eliminate the functions we had created for these purposes.

And we are done! We have transformed all our completion-handler-based code into `async/await`. Keep in mind that in this case it was very easy to do so, because our app is small. There are techniques we can use to accelerate the migration in bigger projects, and we will explore them when we talk about *Continuations* in the next chapter.

### async get properties

`async/await` is not limited to function calls. You can also use it in read-only calculated properties . Listing [2-22](#532302_1_En_2_Chapter.xhtml#PC25) shows how this can be done, but we will not be using this in our Social Media App .

```
var userInfo: UserInfo {
get async throws {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_profile.json")!
let session = URLSession.shared
let (userInfoData, _) = try await session.data(from: url)
let userInfo = try JSONDecoder().decode(UserInfo.self, from: userInfoData)
return userInfo
}
}
Listing 2-22
Using async get properties
```

In Listing [2-22](#532302_1_En_2_Chapter.xhtml#PC25), we declare a property that can be both `async` and `throws`. In general I do not recommend you use this, unless you implement some sort of caching mechanism. Otherwise, consumers of your API may access the property much more often than planned and that will result in significant delays in their apps. There may be cases in which it makes sense to avoid a caching mechanism, but you will have to analyze it on a case-by-case basis.

### async/await in iOS 13 and iOS 14

Earlier, we mentioned that while the `async/await` system works on iOS 13 and iOS 14 , Apple doesn’t have any APIs that are marked as such. This means that if you think about it, there’s nothing you can use `async/await` on in these versions. You may be wondering what the point is to have `async/await` support in these versions if Apple doesn’t offer any `async/await` APIs in them to begin with. If you need to support those versions, I will teach you a trick to have the `async/await` variants of those methods when we talk about *Continuations* in the next chapter.

## Summary

In this chapter, you learned how to use the basic building blocks of the `async/await` system, which are the keywords the system takes its name from. It’s very important to understand how `async` and `await` work in tandem in order to use all the features of this new system.

We also explained the main differences between closure-based concurrency and `async/await`. We showed how this system works with suspensions and how these suspensions behave very different than what we expect to see in completion handlers or closures.

## Exercises

Coding Exercise

Download the “Chapter [2](#532302_1_En_2_Chapter.xhtml) Exercise” project. This is a HealthKit project that reads data from the Health app and displays it in the app. This app is currently using closure-based calls. Having in-depth HealthKit knowledge is not necessary to complete this exercise.

1.  Refactor the `HealthManager` class (in the HealthManager.swift, file) to use `async/await` instead of closure-based calls. Remove the closure-based methods once you are done. Hint: iOS 15 provides a new `HKSampleQueryDescriptor` object which supports Swift Concurrency. Alongside it, you will need to use a new `HKSamplePredicate<HKSample>` object for the retrieval logic.

2.  Refactor `ContentViewViewModel` (in the ContentViewViewModel.swift file) to use your `async/await` methods instead of the closure-based calls.

    The “Chapter [2](#532302_1_En_2_Chapter.xhtml) Exercise - Solved async-await” project contains the answers.

Note

I recommend you run this exercise on a physical device. While it will work on a simulator, you will need to add data manually to the Health app on it. Your iPhone is likely to already be storing this info.

# 3. Continuations

In the last chapter, we learned about the `async` and `await` keywords to get started with the new concurrency system in Swift. We migrated a project that was using closure-based calls into `async/await` code. We did so by rewriting the original implementations of the methods to adapt them to the new system. But in the real world, you may not have the luxury of writing such implementations from scratch due to time constraints, or worse, technical difficulties. For these scenarios, we have *Continuations*, which will allow to “wrap” existing closure-based calls into async/await – and even delegate-based ones!

Continuations are useful, because not only will you be able to provide `async/await` versions of your own closure-based code , but also for third-party libraries and frameworks. This will help you create a consistent codebase that only uses async/await instead of closure-based calls when it makes sense to do so.

Note

Not all closure-based code is necessarily so because it deals with concurrency and/or multithreading. There are multiple calls in both Swift and Apple’s SDKs that take closures and have nothing to do with concurrency. For example, the popular methods of collections, `filter`, `map`, and `reduce`, take closures to operate on their elements. Keep this in mind because it’s very hard to have projects that don’t have closures of any kind, and that’s fine. `async/await` aims to eliminate closure-based calls for concurrency-related tasks only.

## Understanding Continuations

We have briefly touched the concepts of *Continuations* in previous chapters, and we will work through this chapter keeping that same meaning.

In short, a *Continuation* is whatever happens after an `async` call is finished. When we are using `async/await` , a continuation is simply everything below an `await` call. If you are using closure-based code , the continuation is the code written within your completion handlers. And if you are using delegate-based code , the continuation would be whatever methods can be called after an action is done, such as the original `UIImagePickerController`’s `imagePickerController(didFinishPickingMediaWithInfo:)` method.

The new concurrency system allows us to “convert” a continuation in the shape of closure-based code and even delegate-based code into `async/await`.

To better understand this short – but important – concept, we will go back to the original version of the Social Media App. We will use the version that doesn’t have `async/await` to illustrate this concept.

### Converting closure-based calls into async/await

Open the Shared ➤ API ➤ UserAPI.swift file. Take a look at the `fetchUserInfo(completionHandler:)` method. For your convenience, Listing [3-1](#532302_1_En_3_Chapter.xhtml#PC1) shows the original implementation.

```
func fetchUserInfo(
completionHandler: @escaping (_ userInfo: UserInfo?, _ error: Error?) -> Void
) {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_profile.json")!
let session = URLSession.shared
let dataTask = session.dataTask(with: url) { data, response, error in
if let error = error {
completionHandler(nil, error)
} else if let data = data {
do {
let userInfo = try JSONDecoder().decode(UserInfo.self, from: data)
completionHandler(userInfo, nil)
} catch {
completionHandler(nil, error)
}
}
}
dataTask.resume()
}
Listing 3-1
Original implementation for fetchUserInfo(completionHandler:)
```

You can provide an `async/await` version for this method without reimplementing it from scratch like we did in Chapter [2](#532302_1_En_2_Chapter.xhtml). The disadvantage of doing this is that you won’t be able to delete this original implementation. The advantage is that keeping the original implementation is not so bad, because you will always have the closure-based call for codebases that don’t want to adopt async/await yet. Listing [3-2](#532302_1_En_3_Chapter.xhtml#PC2) shows we can provide an async/await version of this method without writing it again.

```
func fechUserInfo() async throws -> UserInfo {
// (1)
try await withCheckedThrowingContinuation { continuation in
// (2)
fetchUserInfo { userInfo, error in
// (3)
if let userInfo = userInfo {
continuation.resume(returning: userInfo)
} else if let error = error {
continuation.resume(throwing: error)
} else {
// Throw a generic error.
let nsError = NSError(domain: "com.socialmedia.app", code: 400)
continuation.resume(throwing: nsError)
}
}
}
}
Listing 3-2
Using withCheckedThrowingContinuation
```

Listing [3-2](#532302_1_En_3_Chapter.xhtml#PC2) essentially wraps closure-based code within a *Checked Continuation.* `withCheckedThrowingContinuation` – and other variants we will explore in a bit – is a method that provides a closure with a parameter. The parameter it gives you is the actual continuation, and you are responsible for resuming it when your asynchronous call with the closure is finished. In Listing [3-1](#532302_1_En_3_Chapter.xhtml#PC1), the continuation is of type `CheckedContinuation<T, E>`, meaning that we can resume the continuation with either an object or an error. This makes perfect sense because anything with a network call always has a chance of failing.

Let’s walk through Listing [3-2](#532302_1_En_3_Chapter.xhtml#PC2) step by step:

*   `(1)` will call `withCheckedThrowingContinuation` . This is an `async` function that can throw. For this reason, we call it with `try await`, and the function signature is similar to the async version of this code we saw in Chapter [2](#532302_1_En_2_Chapter.xhtml). When calling this method, we need to pass it a closure that will provide us with a continuation object we need to resume after it’s done.

*   `(2)` will call the closure-based version of `fetchUserInfo(completionHandler:)` as you would call it in any other context.

*   `(3)` is where the magic happens. If `fetchUserInfo(completionHandler:)` finishes successfully and we have a `UserInfo` object, we will resume the continuation by calling `continuation.resume(returning: userInfo).` If we get an error, we will instead call `continuation.resume(throwing: error).` When calling the `continuation.resume(returning:)` method, it will cause the caller of the `async` version of `fetchUserInfo()` to return the resulting object, and calling `continuation.resume(throwing:)` will cause it to throw an error instead. In the end, the caller of the method in Listing [3-2](#532302_1_En_3_Chapter.xhtml#PC2) will behave the same as the caller in Listing [2-10](#532302_1_En_2_Chapter.xhtml#PC12) all the way back in Chapter [2](#532302_1_En_2_Chapter.xhtml). Keep in mind that when using continuations*, you* ***must*** *call the continuation at some point exactly once.* You cannot forget to call it, and you cannot call it more than once. If you forget to call it, you will be in a deadlocked state.

Alongside `withCheckedThrowingContinuation` , we have three additional methods we can use to create this type of continuations:

*   `withCheckedContinuation`

*   `withUnsafeThrowingContinuation`

*   `withUnsafeContinuation`

Use `withCheckedContinuation` when the original closure-based code does not give you back an error of any kind.

The unsafe variants – `withUnsafeThrowingContinuation` and `withUnsafeContinuation` – are like their Checked counterparts. The main difference is, when working with Checked Continuations , Swift will help protect you by logging errors when any violations are encountered – calling continuations more than once or forgetting to call them completely. Unsafe continuations do not have these features, but they could be slightly faster. In general, I do not recommend you use Unsafe continuations , but be aware that you can use them interchangeably in most situations.

### Converting delegate-based code into async/await

Converting delegate-based code into `async/await` is a more involved process since these types of APIs may call different delegate methods depending on what kind of events are happening. This will create naturally “jumpy” code. While the calls may happen in the same thread, they can be unpredictable because of the way they call their methods. For example, the *ContactsUI* framework offers an object, `CNContactPickerViewController` , which notifies you when the user chooses a contact in one method, and if they cancel the selection, a different method will be called.

For this section, download the “Chapter [3](#532302_1_En_3_Chapter.xhtml) - Contact Picker (Base Project)” project.

#### The ContactPicker Project

This project is very simple (Figure [3-1](#532302_1_En_3_Chapter.xhtml#Fig1)). It consists of a top label with a static prompt “Select a Contact”. Underneath it, we have a button, “Select Contact” that, when tapped, will present a `CNContactPickerViewController` that our users can use to select a contact. Once a contact is selected, a new label will appear underneath the button with a black text showing the contact’s name. If they cancel the operation, the label will be red instead, and it will show the text “Cancelled”.

![](images/532302_1_En_3_Chapter/532302_1_En_3_Fig1_HTML.png)

A screenshot depicts the contact picker, which includes the labels Wi Fi, full battery, and time 9:06, as well as the format, select a contact, below select contact button, and John Appleseed.

Figure 3-1

The ContactPicker app

Open the `ViewController.swift` file . The core of this example lies in the methods in Listing [3-3](#532302_1_En_3_Chapter.xhtml#PC3).

```
func contactPickerDidCancel(_ picker: CNContactPickerViewController) {
selectedContactLabel.text = "Cancelled"
selectedContactLabel.textColor = .red
selectedContactLabel.isHidden = false
}
func contactPicker(_ picker: CNContactPickerViewController, didSelect contact: CNContact) {
let nameFormatter = CNContactFormatter()
nameFormatter.style = .fullName
let contactName = nameFormatter.string(from: contact)
selectedContactLabel.text = contactName
selectedContactLabel.textColor = .black
selectedContactLabel.isHidden = false
}
Listing 3-3
CNContactPickerViewController delegate methods
```

When our “Select Contact” button is tapped, the `promptContact()` method will be called. This will create, configure, and show a `CNContactPickerDelegate`. If the user cancels the operation, the `contactPickerDidCancel(_)` method will be called, setting the label to red with the text “Cancelled”. If the user selects a contact, the `contactPicker(_:didSelect)` method will be called instead, setting the label text to the contact name and its color to black. This is a simple example, but it illustrates the complexity of delegate-based calls.

Delegate-based APIs can be very messy, especially if they have a lot of possible method calls. We can create a more streamlined API for our callers by creating a wrapping object around `CNContactPickerViewControllerDelegate` and its methods.

#### Wrapping delegate-based calls in async/await alternatives

We will now proceed to create such a wrapper. We will modify the “Chapter [3](#532302_1_En_3_Chapter.xhtml) - ContactPicker (Base Project)” project. You can download the “Chapter [3](#532302_1_En_3_Chapter.xhtml) - ContactPicker (Async-Await)” project to see the final result.

Our task is to replace the current implementation of `promptContact()` , shown in Listing [3-4](#532302_1_En_3_Chapter.xhtml#PC4), with the new implementation in Listing [3-5](#532302_1_En_3_Chapter.xhtml#PC5), and getting rid of all the contact selection logic in `ViewController` along the way, such as the delegate conformance and delegate methods.

```
func promptContact() {
async {
let contactPicker = ContactPicker(viewController: self)
if let contact = await contactPicker.pickContact() {
// Contact selected…
} else {
// Selection Cancelled…
}
}
}
Listing 3-5
A new implementation for promptContact()
```

```
func promptContact() {
let picker = CNContactPickerViewController()
picker.delegate = self
picker.displayedPropertyKeys = [CNContactGivenNameKey, CNContactNamePrefixKey, CNContactNameSuffixKey]
present(picker, animated: true)
}
Listing 3-4
Original implementation for promptContact()
```

In Listing [3-5](#532302_1_En_3_Chapter.xhtml#PC5), we safely unwrap an optional, because if the user tapped Cancel instead of selecting a contact, we will have no contact information to show. You could alternatively throw an error when the user cancels the selection, but because it’s the only possible failure reason in this project, we will not use errors.

We will start by creating a new object. Create a new file, `ContactPicker.swift`, and add the code in Listing [3-6](#532302_1_En_3_Chapter.xhtml#PC6).

```
import Foundation
import ContactsUI
class ContactPicker: NSObject, CNContactPickerDelegate {
}
Listing 3-6
Starting ContactPicker
```

We will use this object to wrap all the logic of the contact picker. It will be responsible for showing the contact picker UI and returning the result to us.

Next, we need the basic properties alongside a typealias we will use to refer to the continuation type, as shown in Listing [3-7](#532302_1_En_3_Chapter.xhtml#PC7).

```
private typealias ContactCheckedContinuation = CheckedContinuation
private unowned var viewController: UIViewController
private var contactContinuation: ContactCheckedContinuation?
private var picker: CNContactPickerViewController
Listing 3-7
Basic properties
```

The `viewController` property will present the UI. We need to store the `picker` to show it when we call `promptPicker()`. Finally, because there are two different delegate methods , we need to store the checked continuation , so we can reference it when either delegate method is called.

The initializer should take the view controller that will present the picker. Listing [3-8](#532302_1_En_3_Chapter.xhtml#PC8) has the implementation for the initializer.

```
init(viewController: UIViewController) {
self.viewController = viewController
picker = CNContactPickerViewController()
super.init()
picker.delegate = self
}
Listing 3-8
The initializer for ContactPicker
```

The initializer takes a single property (the ViewController responsible from showing the picker). The picker is internal, so there’s no need to expose it. We will initialize the continuation when `promptContact()` is tapped.

`promptContact()` has a very interesting implementation. Listing [3-9](#532302_1_En_3_Chapter.xhtml#PC9) shows what we need to get this method to work.

```
@MainActor
func pickContact() async -> CNContact? {
viewController.present(picker, animated: true)
return await withCheckedContinuation({ (continuation: ContactCheckedContinuation) in
self.contactContinuation = continuation
})
}
Listing 3-9
The async version of pickContact()
```

We are marking this method with the `@MainActor` and not the whole class. This is because the `ContactPicker` class could be doing all sorts of different things in different threads, but we only care about the `MainActor` in this method because it interacts with the UI by presenting the contact picker modally.

The body of `withCheckedContinuation` does nothing but assign the continuation to our `contactContinuation` property. Again, we need to store the continuation somehow because we can resume it from two different delegate methods.

Listing [3-10](#532302_1_En_3_Chapter.xhtml#PC10) will implement the delegate methods we need: `contactPicker(_:didSelect)` and `contactPickerDidCancel(_)` .

```
@MainActor
func contactPicker(_ picker: CNContactPickerViewController, didSelect contact: CNContact) {
contactContinuation?.resume(returning: contact)
contactContinuation = nil
picker.dismiss(animated: true, completion: nil)
}
@MainActor
func contactPickerDidCancel(_ picker: CNContactPickerViewController) {
contactContinuation?.resume(returning: nil)
contactContinuation = nil
}
Listing 3-10
Resuming the continuation from two different delegate methods
```

These methods are very straightforward. Both methods will resume the continuation, either passing a contact when selecting it, or passing in nil if no contact is selected (the selection has been cancelled). Assigning the `contactContinuation` property to nil after resuming it will ensure that we cannot call the continuation more than once.

We have finished implementing the `ContactPicker` class, so now we need to go back to `ViewController`. You can remove the `CNContactPickerDelegate` conformance and its associated delegate methods . You can also replace the `promptContact()` implementation with the code in Listing [3-11](#532302_1_En_3_Chapter.xhtml#PC11).

```
func promptContact() {
Task {
let contactPicker = ContactPicker(viewController: self)
if let contact = await contactPicker.pickContact() {
let nameFormatter = CNContactFormatter()
nameFormatter.style = .fullName
let contactName = nameFormatter.string(from: contact)
selectedContactLabel.text = contactName
selectedContactLabel.textColor = .black
selectedContactLabel.isHidden = false
} else {
selectedContactLabel.text = "Cancelled"
selectedContactLabel.textColor = .red
selectedContactLabel.isHidden = false
}
}
}
Listing 3-11
The final implementation for pickContact()
```

`ViewController` is now significantly smaller. Tapping the Choose Contact button should behave exactly as it did before we changed it all to use `async/await`.

Note

While it is wonderful that we managed to offer an async/await version for a delegate-based call, you should consider if it’s *really* necessary to do so. Sometimes, the complexity of the delegate-based calls (CoreBluetooth, Core Location) will not be worthy to offer async/await versions for them. With that said, it’s a good exercise to work on to better understand how continuations work.

### Supporting async/await in iOS 13 and 14

We mentioned this before, but it’s worth mentioning it again: While with Xcode 13.3 you can use `async/await` , Apple doesn’t offer any APIs that use them unless you compile with the iOS 15 SDK or above.

If you open the async/await version of our Social Media App from Chapter [2](#532302_1_En_2_Chapter.xhtml) (feel free to download the “Chapter [2](#532302_1_En_2_Chapter.xhtml) - Social Media App (Async-Await)(iOS 14)” if you don’t have it), and change the deployment target version to iOS 13 or iOS 14 , you will see the project doesn’t compile anymore. Most of the errors have to do with the missing async methods that do not exist. Figure [3-2](#532302_1_En_3_Chapter.xhtml#Fig2) shows the errors that Xcode shows.

![](images/532302_1_En_3_Chapter/532302_1_En_3_Fig2_HTML.jpg)

A screenshot represents the chapter two social media app of i O S with four issues, where swift compiler error, data, Asynclmage, and bordered prominent are only available in i O S 15.0 or newer.

Figure 3-2

We can’t use `async/await` with iOS 14 and lower

Xcode’s suggestion – to use the availability checks directly – sounds good in theory, but if you let Xcode fix it for you, the project will be in a worse state than it is now. Because it will wrap all our async calls in availability checks, the project will do *nothing* in iOS 13 and iOS 14 after you get it to run.

The solution is to use the availability check , but in extensions for `URLSession` and `LAContext`. We will fix this project without touching `UserAPI` and `UserProfileViewModel`. We will need to fix some SwiftUI issues later, but we don’t have to touch the logic code, making it easy to add `async/await` code without breaking any functionality. You can download the complete and fixed project as well, called “Chapter [2](#532302_1_En_2_Chapter.xhtml) - Social Media App (Async-Await)(iOS 14)(Fixed)”.

Start by creating a folder or group inside the “Shared” folder, and name it “Extensions”. Then inside that group, create a new file called “LAContext+Extensions.swift”. Its contents will look like Listing [3-12](#532302_1_En_3_Chapter.xhtml#PC12).

```
extension LAContext {
@available(iOS, introduced: 13, deprecated: 15, message: "This method is no longer necessary. Use the API available in iOS 15.")
func evaluatePolicy(_ policy: LAPolicy, localizedReason: String) async throws -> Bool {
try await withCheckedThrowingContinuation({ continuation in
self.evaluatePolicy(policy, localizedReason: localizedReason) { success, error in
if let error = error {
continuation.resume(throwing: error)
} else {
continuation.resume(returning: success)
}
}
})
}
}
Listing 3-12
Backporting evaluatePolicy(_:localizedReason) async to iOS 14
```

The `@available` check will ensure that this piece of code can only be run on iOS 13 and iOS 14 . Older versions of iOS do not support `async/await`, so we will just exclude them altogether. When the deployment target is iOS 15, we will get a warning that reads “This method is no longer necessary. Use the API available in iOS 15.” When we start targeting iOS 15, we will be able to completely remove this extension, and the code will just work without having to do any further changes.

The continuation here allows us to smoothly convert a closure-based call into an `async/await` call that already exists in the SDK, but when targeting iOS 15 and up only. Instead of returning an error, we convert the method into a throwing one.

In Figure [3-3](#532302_1_En_3_Chapter.xhtml#Fig3), we see the warning in action, prompting us to update the code when running on a simulator lower than iOS 15 . You may need to clean the build folder and compile again to get the warning to show up:

![](images/532302_1_En_3_Chapter/532302_1_En_3_Fig3_HTML.jpg)

A screenshot depicts three issues discovered in the social media app in I O S, where deprecation, and evaluate policy were deprecated in I O S 15, warning is to use the A P I available in I O S 15.

Figure 3-3

The warning in the `@availabile` check , prompting us to update the code

Next, we need to provide the `data(from:)` method in `URLSession`. Go to the “Shared” ➤ “Extensions” directory, and create a file called “URLSession+Extensions.swift”, with the code in Listing [3-13](#532302_1_En_3_Chapter.xhtml#PC13).

```
extension URLSession {
@available(iOS, introduced: 13, deprecated: 15, message: "This method is no longer necessary. Use the API available in iOS 15.")
func data(from url: URL) async throws -> (Data, URLResponse) {
try await withCheckedThrowingContinuation({ continuation in
self.dataTask(with: url) { data, response, error in
if let error = error {
continuation.resume(throwing: error)
} else {
continuation.resume(returning: (data!, response!))
}
}
.resume()
})
}
}
Listing 3-13
Backporting the data(from:) method to iOS 13 and iOS 14
```

There’s not much difference with the code in Listing [3-13](#532302_1_En_3_Chapter.xhtml#PC13). Perhaps the most interesting part here is that the return type is a tuple of type `(Data, Response),` but in general, exposing this method to iOS 14 and 13 was not complicated. Do note that we are force-unwrapping the returning `data` and `response` properties. The reason we do so is because if you look at the API’s original async signature, they are not marked as optionals in the tuple, therefore it is safe to assume that if we do not get an error, these two variables are going to have non-nil values. If you doubt this, feel free to mark the tuple elements as optionals.

If you try to compile the project now, we still get two errors, but these errors have nothing to do with `async/await` anymore. The errors we need to fix are in `UserProfileView` , so open the “UserProfileView.swift” file.

The first error complains that there’s no `AsyncImage` in an iOS version lower than 15\. We could provide our own implementation for a network image, but that goes out of the scope of this book. Instead of doing that, we will provide a simple blue circle when running on an iOS version lower than 15\. The fix for this error is in Listing [3-14](#532302_1_En_3_Chapter.xhtml#PC14).

```
if #available(iOS 15.0, *) {
AsyncImage(url: userInfo.avatarUrl)
.frame(width: 150, height: 150)
.aspectRatio(contentMode: .fit)
.clipShape(Circle())
} else {
Circle()
.frame(width: 150, height: 150)
.foregroundColor(.white)
}
Listing 3-14
Creating an alternative view in case the device is not on iOS 15
```

Finally, the final error we need to fix has to do with prominent styles for buttons being introduced in iOS 15 . The easiest way to fix is to remove the `.buttonStyle(.borderedProminent)` line from `UserProfileView`.

And that’s it! You have successfully ported two async calls that didn’t exist in iOS 14 and that are native to iOS 15 , and if you try the compile the app now, it should work without an issue. If you want to test the iOS 14 and iOS 13 compatibility, make sure you run it in a simulator with one of those iOS versions. If you have none, you can go to the “Window” menu on Xcode, followed by “Organizer”, and you can add new simulators with different iOS versions there.

Note that unfortunately you cannot automatically backport all async/await code to iOS 14 and lower for free. You need to manually create extensions and write the continuation method by hand. Keep that in mind if you have a lot of closure-based code you would like to backport.

## Summary

In this chapter, we explored what continuations are, and we learned how we can use them to convert closure-based code into `async/await` code without having to write new implementations for the methods they intend to replace from scratch. We also learned how we can provide an async API for those calls that use delegates by creating wrapper objects around the delegate methods . Finally, we learned how we can use continuations to backport `async/await` code to iOS 14 and iOS 13 – we can’t go lower because this feature is only available on iOS 13, 14, and 15.

## Exercises

Coding Exercise

Download the “Chapter [2](#532302_1_En_2_Chapter.xhtml) Exercise” project (the same base project we used in Chapter [2](#532302_1_En_2_Chapter.xhtml)).

1.  Provide `async/await` versions for the `requestPermissions` and `requestData` functions using checked continuations. You should not remove the original implementations.

2.  Change the implementation of `ContentViewViewModel.swift` so it uses the async/await versions of those methods instead of the closure-based ones.

    The solutions can be found in the “Chapter [2](#532302_1_En_2_Chapter.xhtml) Exercise - Solved Continuations” project.

# 4. Structured Concurrency

So far, we have talked extensively about what `async/await` is and how it works. We have seen how this new tool can help us write structured code akin to procedural programming because we can get rid of other types of callbacks that break the linear readability of our code, such as closures and delegates. But we have seen that all this code doesn’t actually run at the same time, concurrently, or in a multithreaded manner.

Consider the async version of the `authenticateAndFetchProfile` method from the Social Media App we wrote in Chapter [2](#532302_1_En_2_Chapter.xhtml) (Listing [4-1](#532302_1_En_4_Chapter.xhtml#PC1)).

```
func authenticateAndFetchProfile() async {
isAuthenticated = await authenticateWithBiometrics()
if isAuthenticated {
do {
let userApi = UserAPI()
loadingStatus = .loading
userInfo = try await userApi.fetchUserInfo()
followingFollowersInfo = try await userApi.fetchFollowingAndFollowers()
self.loadingStatus = .idle
} catch {
loadingStatus = .error(error)
}
}
}
Listing 4-1
The async version of authenticateAndFetchProfile
```

This method has three async calls, but it’s not like all of them execute at the same time. In fact, we *need* the biometric operation to finish before we download any info to show. Downloading the profile info and the followers *depends* on the authentication being successful. Right now, that function goes through three basics steps:

1.  Authenticate with biometrics.

2.  Download the profile info.

3.  Download the followers and following users info.

Both download operations depend on the authentication, but other than that they are fully independent of each other, and we could execute them at the same time if we wanted to. In this chapter, we will begin executing more than one task at the same time.

Note

In Chapter [1](#532302_1_En_1_Chapter.xhtml), we defined Concurrency as “the ability of a thread (or your program) to deal with multiple things at once.” This does not strictly mean to do multiple things simultaneously, and, in general, when people think of Concurrency what they really have in mind is multithreading or parallelism. However, to be consistent with the community-given meaning of the word, from this point onwards, when we talk about concurrency, we are talking about executing multiple tasks at the same time.

## Understanding Structured Concurrency

Structured Concurrency follows the same idea as structured programming. It’s all about writing code in the order we expect it to run and seeing predictable (or at least semi-predictable) results. Unlike closure-based code, variables have a well-defined scope, and you can expect your code to not jump to other places that break the reading flow of your code. This also spares you from concerning yourself with other details such as memory management (what happens to variables captured in blocks? can closures be retained? “weak self”? huh?).

And that is if you are only thinking about closure-based calls that are not concurrent. Can you picture your code with multiple closure-based concurrent calls, where a lot of independent code is running at the same time?

With structured concurrency , we can have multiple code paths running at the same time with results we can predict, without having to think about labyrinths of code that jump through multiple closure-based calls.

The new `async/await` system offers two ways to do structured concurrency: the `async let` construct, and task groups.

### The async let construct

Also known as an *async let binding* . Creating your first bits of concurrent with this code is underwhelmingly simple. If you know how to create a constant with `let`, you can use `async let` . To run code concurrently, all you need to do is to tack the `async` keyword before a `let` declaration. The rest of your declaration looks like a normal Swift variable initialization. Do note that to be able to do this, you must be within an asynchronous context – either inside a `Task` or within a function marked as `async`. As your code runs through `async let` declarations, everything after the assignment will take place somewhere else (the functions and methods launched may run in different threads), and the tasks will run concurrently. `async let` tasks are created as subtasks of the task that created them.

With that said, we can do some changes to Listing [4-1](#532302_1_En_4_Chapter.xhtml#PC1). We said earlier that we can fetch the profile info and followers and following users’ data at the same time because they are completely independent of each other. We will create a different function to illustrate how `async let` works. The function in Listing [4-2](#532302_1_En_4_Chapter.xhtml#PC2) creates a new function, `fetchData`, that will do the two network calls concurrently to fill the `UserProfileView` screen.

```
func fetchData() async throws -> (userInfo: UserInfo, followingFollowersInfo: FollowerFollowingInfo) {
let userApi = UserAPI()
async let userInfo = userApi.fetchUserInfo()
async let followers = userApi.fetchFollowingAndFollowers()
return (try await userInfo, try await followers)
}
Listing 4-2
Using async let to run code concurrently
```

If you were to add print statements sandwiched between the `async let` calls, you would see that they get called instantly. `userInfo` and `followers` will run at the same time, but the code suspends right before it’s time to return. Recall that the `await` keyword is a suspension point, meaning our function will suspend until both `fetchUserInfo` and `fetchFollowingAndFollowers` return or either of them throws an error.

You may remember that in Chapter [2](#532302_1_En_2_Chapter.xhtml) we said that every `async` call must be paired with an `await`. But we never said that the await must be on the same line. With `async let` , the `await` keyword is needed when you need the value of the variable that was assigned as part of the `async let`. So, if your `async let` call returns a value another function will depend on, you await at the call site. If you need to get multiple values concurrently inside a function, you can await right at the `return` statement. The rule that states that an `async` call must be paired with an `await` keyword is not violated.

Using `async let` is useful and you can use the `await` keyword anywhere, whether it is in a single variable declaration, or when waiting for array elements, before returning a tuple like we did in Listing [4-2](#532302_1_En_4_Chapter.xhtml#PC2), and more.

If you prefer, you can write the last line of the function, which currently is `return (try await userInfo, try await followers)`, as `return try await (userInfo, followers)`, saving you from writing some code. Whichever you choose is a stylistic choice.

Listing [4-3](#532302_1_En_4_Chapter.xhtml#PC3) shows the changes to the `authenticateAndFetchProfile()` method that makes use of the new `fetchData()` method :

```
func authenticateAndFetchProfile() async {
isAuthenticated = await authenticateWithBiometrics()
if isAuthenticated {
do {
loadingStatus = .loading
let data = try await fetchData()
userInfo = data.userInfo
followingFollowersInfo = data.followingFollowersInfo
self.loadingStatus = .idle
} catch {
loadingStatus = .error(error)
}
}
}
Listing 4-3
Changes to authenticateAndFetchProfile()
```

We are now calling the `fetchData` method which will return a tuple. Note that we still need to call it with `await`, because even though the logic within it may have `async let` calls, this signature itself for this method is still `async`. This method will only return when both `async let` tasks within it have finished. That said, you could still call `fetchData` with `async let` to run it concurrently alongside other tasks.

Note

While we use the `async let` construct to explicitly state our intention of running tasks concurrently, it is still up to the system to decide if it will indeed run your code concurrently or if it will reuse another thread for multiple calls. That is to say, the system may decide to run `fetchUserInfo` and `fetchFollowingAndFollowers`, both in a different but shared thread, essentially running them serially, away from the main thread.

You can download the “Chapter [2](#532302_1_En_2_Chapter.xhtml) - Social Media App (Async-Await)(async let)” sample project to see the code in action if you don’t want to do the changes yourself.

### Task Groups

`async let` constructs are ideal when you know the amount of concurrency involved for a given task. In our Social Media App , we know that we need to fetch the profile info and the followers info – that’s exactly two tasks we can run at the same time. But there will be times in which you don’t know the amount of concurrency needed to finish a certain functionality of your app. Imagine you have an app that allows users to download a dynamic number of images, or you want to apply filters to a variable number of images at once. In these cases, we could need one task, two tasks, 300 tasks, or more. For these situations, the `async/await` system provides us with Task Groups.

Task Groups are a structured concurrency tool that allows us to run an unknown number of (possibly) concurrent tasks at once, provided they are related. If you have too many tasks Swift won’t run all of them at once, and it will prioritize tasks and run as many of them as possible concurrently.

#### Task Groups in Action

To create a Task Group , you can use the `withTaskGroup` and `withThrowingTaskGroup` functions . Just like when creating explicit continuations, you use the former when your group can complete without an issue, and the latter when your group can throw an error.

I have provided a simple SwiftUI project that will perform the following tasks:

1.  It will retrieve a JSON file from a server that contains a list of images.

2.  It will download each URL in a task group, store them locally, and then render a SwiftUI view displaying said images.

Because we do not know the number of images we need to download until we get the file list, we can use a task group to try to download as many of them as possible concurrently.

Note

A computer system doesn’t have an infinite number of threads. Task groups will do their best to download all the images concurrently, but it will optimize and prioritize tasks. If you are dealing with a lot of concurrent tasks at once, be aware that task groups may queue tasks for later, and they will not necessarily execute all of them at the same time.

##### The Image Downloader Project

The project is a SwiftUI app that downloads an image list from the Internet and then downloads all the images in that list. You can download the “Chapter [4](#532302_1_En_4_Chapter.xhtml) - ImageDownloader” project to check it out. We will only discuss key points of the source code, as putting it all here would consume a lot of space.

When run, the app has two screens. The first one, shown in Figure [4-1](#532302_1_En_4_Chapter.xhtml#Fig1), is the first screen.

![](images/532302_1_En_4_Chapter/532302_1_En_4_Fig1_HTML.jpg)

A screenshot depicts the main screen of the downloader app, which includes wi-fi and full battery symbols, and the time is 8:16, as well as a nothing to show and below load images button.

Figure 4-1

The main screen of the ImageDownloader app

Tapping the “Load Images” button will cause the app to download the images. After a little while, all the images will be displayed in the UI. Figure [4-2](#532302_1_En_4_Chapter.xhtml#Fig2) shows a screenshot of the UI after the app loads some contents.

![](images/532302_1_En_4_Chapter/532302_1_En_4_Fig2_HTML.jpg)

A screenshot depicts three photographs of cats, with Wi-Fi and battery symbols at the top of the screenshot.

Figure 4-2

The UI upon downloading the images

Note

I hope you like cats.

Our JSON data returns an array of `imageName-url` pairs. Each image has a name and a URL associated with it. The file `ServerImage.swift`, in Listing [4-4](#532302_1_En_4_Chapter.xhtml#PC4) shows its contents:

```
import Foundation
struct ServerImage: Codable {
let imageName: String
let url: URL
}
Listing 4-4
The ServerImage object
```

The `ImageManager` class, in the `ImageManager.swift` file, contains all the magic. The class has two important properties, shown in Listing [4-5](#532302_1_En_4_Chapter.xhtml#PC5).

```
private let imageListURL = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/taskgroups/imagelist_tif.json")!
private let urlSession = URLSession.shared
Listing 4-5
The main properties of the ImageDownloader class
```

The `imageListURL` variable stores the URL to download the image list later. We are reusing the same URL session to download everything, in the `urlSession` variable.

Note

I have deliberately created big images in terms of file size (around 25 MB each). This is to give you some time to appreciate the async concurrency of this project. If you do not have unlimited internet, or your internet is too slow, replace the “tif” portion of the URL stored in the `imageListURL` variable with “jpg”. This will cause the program to use JPEG images that are much smaller in size (around 3 MB).

Then, the `ImageManager` class has three methods. Listing [4-6](#532302_1_En_4_Chapter.xhtml#PC6) shows the function used to download and parse the list itself.

```
func fetchImageList() async throws -> [ServerImage] {
let (data, _) = try await urlSession.data(from: imageListURL)
let urls = try jsonDecoder.decode([ServerImage].self, from: data)
return urls
}
Listing 4-6
The fetchImageList method
```

We can once again appreciate the simplicity of `async/await`. Try to picture in your head what this method would look like if you were using closure-based calls instead.

The method to download a single image is a bit more interesting, as seen in Listing [4-7](#532302_1_En_4_Chapter.xhtml#PC7).

```
/// Returns the local image path.
func download(_ serverImage: ServerImage) async throws -> URL {
let fileManager = FileManager.default
let tmpDir = fileManager.temporaryDirectory
let (downloadedImageUrl, _) = try await urlSession.download(from: serverImage.url)
let destinationUrl = tmpDir.appendingPathComponent(serverImage.imageName)
// If the destination already exists, remove it.
Try? fileManager.removeItem(at: destinationUrl)
try fileManager.moveItem(at: downloadedImageUrl, to: destinationUrl)
return destinationUrl
}
Listing 4-7
Downloading a single image
```

First, we are using the `download` method of `urlSession` instead of `data`, because these are potentially big files. We will store them in a path (`destinationUrl`), and we will remove any file at this path before we store the new one just to prevent any distracting errors. After we store the image locally, we will return the local URL to the caller.

The last method is the interesting one. It’s a method that will download all the images in an array of objects of type `ServerImage`, seen in Listing [4-8](#532302_1_En_4_Chapter.xhtml#PC8).

```
/// Downloads all the images in the array and returns an array of URLs to the local files
func download(serverImages: [ServerImage]) async throws -> [URL] {
var urls: [URL] = []
try await withThrowingTaskGroup(of: URL.self) { group in
for image in serverImages {
group.addTask(priority: .userInitiated) {
let imageUrl = try await self.download(image)
return imageUrl
}
}
for try await imageUrl in group {
urls.append(imageUrl)
}
}
return urls
}
Listing 4-8
Implementing a task group to download multiple images at once
```

At a high level, the function begins by declaring a variable of URLs. In this variable, we will store all the local URLs of the images we download. Then, we create a task group, which is by itself an operation that can be awaited, and finally, we return the images. Once again, we can appreciate the linearity provided by async/await.

When you create a task group with either `withTaskGroup` or `withThrowingTaskGroup` , you need to specify the data type the group will yield. In this case, our group is yielding URLs, because the `download(serverImages:)` method will return URLs. The closure of either task group method will provide us with the task group itself – in this case, we named it `group`.

We submit tasks to this group with the `addTask` method , and as soon as a task is submitted, they begin executing. In our `download(serverImages:)` function, we are iterating over all the server images, and on each iteration we create a task to download the image. This will cause the group to execute all your tasks at the same time *in the best case.* Just like the other tools in the `async/await` system, the system will prioritize the tasks and if it has all the resources available, it will try to execute all the tasks concurrently, but don’t be surprised if you have a task group that is not executing every single task at the same time, especially if you submit a lot of tasks to it. We can optionally specify the priority . Our method uses the `.userInitiated` priority , which is the highest priority available. We will talk more about priorities in the next chapter.

One important thing to keep in mind is that tasks added to the group may not execute in the order they were submitted. If you want to use task groups, it’s important you design your code in a way that makes the order irrelevant.

Finally, within `withThrowingTaskGroup` , we have another loop – an awaited `for` loop. This loop will receive all the URLs as they are downloaded. It will receive each URL one by one, so we cannot put the `URLs` variable in an invalid state. Once again, do not expect to get the URLs in a specific order. The group variable itself is a collection, so not only can we iterate over it with `for-in`, but we can also use higher-order functions such as `filter`, `map`, `reduce` to process the results. And yes, `for` loops can have the `await` keyword for code that will emit values over time.

Note that if an error occurs in any image download task, the whole group will throw an error. Keep this in mind as it can cause surprising behavior. If you think your image downloads will fail, it’s probably a good idea to provide a default image for the download task in this specific project.

## Summary

In this chapter, we learned what structured concurrency is. We learned that structured concurrency gets its ideas from structured programming, and it makes it really useful to write and understand concurrency code.

We explored two different ways with which we can create structured concurrency: First, the `async let` binding , which is useful when you know how much concurrency you need. When we use `async let`, we will need to use the `await` keyword at the time when we need to have the variable’s value. The other structured concurrency tool is task groups. We use task groups when want to run multiple tasks at the same time, but we don’t know the exact amount of concurrency involved, such as downloading multiple images from the network at the same time, or when the concurrency comes from a dynamic source such as user input.

## Exercises

Improve the App

Download the “Chapter [4](#532302_1_En_4_Chapter.xhtml) Exercise – ForumApp” project. This app will consume a few web services and render the following screen:

![](images/532302_1_En_4_Chapter/532302_1_En_4_Figa_HTML.jpg)A screenshot depicts three photographs, first two are of error loading image retry, the last one is of a thick straight line, and the top of the screen is battery and Wi-Fi symbols. ![](images/532302_1_En_4_Chapter/532302_1_En_4_Figb_HTML.jpg)A screenshot represents the four photographs where AndylbanezK below total posts, threads, and new posts, and online friends list like name and member since date.

Given the source of the code, perform the following:

1.  Currently, the app uses async/await, but it does not use concurrency. Identify the code that could be run concurrently and optimize the app.

2.  The code that retrieves the “Online Friends” is hard-coded to a maximum of 3 friends in the `ForumAPI.swift` file, `fetchProfiles(for:)` method. Fix the code so that it uses a task group to fetch all the profiles concurrently and is no longer hard-coded to a maximum number of profiles.

    You can download the “Chapter [4](#532302_1_En_4_Chapter.xhtml) Exercise – Forum App (Solved)” to see the solution.

# 5. Unstructured Concurrency

In the last chapter, we talked extensively about structured concurrency. We learned that structured concurrency follows the same ideas of structured programming. A natural counterpart for structured concurrency is unstructured concurrency.

Unstructured concurrency offers more flexibility in exchange for slightly less usability. When working with unstructured concurrency, your code will not necessarily follow the top-to-bottom structure we have come to love. Luckily, working with unstructured concurrency is still very easy, and the flexibility won’t increase the complexity of your program considerably.

To work with unstructured concurrency , we use an object we already know: `Task`.

## Tasks in Depth

`Task` is a useful object that allows us to do two important things:

1.  The moment a task is created with its initializer, it launches the code within the closure asynchronously.

2.  It creates a concurrent context for us to be able to use concurrency in the way of calling `async` methods .

### Creating Tasks

Task is an object that can store a value, an error, both, or neither. To create an asynchronous, unstructured task , we call Task and initialize it with a closure which is the code we want to run asynchronously. We have been doing this continuously throughout the book. For example, Listing [5-1](#532302_1_En_5_Chapter.xhtml#PC1) is a small snippet from Chapter [2](#532302_1_En_2_Chapter.xhtml) that calls some `async` method with `Task`.

```
func authenticateAndFetchProfile() {
Task {
//...
let userProfile = try await fetchUserInfo()
//...
}
}
Listing 5-1
Using Task to call async code
```

In Listing [5-1](#532302_1_En_5_Chapter.xhtml#PC1), the `fetchUserInfo()` info method is called the moment `authenticateAndFetchProfile()` method . You cannot decide when the code within a `Task` will execute. It will always be done immediately upon initialization.

When you want to do concurrency using `Task`, the first bit of flexibility you have at your disposal is specifying the priority. The priority is of type `Task.Priority`, which is the same one you can use when working with task groups. Listing [5-2](#532302_1_En_5_Chapter.xhtml#PC2) shows the available priority options you have at your disposal when working with the new concurrency system.

```
static let background: TaskPriority
static let high: TaskPriority
static let low: TaskPriority
static var medium: TaskPriority
static let userInitiated: TaskPriority
static let utility: TaskPriority
Listing 5-2
Available priority options
```

A Task is an object, as such, you can store it in a variable. This is where part of the reason this is called “unstructured concurrency ” comes from. You can have multiple `Task` objects, store them in variables, or even in collections. And one thing you can do with unstructured concurrency that you can’t do with structured concurrency is cancelling tasks. Task cancellation is an important topic, and we will talk more about it when we talk about the Task Tree in this chapter. For now, keep in mind that you can cancel tasks by calling the `cancel()` method.

Listing [5-3](#532302_1_En_5_Chapter.xhtml#PC3) shows how we can declare a task, store it in a variable, and cancel it later.

```
func authenticate() {
let authenticationTask = Task {
let context = LAContext()
let policy = LAPolicy.deviceOwnerAuthenticationWithBiometrics
return try await context.evaluatePolicy(policy, localizedReason: "To log in")
}
authenticationTask.cancel()
}
Listing 5-3
Creating a Task, and explicitly cancelling it later
```

We created a variable of type `Task<Bool, Error>`. This is because the `context.evaluatePolicy(_:localizedReason:)` returns a Bool and can throw an error.

We can await on the Task’s value if we need it to continue doing some work, as seen in Listing [5-4](#532302_1_En_5_Chapter.xhtml#PC4).

```
Task {
let authenticationSuccessful = try await authenticationTask.value
if authenticationSuccessful {
// Successfully authenticated.
}
}
Listing 5-4
Awaiting on a Task’s value
```

We need to do `try await` because our task can return a value and throw an error. Note the lack of a `do-catch` statement in this newly created `Task`. This code will implicitly create a task that is of type `Task<()`, `Error>`, because it doesn’t return anything, but it can throw an error (the error thrown by `context.evaluatePolicy`). Tasks do not need an explicit `do-catch` block because they will be captured by closure itself but add these statements if it makes sense in the context of your application.

When you create a new task with `Task {}`, it will inherit contextual information from the task that spawned it. The information inherited includes the actor the task is running on (if you spawn a `Task` in something that runs on the `@MainActor,` then the task will also run on the `@MainActor`), and priority. If your newly spawned Task is a root task – it creates an asynchronous context but it itself is not running in one – it will inherit the actor of whatever thread it is running on.

### Unstructured Concurrency in Action

Now that you have a better understanding of what unstructured concurrency is, and you finally learned what the Task object is, we can start writing some sample code to better understand how it all works.

We will work and modify the project we created in the last chapter – the ImageDownloader project .

The project worked perfectly fine, but it had two main issues that I’d like to address:

1.  When the app launched, it showed a `ProgressView` in the center until all images were done downloading. This means if we had a lot of images or they were *really* big, the `ProgressView` would be shown for a very long time. Once the images were done downloading, the view would refresh and show all the images at once.

2.  There was no guaranteed to the order the images would be displayed in. Images would be added to the array in a first-downloaded basis. This is not harmful buy itself, but if you had an app that displayed a chronological order of elements, it would matter.

We will modify the original project and address these two issues. If you would like to download the modifications directly, feel free to download the “Chapter [4](#532302_1_En_4_Chapter.xhtml) - ImageDownloaderUnstructured” project.

#### Modifying the ImageManager Class

Start by adding the code in Listing [5-5](#532302_1_En_5_Chapter.xhtml#PC5) to the top of the file.

```
enum ImageDownloadStatus {
case downloading(_ task: Task)
case error(_ error: Error)
case downloaded(_ localImageURL: URL)
}
struct ImageDownload: Identifiable {
let id: UUID = UUID()
let status: ImageDownloadStatus
}
Listing 5-5
Additional objects
```

Currently, `ContentView` reads an array of URLs from the `ContentViewViewModel` and displays them in the UI. We will change this, so the `ImageManager` is the actual data source of the images. We will also make the view read `ImageDownload` objects instead of URL objects. This will help us show a different UI depending on the download status of the image (the `ImageDownloadStatus` enum). We need to make `ImageDownload` conform to `Identifiable` so it plays nicely with SwiftUI.

Also, since the `ImageManager` class will be a data source for SwiftUI now, mark it as `@MainActor`, as seen in Listing [5-6](#532302_1_En_5_Chapter.xhtml#PC6).

```
@MainActor
class ImageManager: ObservableObject {
Listing 5-6
Marking ImageManager as @MainActor
```

Now go to the `TaskGroupsApp.swift` file and change the declaration of the `ImageManager` class variable there as well to add the `@MainActor` attribute, as seen in Listing [5-7](#532302_1_En_5_Chapter.xhtml#PC7).

```
@MainActor
fileprivate let imageManager = ImageManager()
Listing 5-7
Marking the imageManager variable as @MainActor
```

Go back to the `ImageManager.swift` file , and this time modify the `images` variable to be of type `[ImageDownload].` Also, add the `@Published` attribute. Both changes are shown in Listing [5-8](#532302_1_En_5_Chapter.xhtml#PC8).

```
@Published private(set) var images: [ImageDownload] = []
Listing 5-8.
Creating a published property for remote images
```

Because SwiftUI will use this as a data source, whenever this array changes, our UI will update.

Finally, we will modify the `download(serverImages:)` function as per Listing [5-9](#532302_1_En_5_Chapter.xhtml#PC9).

```
func download(serverImages: [ServerImage]) {
for imageIndex in (0.. {
do {
let imageUrl = try await download(serverImages[imageIndex])
self.images[imageIndex] = ImageDownload(status: .downloaded(imageUrl))
} catch {
self.images[imageIndex] = ImageDownload(status: .error(error))
}
}
images.append(ImageDownload(status: .downloading(downloadTask)))
}
}
Listing 5-9
A new version of download(serverImages:)
```

This is where part of the magic will happen. We will start by iterating over all the `ServerImage`s we have downloaded. For each server image, we will create an explicit `Task`. We will also add this task immediately to the images array with the `.downloading(_)` status. The enum tracks the task itself because we can cancel it later if we want.

The task body will attempt to download the image. If the download fails, we will replace its entry in the images array with the `.error(_)` value. If it succeeds, we will replace it with the `.downloaded(url)` value. In short, the body of the task will replace its place in the array with a different enum value depending on whether the download was successful or not. And because this is a `@Published` property, SwiftUI can observe its changes and be notified in real time of whatever happens with this array.

#### Modifying the ContentViewViewModel Class

The main modification in the `ContentViewViewModel.swift` file is the `downloadImages()` function as in Listing [5-10](#532302_1_En_5_Chapter.xhtml#PC10). We have also eliminated the imageUrls array from this file as that work has been delegated to the ImageManager class.

```
func downloadImages() {
Task {
guard let manager = imageManager else { return }
do {
let imageList = try await manager.fetchImageList()
manager.download(serverImages: imageList)
} catch {
print(error)
}
}
}
Listing 5-10
The new downloadImages() method
```

The view model is now a very light class, containing only this method. We could delegate everything to the `ImageManager` class, but I prefer to keep it as a separate logic class.

#### Modifying the ContentView Object

The `body` property is modified as per Listing [5-11](#532302_1_En_5_Chapter.xhtml#PC11).

```
var body: some View {
ScrollView {
LazyVStack {
if let manager = viewModel.imageManager {
ForEach(manager.images) { imageDownload in
switch imageDownload.status {
case .error(_): imageErrorView()
case .downloading(_): imageDownloadView()
case .downloaded(let url): imageView(for: url)
}
}
}
}
}
.onAppear {
viewModel.imageManager = imageManager
viewModel.downloadImages()
}
}
Listing 5-11
The new body property
```

We will display a new view depending on the `ImageDownloadStatus` property of the `ImageDownload` object. If we have an error, we will show an “x” mark. If we are currently downloading it, we will show a photo placeholder. If we have the image, we will show the image itself. Listing [5-12](#532302_1_En_5_Chapter.xhtml#PC12) shows the implementation of these additional methods.

```
@ViewBuilder
func imageDownloadView() -> some View {
VStack {
Image(systemName: "photo")
.resizable()
.frame(width: 300, height: 300)
.foregroundColor(.gray)
}
}
@ViewBuilder
func imageErrorView() -> some View {
VStack {
Image(systemName: "xmark")
.resizable()
.frame(width: 300, height: 300)
}
}
@ViewBuilder
func imageView(for url: URL) -> some View {
Image(uiImage: UIImage(data: viewModel.data(for: url))!)
.resizable()
.frame(width: 300, height: 300)
}
Listing 5-12
Helper properties to create the views
```

And that’s it! If you run the app now, you will see a UI with placeholders for the images. Figure [5-1](#532302_1_En_5_Chapter.xhtml#Fig1) shows our basic UI.

![](images/532302_1_En_5_Chapter/532302_1_En_5_Fig1_HTML.jpg)

A screenshot represents a mobile screen with three photos about to load, one below the other, and time, cellular, and battery symbols on the top.

Figure 5-1

Showing placeholders for images that will be downloaded

As the images load, Figure [5-2](#532302_1_En_5_Chapter.xhtml#Fig2) shows the placeholders getting replaced with images as they are downloaded.

![](images/532302_1_En_5_Chapter/532302_1_En_5_Fig2_HTML.jpg)

A screenshot depicts a mobile screen with three photos of cats one below the other, and time, cellular, and battery on the top.

Figure 5-2

The images replace the placeholders one by one as they are downloaded

This improved version of the ImageDownloader project shows placeholders for images as they are downloaded instead of showing a `ProgressView` while waiting for all the images until they are downloaded, and the images will always show up in the same order. Neat!

## The Task Tree

The new concurrency system in Swift is driven by an underlying structure called the task tree . Like the name states, the task tree gives us a mental model of how concurrency works behind the scenes. This task tree dictates how your tasks are going to run in relation to others. It also controls how data is inherited to child tasks. Earlier, we mentioned that a task inherits the actor, priority, and task local variables from the task that launched it. This is thanks to the task tree. Tasks spawn child tasks and these child tasks inherit all these attributes from their parents.

To better understand this concept, let’s explore with some of the code we have written previously. Listing [5-13](#532302_1_En_5_Chapter.xhtml#PC13) brings back a function we had in the `async let` version of our Social Media App.

```
func fetchData() async throws -> (userInfo: UserInfo, followingFollowersInfo: FollowerFollowingInfo) {
let userApi = UserAPI()
async let userInfo = userApi.fetchUserInfo()
async let followers = userApi.fetchFollowingAndFollowers()
return (try await userInfo, try await followers)
}
Listing 5-13
Asynchronously fetching data from a server
```

This code has two `async let` calls that retrieve the user info and followers info, respectively. Because they are `async let` , the system will try to run them concurrently.

Intuitively, you may think that each `async let` call launches a new task implicitly, but this is not the case. Each `async let` call will launch a new *child* task. The distinction is important because `Task`s can only be created explicitly.

Each child task will inherit the information we received earlier from the parent (in this case, the parent is the `fetchData()` function itself) – actor, priority, and local variables. When you create `Task` within another `Task`, it will also inherit the parent `Task`’`s` attributes, but unlike implicit tasks, you can tweak it a bit according to your necessities, like spawning it with a different priority. Nested tasks are not child tasks, strictly speaking.

If you were to draw the `fetchData()` task as a tree, it would look like Figure [5-3](#532302_1_En_5_Chapter.xhtml#Fig3).

![](images/532302_1_En_5_Chapter/532302_1_En_5_Fig3_HTML.png)

A flow diagram depicts the visual representation with fetchData, userApi dot fetchUserInfo, and userApi dot fetchFollowingandFollowers.

Figure 5-3

A visual representation of a task tree

When `fetchData` is executed, the same task is used to launch `fetchUserInfo` and `fetchFollowingAndFollowers`. This needs to be repeated and it’s very important you don’t forget. ***Tasks*** ***are always created explicitly***. Unless we manually make those calls wrapped within a `Task`, they will be run under the same `Task`. One task can run multiple concurrent child tasks, with the same actor, priority, and task local variables.

Another important aspect of the task tree you need to consider is that ***a parent task can only finish its work when all its children have finished their work***. Even if such termination is not successful (i.e., an error is thrown), it’s still a termination, and a task can be completed when such abnormal situations occur.

*There are two other aspects that are also governed by the task tree* . *They are important enough to warrant their own sections: Error propagation and task cancellation.*

### Error Propagation

Let’s go back to the `fetchData` tree . `fetchData` itself can throw, and so can all the child tasks it spawns. Suppose an error occurs in `fetchUserInfo`. If the function throws an error, execution of the function will end at that point, throw the error up to the caller, and it will not execute anything underneath the point the error took place. So far, that’s exactly what would happen if we weren’t using the new concurrency system at all. That’s how error propagation works in Swift (and in many other programming languages, in fact).

But when we are working with the new concurrency system, things are not so straightforward. Sure, `fetchUserInfo` could throw an error, but `fetchFollowingAndFollowers` could complete normally and return an expected value.

If an error is thrown, it will be propagated up the task tree until it reaches the caller. Any other child tasks that are either awaiting or running concurrently will be marked as cancelled.

### Task Cancellation

Task cancellation is very important, yet it does not work like you would expect at first glance. If you reread the last sentence in the previous paragraph, you will notice that we said that the tasks “will be *marked* as cancelled.”

With this new concurrency system, task cancellation is cooperative. Cancelling a task simply means that you are notifying it that you intend it to be cancelled. It is up to the task to actually find an appropriate time to cancel itself, and you need to implement this logic by yourself. The reason task cancellation is cooperative is because there are sensitive yet important tasks that may not be acceptable to forcefully stop their execution. If you are writing data to a database, or you are modifying a user file in a concurrent task, abruptly cancelling it can lead to user data corruption (and many 1-star reviews).

When a Task is cancelled , it also marks any children subtasks as cancelled, attempting to ensure no unnecessary work will be done.

To explain this concept better, I have provided a project, “Chapter [5](#532302_1_En_5_Chapter.xhtml) – Counter App” that we will work through.

When you launch the app, you will see a simple button, as seen in Figure [5-4](#532302_1_En_5_Chapter.xhtml#Fig4).

![](images/532302_1_En_5_Chapter/532302_1_En_5_Fig4_HTML.jpg)

A screenshot represents a mobile screen with start counting on it along with time, cellular, and battery symbols on the top.

Figure 5-4

A simple UI with a button to start a counter

Tapping the button will start showing the numbers 1 to 10 and 11 to 20 getting printed in two different sections, at the same time. Figure [5-5](#532302_1_En_5_Chapter.xhtml#Fig5) shows this action in progress.

![](images/532302_1_En_5_Chapter/532302_1_En_5_Fig5_HTML.jpg)

A screenshot represents a mobile screen with numbers 1, 2, 11, and 12 encircled separately, cancel at the bottom, along with time, cellular, and battery symbols on the top.

Figure 5-5

Showing numbers in the UI. The UI is updated every second

The functions that output the numbers to draw are in Listing [5-14](#532302_1_En_5_Chapter.xhtml#PC14).

```
func countFrom1To10() async throws -> Bool {
for i in (1...10) {
try? await Task.sleep(nanoseconds: 1_000_000_000)
values1To10 += [i]
}
return true
}
func countFrom11To20() async throws -> Bool {
for i in (11...20) {
try? await Task.sleep(nanoseconds: 1_000_000_000)
values11to20 += [i]
}
return true
}
Listing 5-14
The methods output the numbers to show in the UI
```

Listing [5-15](#532302_1_En_5_Chapter.xhtml#PC15) shows the method that launches these two functions asynchronously.

```
func startCounting() {
isCounting = true
counterTask = Task {
async let counter1To10DidFinish = countFrom1To10()
async let counter11To20DidFinish = countFrom11To20()
do {
let _ = try await (counter1To10DidFinish, counter11To20DidFinish)
} catch {
self.error = error
}
}
}
Listing 5-15
Launching both counter methods asynchronously
```

`counterTask` is a variable of type `Task<Void, Never>`. Storing the task in a variable will allow us to cancel it later, as seen in Listing [5-16](#532302_1_En_5_Chapter.xhtml#PC16).

```
Button("Cancel") {
viewModel.counterTask?.cancel()
}
Listing 5-16
Giving the user manual control over the task
```

You can see that the `counterTask` variable acts as a parent task for `countFrom1to10` and `countFrom11to20`. What do you think will happen when you tap the “Cancel” button? Will the number stop printing altogether and the UI display the ones it managed to print? Will it erase all the numbers? Feel free to take a minute to think about it.

But the right answer is neither of those options. The moment you tap the Cancel button, all the numbers will be displayed in the UI instantly (unlike one number at a time, like it had been doing) and the task will appear like it finished without an issue. What’s going on here?

***Cancellation is cooperative***. This means that the tasks will not be explicitly and automatically cancelled by anyone. Earlier, we said that tasks are *marked as cancelled*. Cancellation being cooperative means that tasks need to check their cancellation status and cancel execution when it is appropriate. It is not correct to cancel tasks instantly, because your code could be doing something essential that cannot be unexpectedly interrupted. So instead of having someone else cancel your code, your code needs to wait for an appropriate time to stop executing itself.

To check for cancellation, you need to place cancellation checks where it makes sense to do so. Swift provides you with the `Task.checkCancellation()`static method and the `Task.isCancelled` static property . Use the former when you are within an async context that can throw, and the latter when your context is not marked as `throws`.

With this in mind, we now need our countFrom1To10() and countFrom11To20() methods to check their cancellation status at some point and return when it makes sense. One way we can do this is to place a check before we append the value to the variable. Listing [5-17](#532302_1_En_5_Chapter.xhtml#PC17) shows how this is done.

```
func countFrom1To10() async throws -> Bool {
for i in (1...10) {
try? await Task.sleep(nanoseconds: 1_000_000_000)
if Task.isCancelled { return false }
values1To10 += [i]
}
return true
}
Listing 5-17
Adding a task cancellation check to countFrom1To10()
```

It would also be acceptable to add the check after you append the value to the array. And even better, if it makes sense to have the cancellation check in both places, you can do that too. Listing [5-18](#532302_1_En_5_Chapter.xhtml#PC18) shows the method with some additional checks.

```
func countFrom1To10() async throws -> Bool {
for i in (1...10) {
try? await Task.sleep(nanoseconds: 1_000_000_000)
if Task.isCancelled { return false }
values1To10 += [i]
if Task.isCancelled { return false }
}
return true
}
Listing 5-18
Adding additional cancellation checks
```

There is no formula to identify where the cancellation checks should go, but it is important that you design your code with cancellation in mind, because you don’t want consumers of your code to have unnecessarily running operations. Many of Apple’s async methods are already accounting for cancellation. If you cancel a task that has a `URLSession` data task running, for example, it will react to cancellations automatically.

If you run the code now and tap the Cancel button, the first numbers will stop printing in place, whereas the ones that go from 11 to 20 will still print to the end. Figure [5-6](#532302_1_En_5_Chapter.xhtml#Fig6) shows this behavior in action.

![](images/532302_1_En_5_Chapter/532302_1_En_5_Fig6_HTML.jpg)

A screenshot represents a mobile screen with numbers 1, 11 to 20 encircled separately, cancel at the bottom, along with time, cellular, and battery symbols on the top.

Figure 5-6

Only one task is checking for cancellation, therefore cancellation is not working as expected

Before we fix countFrom11to20 , you may be wondering why exactly the numbers show up instantly when cancelling, instead of showing one number per second like it does before the “Cancel” button is tapped. The reason is that the t`ry? await Task.sleep(nanoseconds: 1_000_000_000)` call has the error silenced with `try?`. The code was written like this because the `sleep(nanoseconds:)` method actually throws an error when the `Task` it’s running on has been cancelled. Because the error is silenced, the task doesn’t sleep for one second, and the loop gets executed immediately. Essentially, cancellation is an error that gets thrown, and you can catch it and handle it differently than the others.

To provide an example of cooperative task cancellation , I had to silence the error and manually add the cancellation checks. In general, you can expect that any method of the new concurrency system is already checking for cancellation at some point, alongside Apple’s own async methods. You can avoid checking for cancellation in this specific method if you remove the question mark after the `try`.

Listing [5-19](#532302_1_En_5_Chapter.xhtml#PC19) fixes the `countFrom11to20` method .

```
func countFrom11To20() async throws -> Bool {
for i in (11...20) {
if Task.isCancelled { return false }
try? await Task.sleep(nanoseconds: 1_000_000_000)
values11to20 += [i]
if Task.isCancelled { return false }
}
return true
}
Listing 5-19
Fixing the countFrom11To20 method
```

And now, whenever you tap the “Cancel” button , the numbers will not print from top to bottom like they were doing before we added the cancellation checks. It’s interesting to note that due to the nature of asynchronous code, you may see different outputs in the UI depending on when you cancel. For example, the app tries to print two numbers every second (1 and 11, 2 and 12, etc.), but depending on when you cancel you may get only one number of a pair. Figure [5-7](#532302_1_En_5_Chapter.xhtml#Fig7) shows what happened when I cancelled the task when it was showing the third pair.

![](images/532302_1_En_5_Chapter/532302_1_En_5_Fig7_HTML.jpg)

A screenshot represents a mobile screen with numbers 1, 2, 11, 12, and 13 encircled separately, cancel at the bottom, along with time, cellular, and battery symbols on the top.

Figure 5-7

The tasks were cancelled at the same time, but one of them was slightly ahead of the other

### Task Cancellation and Task Groups

It is very important to understand that structured concurrency with task groups can make your code return partial results to the caller instead of a complete collection. Consider the code in Listing [5-20](#532302_1_En_5_Chapter.xhtml#PC20), which is from the ImageDownloader project from Chapter [4](#532302_1_En_4_Chapter.xhtml).

```
func download(serverImages: [ServerImage]) async throws -> [URL] {
var urls: [URL] = []
try await withThrowingTaskGroup(of: URL.self) { group in
for image in serverImages {
group.addTask(priority: .userInitiated) {
let imageUrl = try await self.download(image)
return imageUrl
}
}
for try await imageUrl in group {
urls.append(imageUrl)
}
}
return urls
}
Listing 5-20
Downloading multiple images in a Task Group
```

If you have 10 images to download, and an error were to occur (or you cancel the group calling `group.cancelAll()`) within the `withThrowingTaskGroupCall` before all images are downloaded, the `URLs` array will contain a partial result consisting of only the images it managed to download before the unusual exit took place. Make sure you document this behavior, as callers to your code may not be aware of this and may cause their programs to have unexpected behavior.

## Unstructured Concurrency with Detached Tasks

We have mentioned that the task tree ensures tasks inherit some properties from their parents such as priority, actor, and local variables. It is entirely possible to launch a task from another task that doesn’t inherit anything from its parent. These are called *Detached Tasks* , and they are useful when you need concurrency, but this concurrency is not strictly related. For example, downloading images from the internet could be one task. You may later want to store those images in a local cache. The process of storing the images in a local cache can be a detached task, that way if the task is cancelled but the image is downloaded, the cache-saving operation will proceed without an issue.

To launch a detached task , you simply use the `detached` static method of task . Listing [5-21](#532302_1_En_5_Chapter.xhtml#PC21) shows how we can create one.

```
Task.detached(priority: .low) {
imageManager.writeImageToCache(image)
}
Listing 5-21
Creating a detached task
```

Syntactically, there’s not much difference when it comes to standard tasks but be aware of the task tree and inheritance properties when you work with them. Specifying the priority is optional, just like when working with normal tasks.

## Summary

This chapter was packed with a discussion of unstructured concurrency and the task tree. We gave a lot of coverage to the Task object and how it behaves when it comes to creating concurrency. This type of concurrency is called unstructured concurrency , and it gives us more control, allowing us to cancel the tasks manually or passing them around as arguments.

We talked about how structured concurrency works when used alongside `Task`s. We said that calling async methods within a Task will make the methods child tasks of the parent task. These child tasks inherit all the properties of the parent including priority, actor, and local variables. Launching async methods does not mean they run in a different task. Tasks are always explicitly created with the `Task {}` object.

We also talked about the task tree and how it governs the execution of your tasks. The task tree gives you a mental model of what kind of information is being inherited by child tasks. Also, it governs how cancellation takes place.

We learned that task cancellation is not straightforward. “Cancelling” a task simply means that will be marked as cancelled, but the tasks themselves are responsible for finding an appropriate time to stop execution, as forcefully stopping asynchronous code can have disastrous consequences for your users, such as data corruption. We also touched on how task cancellation works with task groups and how it can cause them to deliver partial results, which may be unexpected.

Finally, we talked about detached tasks , which are a form of unstructured concurrency that don’t inherit anything from a parent task. These tasks are completely independent.

## Exercises

Improve the Project

Download the “Chapter [4](#532302_1_En_4_Chapter.xhtml) - ImageDownloader” project and do the following changes:

1.  Change it so the images are no longer downloaded with a task group. Instead, it should use unstructured concurrency with Task to download each image. Hint: You can create a separate view, called ServerImageView, with an accompanying view model to download a single image.

2.  Add the ability to let users cancel an independent image download task if they tap the “Cancel” button.

3.  SwiftUI includes a view modifier called `.task`, which will execute its contents when a view appears, and it will cancel the execution when the view goes out of view, essentially replacing the behavior for the `onAppear` and `onDisappear` modifiers. Use it to automatically download an image when its placeholder view (which can be a `ProgressView`) appears.

By the end of this exercise, your program should look similar to Figure [5-8](#532302_1_En_5_Chapter.xhtml#Fig8).

![](images/532302_1_En_5_Chapter/532302_1_En_5_Fig8_HTML.jpg)

A screenshot depicts a mobile screen with three photos loading, along with time, cellular, and battery symbols on the top.

Figure 5-8

The UI that shows when images are downloading

If you tap the “Cancel” button (or if an error occurs), the UI should look similar to Figure [5-9](#532302_1_En_5_Chapter.xhtml#Fig9).

![](images/532302_1_En_5_Chapter/532302_1_En_5_Fig9_HTML.jpg)

A screenshot depicts a mobile screen with three error-loading photos, along with time, cellular, and battery symbols on the top.

Figure 5-9

An image download has been cancelled, or another error has occurred

And naturally, if no errors occur, the images should appear like they originally did in the base exercise.

You can find my solution in the “Chapter [4](#532302_1_En_4_Chapter.xhtml) - ImageDownloaderWithTask” project.

# 6. Actors

So far, we have covered about writing concurrent code in Swift using tasks that are independent of each other. You run some tasks, and oftentimes deliver the results to the main thread. But there are cases in which you need to have *shared mutable state* in your program – a common mutable source that your code can read from and write to. This source can be a simple variable, or it can be a file, or it can be any other kind of resource that is dangerous to access concurrently, yet it needs to be available to multiple tasks at the same time.

If you have multiple pieces of code that can access a read-only resource, your program will always be in a valid state, and you do not have to worry about data corruption in that case. But if you have multiple tasks and at least one of them can write data to a shared resource, then we have a problem. This is called a *data race* , or, as we called it in Chapter [1](#532302_1_En_1_Chapter.xhtml), a *race condition.* Race conditions are dangerous, because if multiple pieces of code can write concurrently to a file, it will lead to data corruption. If multiple tasks are reading from that resource, all of them are going to have trash data that may not even be the same they attempted to read. The worst part is, if you are writing concurrent code at the low level, introducing data races is very easy to do, and very hard to debug.

For this reason, if you are using value semantics (structs or enums), you will not have this problem. Value semantics are read-only, and if they mutate, a copy is created, and all mutation is *local* to that variable. Each task would operate on a different copy of the data, although it may not be what you want.

In Chapter [1](#532302_1_En_1_Chapter.xhtml), we saw that the low-level fix solution to this problem is to manually synchronize access to the resources by using locks. But in the new Swift concurrency model, we have a much easier way to create a such synchronization: *Actors*.

## Introducing Actors

Actors are a new type of reference types introduced in Swift , supported by the new concurrency model. Actors isolate their own state from the rest of the program, and they provide synchronized access to mutable state. In simpler terms, if you have an actor that has a simple variable that can be read by and written to by multiple tasks, the task ensures only one of them can do so at a time. It ensures mutual access to a given resource.

Every access to a mutable state is done through the actor. To declare an actor, you simply use the `actor` keyword and give it a name, very similar to declaring a struct or a class. Actors, being reference types, have a lot of features like that of classes. They can conform to protocols and receive more functionality through extensions. The main difference with classes is actors isolate their own state. An actor may suspend zero or more times (have other `await` calls within its methods), so you can integrate actors with the rest of the new concurrency system easily.

Listing [6-1](#532302_1_En_6_Chapter.xhtml#PC1) shows how to declare an actor. In this case, we will also create a Videogame struct which we will use for the examples in this chapter.

```
actor VideogameLibrary {
var videogames: [Videogame] = []
func fetchGames(by company: String) -> [Videogame] {
let games = videogames.filter { $0.title.caseInsensitiveCompare(company) == .orderedSame }
return games
}
func countGames(by company: String) -> Int {
let games = fetchGames(by: company)
return games.count
}
func add(games: [Videogame]) {
self.videogames.append(contentsOf: games)
}
func fetchGames(by year: Int) -> [Videogame] {
let games = videogames.filter { $0.releaseYear == year }
return games
}
}
Listing 6-1
Declaring an actor
```

We have a Videogame object referenced here, whose declaration is in Listing [6-2](#532302_1_En_6_Chapter.xhtml#PC2).

```
struct Videogame {
let title: String
let releaseYear: Int
let company: String
}
Listing 6-2
The Videogame struct
```

We can appreciate that it’s declaration looks very similar to a class, and it being a reference type, we can expect its behavior to be similar. You might be wondering why actors are reference types instead of value types. The reason is simple: An actor encapsulates *shared* mutable state . It’s a type that expects to be mutated by multiple code paths. If it were a struct, callers mutating it would end up with their own copy.

## Interacting with an Actor

All calls to an actor need to be done asynchronously, hence needing the `await` keyword. This is the mechanism actors use to synchronize their state and prevent concurrent mutations. Because a call to an actor can be `await`ed, other callers to it will be suspended, waiting their turn to access it.

Internally, there is no need for the actor to call its own methods and properties asynchronously. Because the actor is isolated, any calls within itself will run sequentially until an operation is complete.

Listing [6-3](#532302_1_En_6_Chapter.xhtml#PC3) shows how we might try to add some games to a videogame library .

```
let library = VideogameLibrary()
func addGames(to library: VideogameLibrary) {
let zelda5 = Videogame(title: "The Legend of Zelda: Ocarina of Time", releaseYear: 1998, company: "Nintendo")
let zelda6 = Videogame(title: "The Legend of Zelda: Majora's Mask", releaseYear: 2020, company: "Nintendo")
let tales1 = Videogame(title: "Tales of Symphonia", releaseYear: 2004, company: "Namco")
let tales2 = Videogame(title: "Tales of the Abyss", releaseYear: 2005, company: "Namco")
let eternalSonata = Videogame(title: "Eternal Sonata", releaseYear: 2008, company: "tri-Crescendo")
let games = [zelda5, zelda6, tales1, tales2, eternalSonata]
library.add(games: games)
}
Listing 6-3
Adding games to a library
```

The `addGames` method is just a convenience function to quickly add some videogames. However, if you tried to compile this, you would get an error because “Actor-isolated instance method ‘add(games:)’ can not be referenced from a non-isolated context”.

The method is expecting us to add the `await` keyword. Despite the fact that the `add(games:)` method is not marked as async, the API exposed to us is indeed async as a protection provided by the actor. The compiler is helping you misuse actors, avoiding the introduction of data races .

The direct implication of this is that actors can only run in async contexts , so ultimately, to get that to compile, we need to both add the `await` keyword to the `add(games:)` method and wrap it in a `Task` (or `Task.detached`). The fixed method that compiles is in Listing [6-4](#532302_1_En_6_Chapter.xhtml#PC4).

```
func addGames(to library: VideogameLibrary) {
let zelda5 = Videogame(title: "The Legend of Zelda: Ocarina of Time", releaseYear: 1998, company: "Nintendo")
let zelda6 = Videogame(title: "The Legend of Zelda: Majora's Mask", releaseYear: 2020, company: "Nintendo")
let tales1 = Videogame(title: "Tales of Symphonia", releaseYear: 2004, company: "Namco")
let tales2 = Videogame(title: "Tales of the Abyss", releaseYear: 2005, company: "Namco")
let eternalSonata = Videogame(title: "Eternal Sonata", releaseYear: 2008, company: "tri-Crescendo")
let games = [zelda5, zelda6, tales1, tales2, eternalSonata]
Task {
await library.add(games: games)
}
}
Listing 6-4
Interacting with actors needs to be done in an async context
```

When multiple calls to the `add(games:)` method are performed, the actor will only execute one of those calls at once. In Listing [6-5](#532302_1_En_6_Chapter.xhtml#PC5), we have created a new method that adds even more games to the library.

```
func addNewGames(to library: VideogameLibrary) {
let pokemon1 = Videogame(title: "Pokémon Yellow", releaseYear: 1998, company: "Game Freak")
let pokemon2 = Videogame(title: "Pokémon Gold", releaseYear: 1999, company: "Game Freak")
let pokemon3 = Videogame(title: "Pokémon Ruby", releaseYear: 2002, company: "Game Freak")
let games = [pokemon1, pokemon2, pokemon3]
Task {
await library.add(games: games)
}
}
Listing 6-5
Adding more games to the library
```

We now have two methods that add games. Let’s try calling them now, as in Listing [6-6](#532302_1_En_6_Chapter.xhtml#PC6).

```
addGames(to: library)
addNewGames(to: library)
Listing 6-6
Calling two methods that call the actor
```

Now this is where things become interesting. There is no guaranteed to know if the games from `addGames` or `addNewGames` will be added first. But what is guaranteed is that whichever gets to execute first, the actor will ensure all the games in one of these functions are added to the array before the other one gets to run. So, the array will either contain all the games from `addGames` first, in the order they were added, followed by the games in `addNewGames` in order they were added, or it will contain the games of `addNewGames` in the order they were added followed by games from `addGames` in the correct order as well. The games will never be added in an interleaved order or anything crazy like that.

Moreover, access to the entire actor state is synchronized. If you have someone adding games to the library and someone querying the collection at the same time, the actor will only execute one of those calls until it is done executing. Listing [6-7](#532302_1_En_6_Chapter.xhtml#PC7) will create three different tasks , two to add games and one to filter the games by a given year and print how many of them there are.

```
Task {
addGames(to: library)
}
Task {
let games1998 = await library.fetchGames(by: 1998)
print("\(games1998.count) games")
}
Task {
addNewGames(to: library)
}
Listing 6-7
Accessing the actor from different tasks
```

`addGames` has one game released in 1998\. `addNewGames` also has one game released in 1998\. In total, there’s two games released in that year. Ideally, the code above would print `2 games`, but because the tasks attempt to access the actor concurrently, only of three tasks will get to execute its content first. If you try to run this code, you will notice it prints `0 games` most of the time. It could also print `1 games`. Mutually exclusive access means that only one person will enter the actor and do its work, it doesn’t matter what method of the actor is being called. Any call to the actor will block it until it’s done doing its work.

This is also a good chance to show you how task priorities work. You can tell the compiler that you want the games to be added first by assigning a higher priority to the tasks that add games. Listing [6-8](#532302_1_En_6_Chapter.xhtml#PC8) changes the priority of the tasks . `.utility` is the lowest possible priority, so in this case, it will execute last *most of the time*, printing `2 games`.

```
Task(priority: .high) {
addGames(to: library)
}
Task(priority: .utility) {
let games1998 = await library.fetchGames(by: 1998)
print("\(games1998.count) games")
}
Task(priority: .high) {
addNewGames(to: library)
}
Listing 6-8
Assigning priorities to tasks
```

### Nonisolated Access to an Actor

There will be times in which you know that accessing an actor will not cause data races . For these cases, you can mark some methods in your actor as `nonisolated` .

To demonstrate this, we will add a new type and property to our `VideogameLibrary`. We will add the owner information to it. Listing [6-9](#532302_1_En_6_Chapter.xhtml#PC9) shows this new type.

```
struct Owner {
let name: String
let favoriteGenre: String
}
Listing 6-9
The new Owner type
```

We will now modify the actor to have this new property, alongside an initializer. Listing [6-10](#532302_1_En_6_Chapter.xhtml#PC10) shows these modifications.

```
actor VideogameLibrary {
var videogames: [Videogame] = []
let owner: Owner
init(owner: Owner) {
self.owner = owner
}
func fetchOwnerInfo() -> String {
return "\(owner.name) (\(owner.favoriteGenre))"
}
//...
}
Listing 6-10
Adding the owner property to the actor
```

Note that we also added a `fetchOwnerInfo` method. This will help us get a concatenated string with the owner’s name and favorite videogame genre.

Finally, Listing [6-11](#532302_1_En_6_Chapter.xhtml#PC11) initializes the library with this new type.

```
let owner = Owner(name: "Andy Ibanez", favoriteGenre: "JRPG")
let library = VideogameLibrary(owner: owner)
Listing 6-11
Initializing the VideogameLibrary with the owner info
```

Now, if you wanted to call the `fetchOwnerInfo` method , you will need to call it asynchronously, as seen in Listing [6-12](#532302_1_En_6_Chapter.xhtml#PC12).

```
Task {
let ownerInfo = await library.fetchOwnerInfo()
print(ownerInfo)
}
Listing 6-12
Calling the fetchOwnerInfo method
```

But in this example, we know that there’s nothing dangerous about accessing the owner info synchronously. Because it is an immutable struct, we should really be able to call it without needing to do so with `await`. We can tell the compiler that this method is nonisolated so we can call it as we would any other method. Listing [6-13](#532302_1_En_6_Chapter.xhtml#PC13) shows how this is done.

```
nonisolated func fetchOwnerInfo() -> String {
return "\(owner.name) (\(owner.favoriteGenre))"
}
Listing 6-13
Marking a method as nonisolated
```

And because of this, not only can we lose the `await` keyword (you will get a helpful warning if you leave it), but we also no longer need to call this method in an asynchronous context. In Listing [6-14](#532302_1_En_6_Chapter.xhtml#PC14) we create a new method that prints the owner info of a `VideogameLibrary`. Note that function is not `async`, it does not use a `Task`, and the `fetchOwnerInfo` method no longer needs to be awaited.

```
func printOwnerInfo(of library: VideogameLibrary) {
let ownerInfo = library.fetchOwnerInfo()
print(ownerInfo)
}
Listing 6-14
Calling a nonisolated method doesn’t require asynchronicity
```

## Actors and Protocol Conformance

There is one important thing you need to keep in mind if you are planning on making your actor conform with protocols , or even extend them with extensions.

When you intend to conform to a protocol, you will find issues along the way if the protocol has mutable requirements. For example, take a look at the new type in Listing [6-15](#532302_1_En_6_Chapter.xhtml#PC15).

```
actor Game {
let name: String
let year: Int
init(name: String, year: Int) {
self.name = name
self.year = year
}
}
extension Game: Equatable {
static func == (lhs: Game, rhs: Game) -> Bool {
return lhs.name == rhs.name
}
}
Listing 6-15
Making an actor conform to a type
```

We are making the actor conform to the `Equatable` protocol so we can compare two `Game`s with the `==` operator. This example will compile fine, because the `==` function relies on two immutable properties, and because the `==` function itself does not require our actor to be mutated.

However, consider the conformance in Listing [6-16](#532302_1_En_6_Chapter.xhtml#PC16).

```
extension Game: Hashable {
func hash(into hasher: inout Hasher) {
}
}
Listing 6-16
Conforming an actor to the Hashable Protocol
```

Attempting to use this conformance as-is will cause Swift to yield an error saying that the actor-isolated method `hash(into:)` cannot be used to satisfy a protocol requirement. The compiler is expecting `hash(into:)` to be a synchronous operation.

In this case, we can easily solve the issue by adding the `nonisolated` keyword to the method. Listing [6-17](#532302_1_En_6_Chapter.xhtml#PC17) adds the missing keyword.

```
extension Game: Hashable {
nonisolated func hash(into hasher: inout Hasher) {
}
}
Listing 6-17
Marking hash(into:) as nonisolated
```

This will effectively satisfy the compiler, but you should always stop to consider if this makes sense. Will the `hash(into:)` method depend on the isolated properties? If so, any attempts you do to implement this method will likely be broken. You may find times in which it really is not easy to conform to protocols that expect their methods to be synchronous.

## Actor Reentrancy

When we enter an actor, the actor may suspend if it has a lot of work to do, calling other asynchronous methods. This can lead to a common problem known as the Actor Reentrancy problem .

The actor reentrancy problem occurs when you assume the overall state of your program before it reaches an `await` call. Let’s explain this with an example. We will add a new method to our existing `VideogameLibrary` actor. Listing [6-18](#532302_1_En_6_Chapter.xhtml#PC18) shows this new method.

```
func addGamesAndPrintResults(_ games: [Videogame]) async throws {
let existingGameCount = videogames.count
// Imagine a long-running operation is taking place here.
try await Task.sleep(nanoseconds: 1_000_000_000)
add(games: games)
let newGameCount = existingGameCount + games.count
print("Games before: \(existingGameCount)")
print("Games now: \(newGameCount)")
}
Listing 6-18
The addGamesAndPrintResults method illustrates the actor reentrancy problem
```

This new method will:

1.  Get the number of games currently in our game library.

2.  Sleep and await the task for a second.

3.  Add games to the library.

4.  Print how many games were there before we added new games.

5.  Print how many games there are now.

The actor reentrancy problem occurs when multiple tasks access this method. When one task accesses it, it will store the current game count in the `existingGameCount` variable. It will then hit a lengthy `await` call. In the meantime, a second task could access this method and also store the number of games in a new `existingGameCount` variable. If the first task finishes before the second one leaves the await, the `existingGameCount` variable in the second task will essentially hold incorrect data, because the library has more games than it had before.

To better illustrate this problem, take a look at Figure [6-1](#532302_1_En_6_Chapter.xhtml#Fig1).

![](images/532302_1_En_6_Chapter/532302_1_En_6_Fig1_HTML.png)

A model diagram represents andysLibrary, Task 1, and Task 2, which includes videogames dot count equal to 3, and with codes inside Tasks 1 and 2.

Figure 6-1

The actor reentrancy problem illustrated

Two tasks are accessing the actor to add more games. We assume the library has 3 games already. The first task adds 4 games to it, and the second one adds 2\. At the end of their executions, Task 1 will print that there are now 7 games, and Task 2 will print that we have 5\. But both tasks entered at the same time, and we know that actors prevent the state to be modified at the same time. So, one of the tasks is printing incorrect data.

Each of the tasks is printing the results as if it were the only one accessing the actor and printing it. If Task 1 finishes before Task 2, Task 2 should know that the library had in fact 7 games, and so it should have printed 11 games total.

The problem here is that we assumed how many games we had before we entered the `await` call. There is no guarantee that after we leave a suspension point the state of the program will be as it was before. We are *checking our assumptions* before an `await`ed call.

Unfortunately, actors cannot protect you in this situation. This is purely a logic issue that is up to the developer. But a good rule of thumb is that you should check for your assumptions *after* an awaited call. In this case, our assumption is the number of games in the videogame library. It would make more sense if we calculated that after we come back from a suspension. By simply moving the `existingGameCount` variable, the program will be fixed. Listing [6-19](#532302_1_En_6_Chapter.xhtml#PC19) places the `existingGameCount` variable in a more logical place.

```
func addGamesAndPrintResults(_ games: [Videogame]) async throws {
// Imagine a long-running operation is taking place here.
try await Task.sleep(nanoseconds: 1_000_000_000)
let existingGameCount = videogames.count
add(games: games)
let newGameCount = existingGameCount + games.count
print("Games before: \(existingGameCount)")
print("Games now: \(newGameCount)")
}
Listing 6-19
Moving the assumptions to a more appropriate place
```

By moving our assumptions to be after the await call, Task 1 and Task 2 may still print different results in each call, but one thing is for sure, that no matter who finishes first, the task to finish second will have all the right data to show to the user.

## Actors and Detached Tasks

Recall that detached tasks don’t inherit anything from a parent task. This includes the actor a parent task may be running on. Actors are not inherited. Consider the new methods added to VideogameLibrary in Listing [6-20](#532302_1_En_6_Chapter.xhtml#PC20).

```
func playRandomGame() {
guard let game = videogames.randomElement() else { return }
print("Playing \(game.title)")
}
func playRandomGameLater() {
Task.detached {
await self.playRandomGame()
}
}
Listing 6-20
Launching an actor method from a detached task
```

The `playRandomGame` method is isolated to the actor. But note that `playRandomGameLater` requires we call `self.playRandomGame` with `await`. This is because the moment we launched the detached task, we jumped to a different actor. Being in a different actor will force us to call the actor’s method with await.

## General Tips for Working with Actors

Actors are great for those situations in which you have a shared mutable state . It makes accessing mutable data from multiple parts easy, but it is not a silver bullet by any means.

To make effective use of actors, here’s a list of tips I recommend you keep in mind when you are working with them:

1.  Make your actors as small as possible. While actors will protect you from a lot of misuse thanks to the Swift compiler, it will not protect you from problems such as actor reentrancy. Keeping your actors small will help you make effective use of them.

2.  Keep all your actor’s operations *atomic*. An atomic operation is treated as a single unit. Keeping your methods short will help with this. In general, atomicity means that you either complete all your work at once, or if a single operation fails, the entire operation is discarded. Avoiding `await` calls within actors as much as possible will aid with atomicity.

3.  All your assumptions about the needed state should go after the `await` calls when used within an actor’s methods. It is very easy to do if your method contains a single await call, but if you have multiple, it can be hard to reason about your program’s state.

4.  Do not use an actor as an `ObservableObject` in SwiftUI . The compiler has no issues allowing you to do that, but ObservableObject will expect that it will always run on the main thread. Having an actor with expensive calls will result in visible lags in your SwiftUI app.

## Summary

In this chapter, we learned about actor reference types. These types are great when we need to have a shared mutable state in our program. Shared mutable state is data that multiple processes may want to access asynchronously. Allowing asynchronous access to mutable data is dangerous and can lead to data corruption. Actors have a mechanism that helps them automatically isolate their own state from the rest of the program and provide synchronized access to itself, preventing its data from being written to at the same time by multiple processes and avoiding corrupted reads. We can opt out of this isolated behavior for specific methods by using the `nonisolated` keyword.

Due to its isolated status, all calls to the actor outside of itself must be done asynchronously, using the `await` keyword, unless they are marked as `nonisolated`. The Swift compiler will not let you access the actor synchronously as it will complain about the lack of the `await` keyword.

As actors are normal types, they can conform to protocols and have behaviors similar to that of classes. The main difference between the two is the isolation of the actor. If we want to conform to a protocol, we need to keep in mind the protocol’s requirements for mutation or other requirement that would need certain conformances to be nonisolated. It will not always be possible to conform to all protocols, as marking certain methods as `nonisolated` could prevent us from getting the behavior we want.

# 7. Sendable Types

The simplest concurrent programs are fairly isolated, even when they are dealing with concurrency. But in complex programs, you will need to pass data around across isolation boundaries, from one async task to another, from one actor to another. The data will need to be *thread-safe*. As we learned with Actors, sharing mutable data across asynchronous domains can result in data races and therefore data corruption.

Swift needed a way to define a safe way to share data across isolation boundaries. The Swift Evolution folks came up with a very elegant solution and introduced *sendable types.*

## Understanding Sendable Types

A sendable type can be shared across asynchronous tasks and across actors. By definition, a sendable type needs to be protected against data races. This type should be impossible to mutate from more than one operation at the same time. These types should offer synchronized access only.

Out of the box, Swift has some sendable types that are safe to use concurrently, including:

1.  Value types. Value types such as enums and structs are sendable as long as they don’t have any properties that aren’t sendable. As we discussed when we talked about actors, value types are immutable. If you attempt to mutate a value type, a new copy is created with the new data. This makes structs and enums work out of the box with the new concurrency system. Multiple copies may be created, but an existing copy will always exist as-is until it is deleted.

2.  Actors. Actors were made to share mutable data, so their whole purpose of existing was to be used in the new concurrency model. You can share actors across isolation boundaries without any constraints, keeping in mind that they may have `nonisolated` functionality. Actors are always sendable.

3.  Classes. These *can* be made sendable, but they will need to have certain constraints. Recall that classes are reference types and any modifications to an instance of a class is a modification to the real data. No copy is created unlike structs. A class marked as `final` that has read-only properties can very easily be made sendable. Another way to make a class sendable, if it has mutable properties and is not marked private, is by implementing your own synchronization mechanism within the class. This sounds hard to do – and it is.

4.  Methods and closures can be made sendable, but explicitly.

In many cases, Swift can infer the sendability of types. Structs and actors are one example. But if you do the work to make a class thread-safe, Swift has no way to do that, and it will stop you from using it in a way the compiler deems dangerous.

### The Sendable Protocol

Sendable types in Swift outside of value types and actors conform to the `Sendable` protocol. By simply conforming to this protocol, the compiler will check your usage of sendable types to ensure you are not trying to use them in a way that may result in data races.

#### Analyzing Sendable Types

To better understand how sendable types behave, we will analyze them to learn about their quirks and special considerations.

##### Structs

Most of the time, structs will be sendable out of the box. But there are situations in which they can’t be. Imagine that we have a struct that has a property with a class type, like in Listing [7-1](#532302_1_En_7_Chapter.xhtml#PC1).

```
class TVShow {
let title: String
var rating: Int
init(title: String, rating: Int) {
self.title = title
self.rating = rating
}
}
struct TVShowLibrary {
var shows: [TVShow] = []
}
Listing 7-1
A struct with a property which is an array of classes
```

This code declares a struct, `TVShowLibrary` , whose only property is an array of `TVShow`s. `TVShow,` however, is a mutable class. It makes sense that we will want to change the rating of a TV show a few months after watching it.

If you try to cross isolation boundaries (declaring a `TVShowLibrary` in a nonisolated context and capturing it within a Task.detached `{}`, for example) with this struct, the compiler will stop you, even though it is a struct. Normal `Task {}` captures will not be affected, because they will inherit the actor from the top context, which in this case is the `@MainActor`. In Listing [7-2](#532302_1_En_7_Chapter.xhtml#PC2), we declare a `TVShowLibrary` instance and then we capture it within two `Tasks`.

```
var tvShowLibrary = TVShowLibrary()
let show = TVShow(name: "Card Captor Sakura")
tvShowLibrary.shows += [show]
Task {
tvShowLibrary.shows.first?.rating = 20
print("Current score Task 1: \(tvShowLibrary.shows.first!.rating)")
}
Task {
tvShowLibrary.shows.first?.rating = 30
print("Current score Task 2: \(tvShowLibrary.shows.first!.rating)")
}
Listing 7-2
Capturing a struct with non-sendable types in Tasks
```

Intuitively, you may think that the compiler should stop you from doing this. After all, you are capturing a mutable value and mutating it from two tasks at the same time! But this is perfectly acceptable because both `Task` calls have the same actor of whatever launched them. If you launched the tasks from a non-asynchronous context, they would inherit the main actor.

And the output will always be what you expect it to be. The task above will always print the rating is 20, and the task below will always print the rating is 30\. No race conditions will take place.

Structs will be sendable by default, unless they have properties that are not sendable as well. Our `TVShowLibrary` object is not sendable, because it has an array of `TVShows`. `TVShow` is a class, which is not sendable by default. Because `TVShow` is not sendable, our entire `TVShowLibrary` is not sendable either. We can observe this behavior better if we change the Task {} calls from Listing [7-2](#532302_1_En_7_Chapter.xhtml#PC2) into detached tasks. Listing [7-3](#532302_1_En_7_Chapter.xhtml#PC3) makes this change.

```
var tvShowLibrary = TVShowLibrary()
let show = TVShow(name: "Card Captor Sakura")
tvShowLibrary.shows += [show]
Task.detached {
tvShowLibrary.shows.first?.rating = 20
print("Current score Task 1: \(tvShowLibrary.shows.first!.rating)")
}
Task.detached {
tvShowLibrary.shows.first?.rating = 30
print("Current score Task 2: \(tvShowLibrary.shows.first!.rating)")
}
Listing 7-3
Capturing mutable non-sendable types from detached tasks
```

Now this is more interesting. Because `Task.detached` does not inherit the actor from the top context, each of them run completely independently. That would certainly cause data races, and Swift is there to protect you from it. Figure [7-1](#532302_1_En_7_Chapter.xhtml#Fig1) shows the errors you would get.

![](images/532302_1_En_7_Chapter/532302_1_En_7_Fig1_HTML.png)

A screenshot represents the TVShow code, which consists of the variables tv show library and Task. detached declared twice with Tasks 1 and 2 highlighted to indicate the reference.

Figure 7-1

The compiler protecting you from data races

Note

There is a difference to how Xcode 13 and Xcode 14 behave in this scenario. In Xcode 13, you will get the error shown in Figure [7-1](#532302_1_En_7_Chapter.xhtml#Fig1). In Xcode 14, you will get the same messages, but as warnings.

The most sensible solution, in this case, is changing the `TVShowLibrary` so it is an actor instead of a struct. This is the perfect usage for an actor because multiple tasks are interested in mutating it.

##### Classes

Classes are not sendable by default. You can, however, conform to the `Sendable` protocol when they are marked as `final` and have read-only properties. You can mark the `TVShow` as conforming to `Sendable`, and your code would compile, but not without getting any warnings. Figure [7-2](#532302_1_En_7_Chapter.xhtml#Fig2) shows the warnings we get when attempting to do so.

![](images/532302_1_En_7_Chapter/532302_1_En_7_Fig2_HTML.png)

A screenshot represents the code of the class TV Show, which consists of seven lines of code and the first and second lines with some errors highlighted.

Figure 7-2

Conforming a class to sendable

Marking the class as final will silence the top warning, but not the bottom one. Making the rating a `let` variable will silence the bottom warning, but we will not be able to mutate the score later. As a rule of thumb, if you have classes that need to be sendable, marking them as final and making everything read-only is the way to go, but it is not possible, as it is the case here.

Ultimately, the only way we could fix the class is by opting out of compiler safety checks. To do this, add the `@unchecked` attribute before the `Sendable` conformance. Listing [7-4](#532302_1_En_7_Chapter.xhtml#PC4) shows the new `TVShow` class that does not yield any warnings.

```
class TVShow: @unchecked Sendable {
let name: String
var rating: Int = 0
init(name: String) {
self.name = name
}
}
Listing 7-4
Opting out from compile-time checks
```

This needs to be said again. You are opting out from compiler checks when you use `@unchecked`. Because this class has no synchronization of its own, it **will** cause data races when multiple tasks or actors mutate it at the same time. You can add your own synchronization mechanism by using something like `NSLock`. Listing [7-5](#532302_1_En_7_Chapter.xhtml#PC5) shows a very simple implementation of this .

```
class TVShow: @unchecked Sendable {
let name: String
var rating: Int = 0 {
willSet {
mutex.lock()
}
didSet {
mutex.unlock()
}
}
let mutex = NSLock()
init(name: String) {
self.name = name
}
}
Listing 7-5
Manually implementing a lock to the synchronize state
```

By using the lock, we can lock it before we set the variable, and unlock It after it is set. This example is simple enough, but more complex classes won’t have the same benefit.

Caution

I do not recommend you use this code in production unless you truly understand how the concurrency primitives such as locks work. The new concurrency system aims to aid developers into not concerning themselves with such details.

##### Closures

In Swift, closures are first-class citizens. You can pass closures and function references around other parts of your code to call them anytime. You have likely seen this when working with the `filter`, `map`, and `reduce` methods of collections in Swift. To recap, Listing [7-6](#532302_1_En_7_Chapter.xhtml#PC6) shows a simple use of the `filter` method.

```
library.shows.filter { $0.rating >= 10 && $0.rating <= 30 }
Listing 7-6
Using the filter method
```

Because you can pass functions and closures around other pieces of your code, you can be more than certain that they should be able to be sendable as well.

Neither functions or closures can conform to protocols, but we can use the `@Sendable` attribute instead. When we use this attribute, the compiler will understand that it doesn’t need to synchronize for anything, because the body of `@Sendable` functions are sendable, and sendable closures can only capture sendable types.

If you ever look at the documentation for `Task`, you will see that its signature is the same one in Listing [7-7](#532302_1_En_7_Chapter.xhtml#PC7).

```
@frozen struct Task where Success : Sendable, Failure : Error
Listing 7-7
Declaration for Task
```

It has a generic type `Success` that conforms to `Sendable`. So tasks operate on `Sendable` types. Now let’s take a look at the declarations for its initializer and for the `detached` static method. They are both in Listing [7-8](#532302_1_En_7_Chapter.xhtml#PC8).

```
@discardableResult init(
priority: TaskPriority? = nil,
operation: @escaping () async -> Success
)
//...
@discardableResult static func detached(
priority: TaskPriority? = nil,
operation: @escaping () async throws -> Success
) -> Task
Listing 7-8
Declarations for a Task’s initializer and the detached static method
```

We have established that `Success` is something that conforms to `Sendable`. This is how Swift does its magic to ensure that non-sendable types cannot be transferred to other concurrent domains. The initializer takes a closure that returns a `Sendable` type, and the `detached` method returns a `Task` with the same requirements.

We can create our own `Sendable` functions with this same behavior. Listing [7-9](#532302_1_En_7_Chapter.xhtml#PC9) declares a method with a `@Sendable` closure . This method will print something before executing the method, and something after doing so. You could use a function like this to benchmark your methods.

```
func logBeginningAndEnd(operation: @Sendable () -> Void) {
print("Calling Closure")
operation()
print("Closure finished")
}
Listing 7-9
Declaring a method that takes a @Sendable closure
```

Now let’s try calling this method, passing something that is not sendable. Listing [7-10](#532302_1_En_7_Chapter.xhtml#PC10) creates a new `TVShowLibrary` instance and makes use of this method.

```
var tvShowLibrary = TVShowLibrary()
logBeginningAndEnd {
let showsCount = tvShowLibrary.shows.count
print("\(showsCount) Shows")
}
Listing 7-10
Calling a @Sendable closure capturing a non-sendable type
```

The compiler will not let you compile this, and you will see the same errors as in Figure [7-1](#532302_1_En_7_Chapter.xhtml#Fig1).

You know that in Swift, you can pass an actual function to a closure parameter. Listing [7-11](#532302_1_En_7_Chapter.xhtml#PC11) attempts to pass a function to a method that expects a closure, but the function we are passing is not `@Sendable` .

```
func doSomething() {
}
logBeginningAndEnd(operation: doSomething)
Listing 7-11
Passing a non-sendable function to a sendable closure
```

Swift will protect you here as well, but with a warning. Swift will warn you that “Converting non-sendable function value to ‘@Sendable () -> Void’ may introduce data races”.

So, if you are certain that your functions can be shared across different threads, don’t forget to mark them as @Sendable so the system doesn’t send out any unnecessary warnings, like in Listing [7-12](#532302_1_En_7_Chapter.xhtml#PC12).

```
@Sendable func doSomething() {
}
Listing 7-12
Marking the doSomething function as Sendable will silence the warning
```

And naturally, don’t go on marking every single function or method you have as `@Sendable`. Only do this when you are certain the functions are safe to use in different concurrency domains and you intend to use them as such.

Finally, if you have a variable that can store a closure, you can constrain it to `@Sendable` closures as well. Listing [7-13](#532302_1_En_7_Chapter.xhtml#PC13) declares a class which has a property that can store a `@Sendable` closure .

```
class MyClass {
var aClosure: @Sendable () -> Void
init(aClosure: @escaping @Sendable () -> Void) {
self.aClosure = aClosure
}
}
Listing 7-13
Declaring a variable that can store a @Sendable closure
```

Finally, when you pass in a closure to a method, and you want that closure to be @Sendable, you can use the @Sendable in syntax at the top of your closure to mark it as such. Listing [7-14](#532302_1_En_7_Chapter.xhtml#PC14) passes an explicit `@Sendable` closure to the `MyClass` initializer.

```
let myObject = MyClass { @Sendable in
let a = 3
}
Listing 7-14
Using the @Sendable in syntax
```

In this specific example, it’s not necessary to do this, because `MyClass` is already treating the closure as `@Sendable`, but there will be times in which this will be useful, like when we talk about async sequences.

## Summary

In this chapter, you learned about the mechanism that Swift uses to ensure that data cannot be mutated across different concurrency domains, or different threads. The `Sendable` protocol for types and the `@Sendable` attribute helps us tell the compiler that sharing data across concurrent domains should be allowed because they are protected against data races. Many types will have the conformance automatically by default, such us read-only structs and `actor` types. Swift will check for Sendable conformance when attempting to share these types across different threads. If we have a type that can be sendable but the Swift compiler cannot infer it, we can mark it as `@unchecked @Sendable` to tell Swift that this is indeed a sendable type, but we are responsible for synchronizing its internal state.

# 8. The Main Actor & Final Actors

In Chapter [6](#532302_1_En_6_Chapter.xhtml), we learned about actors. Actors are reference types that synchronize their own internal state, so it is safe to mutate them from different threads. This is done by guaranteeing that only one task or thread can mutate the actor’s state at a time. Actors are sendable types meant to be used in a concurrent context. Actors are types that you instantiate and, for the most part, treat as normal classes. But what happens when we need *something* that is always guaranteed to run on the same thread, but when it may be spread out, even across different files?

Before we study global actors, I want to talk about an old friend of ours: the main thread. I want to do this now because it is linked to this chapter’s main topic.

## The Main Thread

If you have been programming for Apple platforms for a while, you know about The Main Thread . If this is the first time you hear about it, worry not, because we are going to have a refresher on it in this section. The main thread’s sole responsibility is to run your UI code. If you want to update a label, add some color to view, or toggle a switch, you need to do those operations on the main thread. We are not allowed to update our UI outside the main thread. Doing so will result in warnings in the best case and runtime crashes in the worst case. If we are running asynchronous tasks – whether with the new concurrency system or with older tools such as the GCD – and you are interested in showing the returning data in your UI, you need to do that on the main thread.

Because the main thread is responsible of updating your UI , doing any expensive operations on it will leave your app hanged for a few seconds, or it can leave your app with visible stutters and visible frame drops in the UI. If your app hangs for a long time, then the system will take matters in its own hands and kill it. This is the reason a lot of APIs, including all the networking calls in `URLSession`, default to running in separate threads, doesn’t matter if you use the `async/await` variations of these methods or the traditional closure-based ones. You *can* force `URLSession` and other asynchronous APIs to run on the main thread, but you absolutely shouldn’t, and I doubt you will ever find a good excuse for it.

In short, anything you do with UIKit (and by extension, SwiftUI) needs to be done on the main thread, and you need to avoid clogging it up with expensive work. Traditionally, we would handle data to the main thread calling something like `DispatchQueue.main.async`. This method takes a closure that executes on the main thread so it’s ideal to work with your UI there. `DispatchQueue` is an object from the GCD, and the `main` static property refers to the `main queue`, which always runs on the main thread. This is a quick and painless way to get back to the main thread, safe, but it is also a fact that it can create pretty pyramids of doom if you are using it extensively. Any object that starts with the “UI” prefix (`UIButton`, `UISwitch`) in iOS or any AppKit object in macOS should be mutated from the main thread only.

Tip

As of Xcode 14 and all OS releases accompanying it, `UIImage` and `UIColor` are the exception to the rule that states “all UI-prefixed objects should be mutated from the main thread.” Starting on this Xcode version, `UIImage` and `UIColor` conform to the `Sendable` protocol , so it is safe to pass them across different concurrency domains.

In the case of the modern concurrency system , the system will decide if it should launch your tasks in different threads. Remember we learned that our tasks suspend when they hit an `await` keyword and the system decided to launch that call in a different thread. If a suspension takes place, you do not know where the code will execute, or even what thread it will use to deliver its results. How can we deliver results to the main thread when using the new concurrency system, so we can update our UI after finishing an expensive task? Well, I’m glad you asked!

### The Main Actor

I can finally explain to you something that we have been using throughout the book, but it was hard to find an appropriate time to explain it. Yes, I am talking about the main actor, which in code appears as `@MainActor`.

UIKit and SwiftUI are both big frameworks. If you think about it, they are made up of hundreds if not thousands of classes and they are likely spread out in different files within the framework. They both have the requirement that any UI update should be done on the main thread. Marking a declaration as `@MainActor` means that that specific piece of functionality will run on the main actor. “`@MainActor`” is essentially treated as an attribute. As iOS 15, macOS 12, watchOS 8, and tvOS 16, most of the classes in these frameworks are marked with `@MainActor`. In the case of SwiftUI, creating `ObservableObjects` should manually be marked as `@MainActor` as the protocol doesn’t come with it out of the box. Marking all objects in UIKit, SwiftUI, and AppKit as `@MainActor` is a very elegant solution to making sure all of them run on the main thread.

#### Using the @MainActor

Just like the `@Sendable` attributes , you can use the `@MainActor` “attribute ” in many different places as well. First, you can use it as part of a type declaration, like a struct or a class. Listing [8-1](#532302_1_En_8_Chapter.xhtml#PC1) declares two new types, one of them is marked as `@MainActor`.

```
struct MusicAlbum {
let name: String
let artist: String
let releaseYear: Int
}
@MainActor class MusicLibrary {
var name: String
var albums: [MusicAlbum] = []
init(name: String) {
self.name = name
}
}
Listing 8-1
Adding @MainActor to a class
```

Because of this, every operation we want to perform on `MusicLibrary` instances needs to run on the main actor as well. Every property and method within `MusicLibrary` will run on the main actor as well. Imagine you have a function, `createLibraryAndAddAlbum`, that is created outside the context of the `MusicLibrary` object itself. You may be tempted to work on your music library within this new function. In Listing [8-2](#532302_1_En_8_Chapter.xhtml#PC2), we attempt to use this type in a context that does not run on the `@MainActor`.

```
func createLibraryAndAddAlbum() {
let library = MusicLibrary(name: "Andy's Library")
let album = MusicAlbum(name: "Imaginareum", artist: "Nightwish", releaseYear: 2011)
library.shows += [album]
}
Listing 8-2
Working on a MusicLibrary without being on a Main Actor context
```

If you try to compile this, you will get errors that are very similar to what you would get when attempting to send non-sendable types across different concurrency domains. Namely:

```
Call to main actor-isolated initializer 'init(name:)' in a synchronous nonisolated context
...
Property 'albums' isolated to global actor 'MainActor' can not be mutated from this context
```

By adding `@MainActor` to `createLibraryAndAddAlbum`, the function will be running on main actor too. Both the `MusicLibrary` and `createLibraryAndAddAlbum` function will be running on the same actor (the same thread), and it doesn’t matter if these two are in the same file or even across different frameworks. This is the reason the main actor is called a *global actor*.

Listing [8-3](#532302_1_En_8_Chapter.xhtml#PC4) will compile without an issue.

```
@MainActor
func createLibraryAndAddAlbum() {
let library = MusicLibrary(name: "Andy's Library")
let album = MusicAlbum(name: "Imaginareum", artist: "Nightwish", releaseYear: 2011)
library.albums += [album]
}
Listing 8-3
We can add @MainActor to declarations that are spread out, even in different files or frameworks
```

If you are in a different actor, you can call methods marked as `@MainActor`, but doing so requires you to do it asynchronously (using `await` or `async let`), because the `@MainActor` synchronizes its own internal state across all declarations that use it.

Now, this is where a lot of people get confused when working with the main actor.

Suppose you add the method in Listing [8-4](#532302_1_En_8_Chapter.xhtml#PC5) to the `MusicLibrary` object.

```
@MainActor
func populateFromWebService() async {
albums = []
albums = try await WebService.shared.albumsFromService()
}
Listing 8-4
An asynchronous method to fetch albums
```

Assume that the `populateFromWebService` method is a long-running operation that will suspend. Where will the actor execute?

The long-running operation, the `populateFromWebService` function itself, will still run on a different thread. You do not know where it will run. You just know that your method on the main actor will suspend, and the thread will be doing other work before coming back to you.

What will execute on the main actor itself is the *assignment* of the albums methods. Just the assignment. `populateFromWebService` will be running somewhere else, but the assignment of its returned data to `self.albums` will run on the main actor. A lot of people are under the impression that `populateFromWebService` itself would execute on the main actor. That is, if this method is downloading some JSON data from a slow service and parsing it or doing anything else that can take anything longer than milliseconds, it is running on the main thread. That is not correct, and fortunately the global actors system is smarter than that. If you are running something on `@MainActor`, always know that all its statements will run on the main actor, but if it finds a call that suspends, the execution of whatever triggered the suspension will be done somewhere else, and the data will be returned (and assigned) on the main thread. The “left” side of an `await` will execute on the main thread, and the “right” side of the await keyword will take place someplace else, so to speak.

You do not have to mark an entire class or struct declaration as main actor. If you only require some properties and methods to run on the main actor, you can mark only those as `@MainActor`. Listing [8-5](#532302_1_En_8_Chapter.xhtml#PC6) modifies the `MusicLibrary` object to add a new variable and a new method, gets rid of the `@MainActor` mark in the entire declaration, and adds it to the newly added symbol.

```
class MusicLibrary {
var name: String
var albums: [MusicAlbum] = []
@MainActor var albumCover: UIImage?
init(name: String) {
self.name = name
}
@MainActor func updateAlbumCover() {
// ... Download a new cover from the internet
// and update the albumCover property.
}
}
Listing 8-5
Marking only some properties and methods as @MainActor
```

Now this class will be usable everywhere, but whatever action we want to do on the `albumCover`, it must be done in a context that is running on the main actor.

If you isolate an entire class to run on the main actor (by marking it as `@MainActor class MyClass`…), then that class will always run on the main actor and calling it from somewhere else will need to await. If you have some methods that need to be nonisolated to the main actor (as a normal actor definition, global actors can have methods that are nonisolated), you can mark it as `nonisolated` and use it from any other concurrent context.

Listing [8-6](#532302_1_En_8_Chapter.xhtml#PC7) marks the `MusicLibrary` class as running on the main actor and adds a nonisolated method to it.

```
@MainActor
class MusicLibrary {
var name: String
var albums: [MusicAlbum] = []
@MainActor var albumCover: UIImage?
init(name: String) {
self.name = name
}
nonisolated func libraryPurpose() -> String {
"This library holds my favorite songs"
}
}
Listing 8-6
Adding a nonisolated method to a class running on the main actor
```

Note that `nonisolated` methods need to be *truly* isolated. You cannot return a property derived from one of our properties in the class. We would not be able to return the `name`, the `albums`, or even the number of albums (`albums.count`), because those properties are isolated to the main actor, and we cannot return isolated properties through a nonisolated context. It is also not possible to use the `nonisolated` keyword with stored properties.

Just like `@Sendable`, we can also mark closures and functions with `@MainActor` to run them on the main thread. We have seen how a function can be marked as `@MainActor`. For closures, you can use a syntax similar to `@Sendable,` but by replacing `@Sendable` with `@MainActor`. In general, wherever you can use `@Sendable`, you can also use `@MainActor`. This includes a variable closure declaration, a closure declaration as a parameter to a function, and more.

Listing [8-7](#532302_1_En_8_Chapter.xhtml#PC8) shows how to mark a closure as `@MainActor`.

```
Task { @MainActor in
}
Listing 8-7
Marking a closure as running on the @MainActor
```

In this particular example, we are declaring a Task that will run on the main actor. I wanted to show you this so you can see that you can really use `@MainActor` everywhere. It may not be a good idea to run a closure on the main thread unless it is to update your UI.

You can also run any code on the `@MainActor` by using a static `run` method. Listing [8-8](#532302_1_En_8_Chapter.xhtml#PC9) shows how this is done.

```
Task {
await MainActor.run {
// Update your UI.
}
}
Listing 8-8
Running code on the main actor
```

The `await` keyword is necessary. Because we may not be in a context that is also running on the main actor, the calls need to be done asynchronously. This method of running code on the main actor is useful. If you have to keep writing closure-based code and you can’t migrate to `async/await` completely just yet, you can use this to quickly relay information back to the main thread without having to use the GCD and its `DispatchQueue.main.async` method.

## Understanding Global Actors

To understand what a global actor is, the easiest way to do so is by understanding the main actor.

As we said before, the main actor is a global actor. A global actor can be applied to various declarations, even if they are across different files in a framework. Earlier, we mentioned that UIKit, the main framework to make iOS apps other than SwiftUI, and its equivalent macOS counterpart, AppKit, have their types spread out across different files. There had to be a way to ensure these UI classes ran on the main thread in a way that played well with the new Swift concurrency system.

An actor that can be applied across different declarations, even if they are in different files, is a *global actor*. As you have seen throughout this chapter, the main actor is a very important part of developing modern concurrent apps on Apple platforms, because it provides us with a way to do work on the main thread to update our UI. The main actor will synchronize all access to it to ensure all UI updates are done properly, with no race conditions.

`@MainActor` is a global actor Apple provides to us. It’s great, it does its work, but can we create our own global actors? The answer is a loud yes!

### Creating Global Actors

You can create your own global actors to isolate them from the rest of the program, similar to how the `@MainActor` isolates its state to the main thread for UI updates. To do so, declare a struct that will be used as an attribute, and give it a static property named `shared`. Mark this struct with the `@globalActor` attribute as well.

Listing [8-9](#532302_1_En_8_Chapter.xhtml#PC10) declares a global actor called `@MediaActor` .

```
@globalActor
struct MediaActor {
actor ActorType { }
static let shared: ActorType = ActorType()
}
Listing 8-9
Declaring our own global actor
```

Now everything we mark as `@MediaActor` will run on the same actor, synchronizing the global actor’s state automatically and ensuring no data races take place. In this example, we named it `MediaActor` , but you can name your actors anything that makes sense in the context of your app, such as `@DatabaseActor` .

In the example in Listing [8-10](#532302_1_En_8_Chapter.xhtml#PC11), we will create a global `Videogame`s array. We will then read and write it from multiple different places to show our `@MediaActor` in action.

```
struct Videogame {
let id = UUID()
let name: String
let releaseYear: Int
let developer: String
}
@MediaActor var videogames: [Videogame] = []
Listing 8-10
Creating a new object and a global variable that runs on our MediaActor
```

Note

in this example, videogames is a global variable. In general, I do not condone the usage of global variables like this, but I think it’s a very good example to learn about global actors.

Now, suppose you have a view controller where you can perform operations on this array. We can add a random videogame, remove a random videogame, and retrieve the information of a random videogame . Listing [8-11](#532302_1_En_8_Chapter.xhtml#PC12) shows a short version of such a view controller.

```
@MainActor
class ViewController: UIViewController {
@MediaActor
func addRandomVideogames() {
let zeldaOot = Videogame(name: "The Legend of Zelda: Ocarina of Time", releaseYear: 1998, developer: "Nintendo")
let xillia = Videogame(name: "Tales of Xillia", releaseYear: 2013, developer: "Bandai Namco")
let legendOfHeroes = Videogame(name: "The Legend of Heroes: A Tear of Vermilion", releaseYear: 2004, developer: "Nihon Falcom")
videogames += [zeldaOot, xillia, legendOfHeroes]
}
@MediaActor
func removeRandomvideogame() {
if let randomElement = videogames.randomElement() {
videogames.removeAll { $0.id == randomElement.id }
}
}
@MediaActor
func getRandomGame() -> Videogame? {
return videogames.randomElement()
}
override func viewDidLoad() {
super.viewDidLoad()
Task {
await addRandomVideogames()
await removeRandomvideogame()
if let randomGame = await getRandomGame() {
print("Random game: \(randomGame.name)")
}
}
}
}
Listing 8-11
Adding operation to operate on videogames
```

This looks like it has a lot to unpack, but it’s very straightforward.

First, the `class ViewController` declaration is marked as `@MainActor`. In all the OS versions released with Xcode 13, we do not need to add the `@MainActor` attributes to classes, but I decided to add it so you can see a declaration can have multiple symbols that are run on different actors. Most of this class will run on the `@MainActor`, but the `addRandomVideogames`, `removeRandomVideogame`, and `getRandomVideogame` will all run on our new `@MediaActor`. Because they all have our own @MediaActor attribute, they can interact with the global videogames variable we declared earlier. And all of them will interact with it in a thread-safe manner because all of them are isolated to `@MediaActor`. It’s a beautiful system where we can mark distributed portions of our code to run on the same thread or actor, and all the internal synchronization of it will prevent us from introducing data races.

In `viewDidLoad` , we will run all videogame operations at once. We will add a videogame, we will remove a videogame, and then we will fetch one to print its info. It wouldn’t make sense to mark `viewDidLoad` as `@MediaActor`, and even if we tried to, we would be stopped because this method is known to modify your UI in most implementations. `viewDidLoad` must run on the main actor.

Because `viewDidLoad` runs on `@MainActor` and all our videogame operations run on `@MediaActor`, if we want to access one global actor from the other global actor, we need to do so *asynchronously*. For this reason, we create an asynchronous context with `Task {}`, and we need to either `await` on each videogame operation or call it concurrently with `async let` if it makes sense to do so. Fetching a videogame is also an asynchronous operation, so we can use await on the right side of the if let to call it.

Now, if we want to have another function that operates on these videogames, `showAvailableVideogames`, it can be anywhere. Suppose we have an imaginary Functions.swift file, completely independent of the `ViewController` and the actor declaration, we can just add the new function there, add the `@MediaActor` attribute, and anything running on the `@MediaActor` can interact with it synchronously and everything outside of it asynchronously. Listing [8-12](#532302_1_En_8_Chapter.xhtml#PC13) shows this new method.

```
@MediaActor
func showAvailableGames() {
for game in videogames {
print("\(game.name)")
}
}
Listing 8-12
The showAvailableGames method is in its own file, but it’s still isolated to the @MediaActor
```

## Summary

Global Actors allow us to mark declarations spread across different files and other logical or physical locations, such as different frameworks, and have them run in the same thread. We say these declarations are *isolated* to the actor they are running on. The global actor will synchronize all its accesses, and for the developer, it is as easy as using the global actors as attributes to ensure they run on the same actor and avoid data races.

Apple provides the `@MainActor` global actor in all its platforms that have a UI. This includes iOS, macOS, watchOS, and tvOS. Every UI element, whether it comes from UIKit, AppKit, or SwiftUI, runs isolated to the main actor, and if we want to update or refresh our UI, we need to go the `@MainActor` before running these updates. This will ensure all accesses to the main thread are synchronized by the main actor, it will refresh our UI , and we will not see warnings or errors related to drawing our UI outside of the main thread.

We can create our own global actors for our own functionality. For example, if we had a framework that interacted with a database, we could add our own global attribute to all its classes to ensure all reads and writes happen in the same thread to avoid data races.

# 9. AsyncSequence

In Swift, we have the concept of *Sequences*. Formally, `Sequence` is a protocol that types that need sequential and iterated access to their elements depend on. Arrays conform to `Sequence`, because you can iterate through them via a `for-in` loop, the `forEach` method , and with higher-order functions such as `filter`, `map`, and `reduce`. You can simply access their elements with a numbered index such as `myArray[2]`. Dictionaries are very similar. You can iterate over their keys and values in the same ways as an array, and to access an element in a dictionary, you can simply call `myDictionary[myHashableKey]`. Sets behave similarly, and you can even implement your own types that conform to `Sequence`.

The `AsyncSequence` protocol, provided by the new concurrency system, allows us to achieve similar functionality, but for asynchronous types. Is a Sequence with asynchronicity added on top – `AsyncSequence`!

## Introducing AsyncSequence

An `AsyncSequence` behaves almost like a sequence. You can do almost everything you can with a normal sequence, from iterating over it, to applying higher-order functions . There is one key difference to `Sequence`s: `AsyncSequence`s do not store any values of their own. It’s not like an array that is storing its data in memory. Strictly speaking, it isn’t a value generator either. Instead, an `AsyncSequence` is a mere interface that allows you to access values that are being emitted over time, right as they become available. These values are emitted asynchronously, and as such you need to `await` on them as they become available.

We have actually used an `AsyncSequence` before. Back in Chapter [5](#532302_1_En_5_Chapter.xhtml), when we were talking about Structured Concurrency, we learned about task groups. Task groups allow us to run a dynamic number of concurrent tasks and observe their results in an awaited loop. To recap, Listing [9-1](#532302_1_En_9_Chapter.xhtml#PC1) is one of the original examples we used back then.

```
/// Downloads all the images in the array and returns an array of URLs to the local files
func download(serverImages: [ServerImage]) async throws -> [URL] {
var urls: [URL] = []
try await withThrowingTaskGroup(of: URL.self) { group in
for image in serverImages {
group.addTask(priority: .userInitiated) {
let imageUrl = try await self.download(image)
return imageUrl
}
}
for try await imageUrl in group {
urls.append(imageUrl)
}
}
return urls
}
Listing 9-1
Task groups deliver their results using an AsyncSequence
```

Notice the “`for try await...`” part. This `for` loop is using an `AsyncSequence`. We do not know what the concrete type for this `AsyncSequence`. All we know is that the task group is running a variable number of tasks, and as they finish, they deliver a `URL` object to this loop. The `URL`s cannot exist until each task launched by the group is done downloading. But how is this magic done? In order to understand how `AsyncSequence` works, we need to understand how sequences in general work.

### A Short Dive into Sequences and AsyncSequences

`for-in` loops – and by extension, higher-order functions – expect a `Sequence` to work. `Sequence` is a protocol with some constraints that interested types must adopt. One of these requirements is to implement a `makeIterator()` method that returns an iterator. Understanding iterators is not essential to understanding the basics of how sequences work, so for now, you just need to know that iterators are types that conform to `IteratorProtocol`, and this protocol has a requirement to implement a method called `next()` which returns an optional instance of the sequence’s generic type `Element`. In order words, if your sequence is an array of `Int`s, the `next()` method will return an optional Int (`Int?`). During iterations, this method is called for each run of the loop until there are no more elements to return (until it returns `nil`).

Imagine you have the array in Listing [9-2](#532302_1_En_9_Chapter.xhtml#PC2), and you iterate over it, printing a value from it on each iteration.

```
let myArray = [1, 2, 3, 4, 5]
for item in myArray {
print(item)
}
Listing 9-2
Declaring an array and iterating over it
```

This `for-in` loop will run 5 items, and on each iteration, the `item` local variable will store a value returned by `next() -> Int?`. The moment the collection returns `nil`, the iteration stops, and the code will begin executing below the loop. In short, `for-in` iterations are simply syntactic sugar provided thanks to the `Sequence` conformance.

AsyncSequences are not that different. The main differences are that instead of a `makeIterator` method, they have a `makeAsyncIterator` method that, like you may have guessed it, returns an iterator that conforms to the `AsyncIteratorProtocol` protocol. This iterator requires you to implement a `next() async -> Element?` method. Because the `next` method is `async`, we can use it in `for-in` loops and with higher-order functions.

Therefore, Sequences and AsyncSequences are very similar. The main differences are that Sequences do store their data and AsyncSequences deliver them over time from an asynchronous source, and the underlying protocols are non-async and async, respectively, to support their functionality.

### AsyncSequence Concrete Types

There are various types that conform to `AsyncSequence` . In general, you do not need to concern yourself with the underlying type, but it’s interesting to know, because this is also different to how normal Sequences operate.

Whenever you call a higher-order function in an AsyncSequence, such as `drop(while:)`, `filter`, `map`, and `reduce`, a completely new type that conforms to AsyncSequence is returned to you.

If you call `drop(while:)` on an async sequence, you will get a `AsyncThrowingDropWhileSequence` or `AsyncDropWhileSequence`; using filter will return you an `AsyncThrowingFilterSequence` or `AsyncFilterSequence`, and so on for any other higher-order function you perform. There’s too many of them to list, but you get the idea. They can be chained without an issue. Normal sequences return an `ArraySlice<Element>` when the array is mutated, because an array slice is a “window” of the returned elements, it does not contain a copy of the new data, it just “sees” the elements that should be included for the new array. Because AsyncSequences do not contain data, it doesn’t make sense to have “windows” in the original. Instead, these types will restrict or mutate what will be delivered on each iteration, skipping some iterations altogether. Higher-order functions on arrays that return new data such as map will be normal arrays of type `Element`.

## AsyncSequence Example

While we have used AsyncSequences before, I have prepared an example that does not rely on task groups. Instead, we will use the new `lines` property of the `URL` object which opens a file, and reads it line by line, returning a single line on each iteration.

The following example reads a real URL which returns some data. For reference, the file we will read line by line is in Listing [9-3](#532302_1_En_9_Chapter.xhtml#PC3).

```
The Legend of Zelda: Ocarina of Time|1998|10
The Legend of Zelda: Majora's Mask|2000|10
The Legend of Zelda: The Wind Waker|2003|10
Tales of Vesperia|2008|8
Tales of Graces|2011|9
Tales of the Abyss|2006|10
Tales of Xillia|2013|10
Listing 9-3
A file we will read line by line
```

We have a file very similar to a CSV file. When parsing it, we will create different kinds of `Videogame` objects that each have a title, release year, and score.

Each line will be parsed into a Videogame object, which is in Listing [9-4](#532302_1_En_9_Chapter.xhtml#PC4).

```
Struct Videogame {
let title: String
let year: Int?
let score: Int?
init(rawLine: String) {
let splat = rawLine.split(separator: "|")
self.title = String(splat[0])
self.year = Int(splat[1])
self.score = Int(splat[2])
}
}
Listing 9-4
A new Videogame object
```

Now let’s get to the interesting part. Listing [9-5](#532302_1_En_9_Chapter.xhtml#PC5) will oversee loading the videogames from a webserver, and it will parse the whole file in an array of `Videogame`s.

```
Func loadVideogames() async {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/tutorials/async-await/part11/videogames.csv")!
var videogames: [Videogame] = []
do {
for try await videogameLine in url.lines {
if rawVg.contains("|") {
// Valid videogame
videogames += [Videogame(rawLine: videogameLine)]
}
}
} catch {
// Handle the error
}
}
Listing 9-5
Reading a file from a server line by line
```

We begin this function by declaring a URL pointing to the location of the file. Then, we declare an empty `[Videogame]` array where we will append each new videogame as it becomes available.

The `for-in` loop waits for lines (`videogameLine`) which are of type `String`. This is because `url.lines` is an `AsyncSequence`, and we have to `await` on it.

Each time we get a `videogameLine` , we will check if it contains the pipe character `|`, and if it does, we will create a new videogame by passing this raw line to the `Videogame` initializer. The `Videogame` initializer will parse this line and create a `Videogame` instance on each iteration, which will be appended to the `videogames` array.

We need to do this within a `do-catch` block, because our file is in a remote server and an error may occur anytime.

If you have been programming in Swift for a while, you have no doubt noticed that we can create prettier code here. By using higher-order functions on `url.lines`, we can create more idiomatic code and make this more elegant.

In Listing [9-6](#532302_1_En_9_Chapter.xhtml#PC6), we have a new implementation for `loadVideogames` , which will use higher-order functions.

```
Func loadVideogames() async {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/tutorials/async-await/part11/videogames.csv")!
let videogames =
url
.lines
.filter { $0.contains("|") }
.map { Videogame(rawLine: $0) }
do {
for try await videogame in videogames {
print("\(videogame.title) (\(videogame.year ?? 0))")
}
} catch {
}
}
Listing 9-6
Using higher-order functions to create the videogames array
```

Now, unlike normal sequences, the videogames array is not going to be there instantly. If `lines` were a non-async collection, videogames would ultimately be an array of `Videogame`s – `[Videogame]` or `Array<Videogame>`.

Because `lines` is an async sequence , our videogames variable will not be of type `[Videogame]`. Instead, it will be of type, `AsyncMapSequence<AsyncFilterSequence<AsyncLineSequence<URL.AsyncBytes>>, Videogame>`.

This is the reason I said you don’t generally concern yourself with the underlying type of an `AsyncSequence` , but while this signature looks complicated, it’s easy to understand. Going from last to first in the higher-order functions that created the videogames variable :

1.  We got the `AsyncMapSequence` from calling `map()`.

2.  We got the `AsyncFilterSequence` by calling `filter()`.

3.  We got the `AsyncLineSequence`, which is the type of the `lines` property of `URL`.

Unlike normal Sequences, the filter-map operation does not “kickstart” immediately upon declaration. Instead, we get a concrete type for an `AsyncSequence` that we must later feed to a `for-in` loop. AsyncSequences will never “start” until they are used in a `for-in` loop. When you are building and chaining your async sequences, you are simply building a long instruction set that a loop will later use to deliver its data over time. You can think of it as a “query” that tells the loop what it should deliver as data becomes available.

If you use the same async sequence in another loop, you will get the data instantly, because it will be cached. AsyncSequences will never run multiple times getting the data from the async source, as it would be very inefficient.

So, Listing [9-6](#532302_1_En_9_Chapter.xhtml#PC6) will run synchronously without an issue until it hits the loop itself. As the async sequence is executed, it may suspend multiple times for each iteration, getting the data from an asynchronous source.

As you play around with `AsyncSequence` , you will discover it has some more limitations compared to normal `Sequence`s. First, you cannot get the `count` of elements on `videogames`. This is because the variable is not storing data – as we said, it’s just a “query” of sorts that tells a `for-in` loop what it should return on each iteration. You necessarily need to execute the sequence in a `for-in` loop to be able to figure out its `count`. Next, not all higher-order functions that would expect to see from normal sequences are available. `dropFirst` is an example.

Because `await`ed loops can be treated like normal loops, you can do anything you can do in such loops, including using `continue` and `break` statements to alter the execution of the loop. Listing [9-7](#532302_1_En_9_Chapter.xhtml#PC7) makes use of the `continue` statement to only print videogames with a score of 10.

```
For try await videogame in videogames {
if videogame.score == 10 {
continue
}
print("\(videogame.title) (\(videogame.year ?? 0))")
}
Listing 9-7
Using continue statements in awaited loops
```

In this specific example, we could just add a new `filter` call to the `videogames` variable. This would return only the videogames with a score of 10 without having to use the `continue` statement in the loop. Listing [9-8](#532302_1_En_9_Chapter.xhtml#PC8) has the required changes to eliminate the `continue` statement.

```
Let videogames =
url
.lines
.filter { $0.contains("|") }
.map { Videogame(rawLine: $0) }
.filter { $0.score != 10 } // Apply the filter here
do {
for try await videogame in videogames {
print("\(videogame.title) (\(videogame.year ?? 0))")
}
} catch {
}
Listing 9-8
Calling more higher-order functions on the videogames library
```

### Native APIs That Use AsyncSequence

Apple has added many APIs across their SDKs that make use of `AsyncSequence`. `URL.lines` is one, but there’s more. This is not an exhaustive list, but these are the ones I find myself using more often:

*   `FileHandle.standardInput.bytes.lines`, which can be used to receive input from the command line or other sources. Each command is delivered in a `for-in` loop.

*   URLs can access both the `lines` and `resourceBytes` member properties. In this chapter we explored how to use `lines`. `resourceBytes` is similar.

*   `URLSession` has a `bytes(from:)` method, which you can use to download data byte by byte from the network.

*   `NotificationCenter` now has APIs to `await` on new `notifications` of the types you are interested in. Receiving notifications in a for-in loop can greatly help you reduce the logic you need to deal with notification center triggers.

## The AsyncStream Object

In this book, we have explored ways to migrate existing code and patterns to use the new features of the modern concurrency system. For example, we have migrated closure-based code and delegate-based call to use `async/await` instead, simplifying their usage. There are times in which you have an asynchronous data source that delivers data over time. The SDK offers many APIs that receive streams of data in delegates and closures. We can create `AsyncSequence` wrappers for these and have them deliver their data in a loop. Two examples of this would be Bluetooth, which delivers packet data in a delegate call, and CoreLocation , which delivers coordinates, also in a delegate call. Wrapping them in AsyncStreams will allow us to expose an API that can be used in `for-in` loops and have a more natural interface.

`AsyncStream` allows us to achieve stream-like functionality that works with `for-in` loop without having to implement any iterators ourselves. We can use it to migrate delegate-based and closure-based calls to receive their data in a loop instead. To achieve this, `AsyncStream` conforms to `AsyncSequence`.

To show you how you can use `AsyncStream`, we will create a small project that will receive CoreLocation data as it becomes available and show it in a UI.

### The CoreLocationAsyncStream Project

Create a new SwiftUI project called “CoreLocationAsyncStream ”. This app will receive locations and show them in a list as they are updated. Figure [9-1](#532302_1_En_9_Chapter.xhtml#Fig1) shows a screenshot of the final product.

![](images/532302_1_En_9_Chapter/532302_1_En_9_Fig1_HTML.jpg)

A screenshot of the mobile includes time, WIFI signal, full charge battery, and the screen depicts the stop updating and locations.

Figure 9-1

The UI will show a list of locations as they become available

You can make this app by using delegate-based calls, but we want to use `AsyncStream` to better understand how `AsyncStream` and `AsyncSequence` work. It will also help make your code cleaner and keep anything to do with location in one place only.

#### The LocationUpdater Class

Create a new class called `LocationUpdater.swift` . We will use this class to manage everything to do with location, from asking permission to getting location updates to delivering those updates to interested parties. Make sure you `import CoreLocation`, and give it the contents outlined in Listing [9-9](#532302_1_En_9_Chapter.xhtml#PC9).

```
import Foundation
import CoreLocation
class LocationUpdater: NSObject, CLLocationManagerDelegate {
private(set) var authorizationStatus: CLAuthorizationStatus
private let locationManager: CLLocationManager
override init() {
locationManager = CLLocationManager()
authorizationStatus = locationManager.authorizationStatus
super.init()
locationManager.delegate = self
locationManager.desiredAccuracy = kCLLocationAccuracyBest
}
func start() {
locationManager.startUpdatingLocation()
}
func stop() {
locationManager.stopUpdatingLocation()
}
func requestPermission() async -> CLAuthorizationStatus {
}
// MARK: - Location Delegate
func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
}
func locationManagerDidChangeAuthorization(_ manager: CLLocationManager) {
authorizationStatus = manager.authorizationStatus
}
}
Listing 9-9
The beginning of the LocationUpdater class
```

##### Asking for Location Usage Permission

Before you follow the following steps. Make sure you add the `NSLocationWhenInUseUsageDescription` (`Privacy - Location When In Use Usage Description.`) key to your Info.plist file. This is a user-facing string explaining the user why we need to track their location.

`CoreLocation` delivers the status change of authorization on a delegate call. We can take this chance to use continuations for this. Yes, they are the same continuations we learned about all the way back in Chapter [3](#532302_1_En_3_Chapter.xhtml).

Start by adding a property that will keep track of the continuation that will deliver the authorization status. You can find it in Listing [9-10](#532302_1_En_9_Chapter.xhtml#PC10).

```
private var permissionContinuation: CheckedContinuation?
Listing 9-10
This property will help us bridge the delegate call to async/await for permission status changes
```

Go back to the empty `requestPermission()` method, and complete it with the code Listing [9-11](#532302_1_En_9_Chapter.xhtml#PC11).

```
func requestPermission() async -> CLAuthorizationStatus {
locationManager.requestWhenInUseAuthorization()
if authorizationStatus != .notDetermined {
return authorizationStatus
}
return await withCheckedContinuation { continuation in
permissionContinuation = continuation
}
}
Listing 9-11
Starting the continuation to get the authorization status for location access
```

We are only going to ask for the permission when the authorization status is `.notDetermined`. If we have a different status, we will return it immediately. The reason for this is that permission is always requested when you initialize a `CLLocationManager` object , the permission is requested automatically by the system. If you call the request method while the system is fetching the authorization status, you will discard a continuation without calling `resume` on it, and you will be deadlocked. This is one of the few situations where you can deadlock yourself (if you start a continuation and it goes away before you can resume it), and the good news is that this bug is easy to catch as you run your app to test it.

You will also need to add one more line to the `locationManagerDidChangeAuthorization` delegate method . The finished implementation for this method is in Listing [9-12](#532302_1_En_9_Chapter.xhtml#PC12).

```
func locationManagerDidChangeAuthorization(_ manager: CLLocationManager) {
authorizationStatus = manager.authorizationStatus
permissionContinuation?.resume(returning: authorizationStatus)
}
Listing 9-12
When the authorization status changes, we will resume the continuation
```

In this app, we are only interested in knowing when the permission changes once in our program. It may be a good idea to have an infinite loop that always reports authorization changes. We could do this with an `AsyncStream`, sending location authorization updates every time the delegate method is called and never calling `finish().` When the status changes, we resume the `permissionContinuation` with it, so the awaited calls depending on it can continue executing.

This is all we need to do to request permission one time asynchronously using `async/await` . Listing [9-13](#532302_1_En_9_Chapter.xhtml#PC13) shows how you can use this logic to request permission.

```
let locationUpdater = LocationUpdater()
// ...
let authorized = await locationUpdater.requestPermission()
if [CLAuthorizationStatus.authorizedAlways, .authorizedWhenInUse].contains(authorized) {
// Permission authorized
}
Listing 9-13
Using the requestPermission method to request location access asynchronously
```

##### Receiving Locations in a Loop

Now let’s implement the logic to receive the location objects .

Internally, `AsyncStream` works with continuations. The idea is that when you create an `AsyncStream`, it gives you a continuation where you will send an undefined number of events of the type you are interested in. In this project, we will send objects of type `CLLocation`. The types of these continuations are not strictly the same as that we learned about in Chapter [3](#532302_1_En_3_Chapter.xhtml), but they behave very similarly. The main difference is that the continuations from Chapter [3](#532302_1_En_3_Chapter.xhtml) must be resumed exactly once, whereas `AsyncStream` continuations can `yield()` any number of objects, and when you are done, you need to call `finish()` to make the sequence return `nil` and therefore stop the loop. Not calling `finish()` will result in your `for-in` loop never finishing unless it gets explicitly cancelled – which may be what you want in *some* scenarios, but feel like deadlocks in others.

Add a property that will hold the `AsyncStream` continuation. You can see it in Listing [9-14](#532302_1_En_9_Chapter.xhtml#PC14).

```
private var streamContinuation: AsyncStream.Continuation?
Listing 9-14
Creating a continuation for our CLLocation AsyncStream
```

The generic type of `AsyncStream` tells us what kind of object the for-in loop will receive in every iteration. In this case, `CLLocation`.

Now, we are going to implement a locations property. Its type will be `AsyncStream<CLLocation>`. This is the property `for-in` loops will use to get new location objects as they become available. Add the code in Listing [9-15](#532302_1_En_9_Chapter.xhtml#PC15) to the `LocationUpdater` class.

```
var locations: AsyncStream {
let stream = AsyncStream(CLLocation.self) { continuation in
continuation.onTermination = { @Sendable _ in
self.stop()
self.streamContinuation = nil
}
self.streamContinuation = continuation
self.start()
}
return stream
}
Listing 9-15
We can use the locations property to get CLLocation objects in real time
```

When creating an `AsyncStream`, you need to define what kind of object it will receive. We will stream `CLLocation` objects, so we go with `CLLocation.self`. The initializer also takes a closure that gives you the stream’s continuation, of type `AsyncStream<CLLocation>.Continuation`. We can set up an optional `onTermination` closure on the continuation that will get called after we call `finish()` on an `AsyncStream`. Use this closure if you need to close data pipes or do any other kind of cleanup. In this project, if the user chooses to no longer receive location updates by pressing a button, we will tell the location manager to stop tracking the location. Notice that the continuation is marked as `@Sendable`. This closure may safely be used across different concurrent domains. If you don’t mark it as such, you won’t be able to stop the `locationManager` from delivering updates.

The next line assigns the continuation to our `streamContinuation` variable. We need to keep this variable around because location events are delivered on a delegate method. After we have configured the continuation, we can start tracking the user’s location, by calling the `start()` method . This will start the `locationManager`’s ability to send location updates.

The locations variable can now deliver `CLLocation` objects in real time, but we are not quite done yet. We are not properly calling the `finish()` method , so this loop will never end, and we are not actually delivering location objects anywhere.

We will stop streaming when the `stop()` method gets called. Modify this method so it looks like the one in Listing [9-16](#532302_1_En_9_Chapter.xhtml#PC16).

```
func stop() {
locationManager.stopUpdatingLocation()
streamContinuation?.finish()
}
Listing 9-16
Finishing the continuation
```

When we call `finish()`, the continuation’s `onTermination` closure will be called as well. In this particular example , it may look weird that `stop()` can trigger `onTermination`, and that `onTermination` calls `stop()`. We are doing this because in the event the `Task` that is running the `for-in` loop is cancelled, we want to stop receiving locations as well. You could directly call `locationManager.stopUpdatingLocation`() within `onTermination`, but I prefer the idiomatic approach of calling our own `stop()`. Because we set `streamContinuation` to `nil` within `onTermination`, there is no danger or recursive calls here.

Now we can do the required changes to the `locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation])`. We need to deliver the locations we receive to the continuation. Listing [9-17](#532302_1_En_9_Chapter.xhtml#PC17) shows its final implementation.

```
func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
locations.forEach { streamContinuation?.yield($0) }
Listing 9-17
Delivering the location objects to the continuation
```

You may have noticed the delegate gives us an array of location objects. Each time this method is called, it may have more than one object. So we will iterate over the array of received objects and `yield()` them to the continuation on each call. With this, we have finished the functionality for `LocationUpdater`, and callers can now use the locations property to receive events in real time in a loop.

Listing [9-18](#532302_1_En_9_Chapter.xhtml#PC18) shows how callers can make use of this object to receive location objects in a `for-in` loop.

```
self locationUpdater = LocationUpdater()
//... After confirming we are authorized.
for await newCoordinate in locationUpdater.locations {
// Do something with the newly received newCoordinate object.
}
Listing 9-18
Using the locations AsyncStream
```

##### Finishing the Project

We can now continue writing the rest of the app. Go to the `ContentView.swift` file and create the `ContentViewViewModel` class there. You can create it on a different file if you wish. Its entire implementation is small, so I will put all of it in Listing [9-19](#532302_1_En_9_Chapter.xhtml#PC19).

```
@MainActor
class ContentViewViewModel: ObservableObject {
@Published private(set) var locations: [CLLocation] = []
let locationUpdater = LocationUpdater()
func startUpdating() async {
let authorized = await locationUpdater.requestPermission()
if [CLAuthorizationStatus.authorizedAlways, .authorizedWhenInUse].contains(authorized) {
for await newCoordinate in locationUpdater.locations {
locations += [newCoordinate]
}
}
}
func stopUpdating() {
locationUpdater.stop()
}
}
Listing 9-19
ViewModel for the view that will show the locations
```

We are making the whole class run on the @MainActor due to the fact it can update the UI. The view will read the `@Published locations` property to display them in the list.

The `startUpdating` method will kickstart the operation to receive locations. It will begin by asynchronously requesting permission to track the user’s location. Once we have permission, we will access the `locationUpdater`’s locations property in the loop. This is the `AsyncStream<CLLocation>` we created earlier. `AsyncStream` is an `AsyncSequence`, so we can iterate over it with an `await`. This loop will be infinite until the `stopUpdating` method is called, which will cause the sequence to return nil and hence end the loop.

Finally, we can implement the UI. The UI is also rather small, so I can post it in a single listing. Listing [9-20](#532302_1_En_9_Chapter.xhtml#PC20) shows our completed UI.

```
@MainActor
struct ContentView: View {
@StateObject var viewModel = ContentViewViewModel()
var body: some View {
NavigationView {
List(viewModel.locations, id: \.hash) { location in
Text("\(location.coordinate.longitude), \(location.coordinate.latitude)")
}
.navigationTitle("Locations")
.task {
await viewModel.startUpdating()
}
.navigationBarItems(
leading: EmptyView(),
trailing: Button("Stop Updating") {
viewModel.stopUpdating()
}
)
.navigationViewStyle(.stack)
}
}
}
Listing 9-20
The UI of the app
```

We are done! We use the `.task` modifier to call the view model’s `startUpdating` method . `.task` is called as soon as the view appears, and cancelled when it disappears, so it’s useful to use it when working with concurrency. The “Stop Updating” button will stop the stream.

You can download the finished project, called “CoreLocationAsyncStream ”.

## The AsyncThrowingStream Object

Like many objects in the new concurrency system, `AsyncStream` has its throwing variation which you can use if your loop can throw errors. The object is `AsyncThrowingStream` , and it is as easy to use as `AsyncStream`.

You will be able to `yield` values, `finish` it normally, or `finish(with:)` with an error. If you are using `AsyncThrowingStream`, you will need to have a `for try await` loop.

## Summary

In this chapter, we learned about `AsyncSequence`s. `AsyncSequence`s are almost like normal `Sequence`s, except they work with the new concurrency system. `AsyncSequence`s can deliver their data over time in a `for-in` loop, until the underlying source decides to send a `nil` value. While you can use many of the higher-order functions such as `filter`, `map`, and `reduce`, these operations won’t start until you use your `AsyncSequence` in a loop, unlike Sequences where the operations are triggered automatically as they are chained.

We also learned about `AsyncStream`. We can use `AsyncStream` to bridge closure-based or delegate-based calls that deliver data over time to the `async/await` world. We explored how we can use it with the `CoreLocation` framework, and the considerations you need to keep in mind when using this object. An `AsyncStream` is an `AsyncSequence`, so everything you can do with `AsyncSequence`, you can do with `AsyncStream` . If your sequence can throw, you can use `AsyncThrowingStream` instead of `AsyncStream`.

# 10. @TaskLocal

Swift as a language is rich in a lot of features. The language itself supports the existence of the modern concurrency system, by introducing features that make it possible to catch a lot of common concurrency problems at compile-time rather than runtime. This makes it easy to release very robust apps even if they use a lot of concurrency behind the scenes.

Swift has one very slightly obscure feature – Property wrappers. They are used in many places in SwiftUI (`@State`, `@StateObject`, `@EnvironmentObject`, etc.), but other than that, many developers are not aware that it’s possible to have property wrappers that have nothing to do with SwiftUI. They can even create their own. You can recognize property wrappers almost instantly because they begin with the `@` symbol, although they may be confused with other Swift features (such as attributes or even global actors).

The Swift team has brought an interesting property wrapper that is to be used with the new concurrency system. We are talking about `@TaskLocal`, and it can be used to share data across other tasks in the task tree. As we have learned, the task tree dictates how tasks will inherit some properties, including the actor they are running on, the priority, cancellation status, and *task local variables*. We have already learned how all the previous inherited context works, but we have yet to learn about task local variables.

## Introducing the @TaskLocal Property Wrapper

`@TaskLocal` values can be read and written to in the context of a task. The value is shared implicitly, and it is accessible by any child tasks the task itself may create, whether they are `async let` or `group tasks`.

### Using @TaskLocal

To use this property wrapper, properties marked as `@TaskLocal` must be `static`. They can be optional or have a default value.

To read their values, you don’t need to do anything special. You can attempt to use the value from anywhere as you would any property, but if the value was not set by a parent task beforehand, it will be either `nil` or the default value you assigned it.

Listing [10-1](#532302_1_En_10_Chapter.xhtml#PC1) shows how we declare `@TaskLocal` variables .

```
class ViewController: UIViewController {
@TaskLocal static var currentVideogame: Videogame?
// ...
}
Listing 10-1
Declaring @TaskLocal variables
```

`@TaskLocal` variables are necessarily static. In this example, we are using the `Videogame` struct we created in Chapter [8](#532302_1_En_8_Chapter.xhtml). We can now use this property to share a `Videogame` down the task tree.

Now, Listing [10-2](#532302_1_En_10_Chapter.xhtml#PC2) shows how easy it is to read this variable.

```
// Outside of ViewController
func expensiveVidegameOperation() async {
if let vg = await ViewController.currentVideogame {
print("We are processing \(vg.title)")
}
}
Listing 10-2
Reading a @TaskLocal variable
```

This hypothetical code is somewhere outside the view controller. I want to highlight the fact that `@TaskLocal` properties are not constrained to any scope. They are just constrained to a task itself, and this task can exist in many different places.

The observant may have noticed that accessing this variable is an `await` operation. `TaskLocal` properties synchronize their accesses within the task as to prevent data races from occurring.

Writing to `@TaskLocal` properties is not as straightforward. If you try to do a simple assignment, with the `=` operator, the compiler will stop you because it is read-only property.

Instead of doing a simple assignment, you need to bind the variable down the task tree. To do this, you need to use the `$` symbol before the variable name and call the `withValue` method on it. To avoid confusion, Listing [10-3](#532302_1_En_10_Chapter.xhtml#PC3) shows how to bind a variable to task.

```
override func viewDidLoad() {
super.viewDidLoad()
// Do any additional setup after loading the view.
let vg = Videogame(title: "The Legend of Zelda: Ocarina of Time", year: 1998)
Self.$currentVideogame.withValue(vg) {
// we can launch some async tasks here that make use of the LocalValue
}
}
Listing 10-3
Binding a value that can be used later by tasks
```

If you are not familiar with Swift’s property wrappers, I recommend you read a dedicated blogpost I have on the topic.^([²](#fn2)) But in short, a property can augment the capabilities of a normal property. By using the dollar symbol `$`, we can access the internal capabilities of the property wrapper. Our Videogame struct does not provide the `withValue` method , but the property wrapper wrapping it does.

In Listing [10-3](#532302_1_En_10_Chapter.xhtml#PC3), we are binding a new `vg` variable to our `currentVideogame` task value. All tasks spawned from here on out will have access to it for as long as they are part of the task tree.

Listing [10-4](#532302_1_En_10_Chapter.xhtml#PC4) will create a more complex hierarchy were a `@TaskLocal` value is being shared.

```
class ViewController: UIViewController {
@TaskLocal static var currentVideogame: Videogame?
override func viewDidLoad() {
super.viewDidLoad()
// Do any additional setup after loading the view.
let vg = Videogame(title: "The Legend of Zelda: Ocarina of Time", year: 1998)
Self.$currentVideogame.withValue(vg) {
Task {
await expensiveVidegameOperation()
Task {
await expensiveVidegameOperation()
Task.detached {
await expensiveVidegameOperation()
}
}
}
}
}
}
Listing 10-4
Sharing down a @TaskLocal variable down a more complex hierarchy
```

We start by launching some tasks after binding our `Videogame` task local variable. We start a Task where we call `expensiveVideogameOperation` , which will print “We are processing The Legend of Zelda: Ocarina of Time”. After it `await`s, we launch another `Task`, which is a child of the current `Task`. Calling `expensiveVideogameOperation` will also print “We are processing The Legend of Zelda: Ocarina of Time”, because this child task has access to the same parent.

Things are more interesting when we launch a detached task. When we launch the detached task, we also call `expensiveVideogameOperation`, but this time it prints “No videogame found in the task hierarchy!” As we discussed in Chapter [5](#532302_1_En_5_Chapter.xhtml), detached tasks are completely independent of any task that launched them and they don’t really have a parent to speak of, although they can be parents of other tasks (as long as they aren’t launched as detached tasks too). For this reason, our detached task in Listing [10-4](#532302_1_En_10_Chapter.xhtml#PC4) doesn’t have the `currentVideogame`. It’s not part of the same task tree the previous child tasks were.

You can bind a different Videogame to the @TaskLocal variable within the detached task, and any child tasks of the detached task would have access to it – again keeping in mind that detached tasks are not really its children. Listing [10-5](#532302_1_En_10_Chapter.xhtml#PC5) shows this capability in action.

```
Task.detached {
await expensiveVidegameOperation()
let anotherVg = Videogame(title: "Tales of the Abyss", year: 2005)
await Self.$currentVideogame.withValue(anotherVg) {
await expensiveVidegameOperation()
}
}
Listing 10-5
Assigning the @TaskLocal variable to be used within a detached task
```

## Summary

You may find situations where you need to share a value to all the children in the task hierarchy. The `@TaskLocal` property wrapper is a very good candidate for this. Values will be shared to all the children of a task and all tasks that are within that same task tree, but any detached tasks will not have access to upper-level task local variables due to their detached nature.

Footnotes [1](#532302_1_En_10_Chapter.xhtml#Fn1_source)  Index A Actor-isolated instance method Actor reentrancy problem Actors actor reentrancy problem in async contexts classes games to videogame library as nonisolated priority of tasks and protocol conformance shared mutable state in Swift tasks videogame struct addGames method addNewGames method addTask method Apple’s async/await system async/await system async get properties async keywords async version of authenticateWithBiomatrics fetchFollowingFollowers() fetchUserInfo authenticateAndFetchProfile() await keyword delegate-based code into async/await iOS 13/iOS14 iOS 15 JSON parsing network calls URLSession methods version of the method async methods async get properties Asynchronous programming async let async let constructs/async let binding AsyncSequence APIs AsyncStream object AsyncThrowingStream concrete types ContentViewViewModel class continue statements CoreLocation higher-order functions loadVideogames location objects LocationUpdater.swift class LocationUpdater.swift class reading file URL videogameLine videogame object videogames library Atomic operation authenticateAndFetchProfile() method Availability checks B Biometrics Biometric unlock Blocking mechanism C Callbacks Cancellation CLLocationManager object Closure-based code Closures CNContactPickerViewController Completion handler Concurrency asynchronous programming async let construct closure-based detached tasks multithreading priority structured task cancellation task tree error propagation task cancellation unstructured Concurrent call Concurrent context Concurrent task ContactPicker contactPickerDidCancel(_) method contactPicker(_:didSelect) method ContactsUI ContentViewViewModel class ContentViewViewModel.swift file context.evaluatePolicy Continuations async/await checked closure-based code delegate-based code unsafe withCheckedThrowingContinuation Cooperative task cancellation CoreLocationAsyncStream countFrom1To10() methods countFrom11To20() methods createLibraryAndAddAlbum function D @DatabaseActor Data races Deadlock problem Deadlocks Delegate-based APIs Delegate-based calls Delegate-based code Delegate methods Detached tasks DispatchQueue download(serverImages:) method E expensiveVideogameOperation F fetchData() method fetchData() task fetchData tree fetchImageList method fetchOwnerInfo method fetchUserInfo() function fetchUserInfo method fetchUserInfo(completionHandler:) finish() method forEach method G Global actor Apple definition mark declarations @MediaActor videogame, adding operation ViewController declaration viewDidLoad Grand Central Dispatch (GCD) H Hashable Protocol HealthKit project Higher-order functions I, J, K ImageDownloader project ImageManager class ImageManager.swift file Interruption iOS 13–15 L Linear execution flow Livelocks locationManagerDidChangeAuthorization delegate method locationUpdater’s locations property LocationUpdater.swift M @MainActor attributes call methods definition MusicLibrary nonisolated method populateFromWebService properties/methods running code UI Main thread definition Main Actor UI makeAsyncIterator method makeIterator() method @MediaActor Multiple threads Multithreading and concurrency deadlock Mutex Semaphore starvation problem livelocks multiple tasks parallelism sample app race conditions MusicLibrary Mutex N next() method nonisolated method NSOperation APIs NSOperationQueue NSThread O Objective-C interface OS interruptions P, Q Parallelism Placeholders populateFromWebService method Procedural programming ProgressView promptContact() method pthreads Pyramid of doom R Race conditions requestPermission() method S Semaphores @Sendable attributes @Sendable closure Sendable protocol Sendable types classes closures definition structs Swift ServerImage object Shared mutable state showAvailableGames method Social Media App start() method startUpdating method Starvation stopUpdating method Suspension Suspension point Swift SwiftUI app T Task cancellation Task.checkCancellation()static method Task Groups TaskGroupsApp.swift file Task.isCancelled static property @TaskLocal obscure feature properties property wrapper SwiftUI values variables Tasks Task tree Threads concept multithreading frameworks TVShowLibrary U UIKit and SwiftUI UI-related task URLSession URLSessionDataTask UserAPI.swift file .userInitiated priority UserProfileView UserProfileViewModel.swift V Value semantics Value types videogameLine ViewController ViewController.swift file W, X, Y, Z Web services withCheckedContinuation withCheckedThrowingContinuation withTaskGroup functions withThrowingTaskGroup functions withUnsafeContinuation withUnsafeThrowingContinuation withValue method
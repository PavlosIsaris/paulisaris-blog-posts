---
title: "The 14 habits of highly effective developers (Part 1)"
date: "2019-02-10"
tags: [career,clean-code,productivity]
image: img/posts/14_habits_part1.jpg
---

## Introduction
Many believe that transitioning from an effective Junior-level developer to a mid-level is just a matter of time and experience.
Truth is that the line separating these 2 kinds of developers is very thin and subjective. This article is not going to add more to the endless debate on "What exactly defines a mid-level developer".

To be honest, I firmly believe that something that can shift one's mindset and help in transitioning one from a Junior to a Mid-level or Senior developer is **_habits:_**

>_A habit is something that you start doing systematically until it is no longer something strange to you but comes naturally._ 

>_Forming code-related and work-related habits is of crucial importance when it comes to professional and personal advancement._

Let's see a list of everyday habits that, upon mastering them, will definitely help you get to the next level and progress fruitfully:

### 1. Write small methods
Ideally, no more 20-30 lines of code (LoC) long. This habit is extremely important. It will not only force you to write compact code, but it will help your analytical thinking when it comes to modularize your code. 
Having big methods with a high degree of indentation (many ifs, for loops etc) is a nightmare. It may seem easy and straightforward when you write a method like that, but after some days even you will have a hard time figuring out what this method even does.

>_To add insult to injury, big methods often are not-reusable. They were written to serve only one need in a project and it will be difficult to be used anywhere else._

### 2. Give meaningful names
Both to methods and variables. It is not acceptable for a mid-level developer to have variables named "x" or "xyz", or even "object". The purpose of naming variables with English words is so that they have a meaning. 

>_Communicating with your code is far more important than communicating with documentation or comments._

>_The purpose of comments is to explain the "why", not the "how" in the code._

Having meaningful variables helps you communicate with whoever reads the code better, and may remove the need for an excessive amount of comments.
The same goes both for **variables** and **methods**.
Also, when struggling with naming a method for too long, consider refactoring your code so that the method gets more simple. A name for a good, clean method always comes to mind easier than the name of a cluttered one.

>_When struggling with naming things, take a step back and think of the possibility that the component you are trying to name is too complicated and needs refactoring._

### 3. Don't clutter your methods with many parameters
Having many parameters in a method is a sign for refactoring. More often than not, writing this kind of methods violates the [SRP (Single Responsibility Principle)](https://en.wikipedia.org/wiki/Single_responsibility_principle) meaning that they do too many things.
An efficient, clean method does **one** thing, **well**.
As [Uncle Bob](https://en.wikipedia.org/wiki/Robert_C._Martin) said, three is the maximum arguments acceptable. Although this may not be strict. It gives you an overview of the desired number of arguments in a method.

>_Fight the urge to change some of your method's local parameters into class fields. Consider refactoring your code so that a method does fewer things, or break up your method into 2 separate ones._

Quote by Robert C. Martin: 
_"Functions should have a small number of arguments. No argument is best, followed by one, two, and three. More than three is very questionable and should be avoided with prejudice."_

### 4. Avoid too many methods in a class
As with the number of parameters, the number of methods that a class has is also important.
Big classes with a lot of methods usually signify a component that _knows too much or does too much_. We refer to these components as [God Classes](https://en.wikipedia.org/wiki/God_object) to characterize an anti-pattern of writing highly coupled code.

>If you have many methods in a class, consider how often you will need to enter this class in order to change its behavior, as the code progresses through time. This may violate the [Open–closed principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) stating that "software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification".

### 5. Use LTS / stable releases when using a 3rd party library
Always keep in mind the next guy that will be required to use your code and re-compile the project.
Using LTS (Long Term Support) versions of libraries, plugins and frameworks may not be the best when it comes to shiny new features but it will be better when needing to re-build or re-compile the code sometime in the future. 

>_Fight the urge to use the latest and greatest version of a tool and stick to the safest and more stable one. Your future self and co-workers will thank you!_

### 6. Learn to identify the most common design patterns
That's right, most big projects are built using one or more [Design Patterns](https://en.wikipedia.org/wiki/Software_design_pattern). A Design Pattern defines the description, the relationships and the abstraction level in a component. You don't need to know all of them or be good in all of them, but knowing the most essential will be beneficial not only in terms of thinking and designing but also identifying them in a code base.

>_When able to identify a design pattern in a code base, one is also able to extend it or add more functionality to it, by knowing in which places to look for specific classes and objects._

A well-implemented Design Pattern causes everyone involved in a project to speak the same design language and communicate more effectively through the code.

### 7. Always think of the next guy
Whether it is you, another co-worker, a new employee or even a developer in another company, someone will be required to extend your code or add more functionality to it. 
It is really hard to get a grasp of this since most junior developers are used in a typical University project paradigm where you write the required code and then nobody ever gets involved with it.

Things in a professional setting are somewhat different; You will be asked to write code in a project that was written many years ago, and your code will have to be ready for "the next guy" that may come in a few years.
So whenever you are ready to write a temporary "hack" just to get something working, whenever you add something in the build process and avoid documenting it, whenever you skip refactoring, you are simply adding more [Technical debt](https://en.wikipedia.org/wiki/Technical_debt) that someone will have to deal with in the future.

>_Take the time to review your work every couple of hours. Add needed documentation to README files, delete unused code and files that you temporarily added to the project. When not sure about an architectural or programming decision you took, communicate with someone more experienced in your workplace.
You will not only improve the state of the code you wrote, but also you will get better at handling such situations in the future, and getting used to having your pride hurt. (This is something that will happen all the time when you are not a junior anymore :D )_

## Conclusion
Transitioning from a Junior to a Mid-level developer is not something that happens overnight. Advancing your career and getting better as a professional developer is a matter of forming good habits. In this article, I began laying out the most important habits one needs to form to begin this transition and make an impact as a Software Developer.

Please leave your comments below regarding these habits and stay tuned for Part 2! :-)

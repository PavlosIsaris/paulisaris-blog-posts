---
title: "The 14 habits of highly effective developers (Part 2)"
date: "2019-03-09"
tags: [ career,clean-code,productivity ]
image: img/posts/14_habits_part2.jpg
---

## Introduction

This is the second part of the "The 14 habits of highly effective developers" series. You can also read the first
part [here](https://paulisaris.com/the-14-habits-of-highly-effective-developers-part-1/), or
in [dev.to](https://dev.to/pavlosisaris/how-to-transition-from-junior-to-mid-level-developer-part-1-4gig)

In the [first part](https://paulisaris.com/the-14-habits-of-highly-effective-developers-part-1/) of "The 14 habits of
highly effective developers" series, we began showing the power of **_habits_** in a developer's every day life and how
can we benefit from incorporating simple yet crucial changes into our job, aiming to benefit the most and help leveling
up our careers as software engineers.

Habits are what makes what we are. We are our habits and it is our duty to form them as well as possible. When strong in
a particular work-related or study-related habit, we can efficiently fight things and situations that are holding us
back, like **procrastination**, **burnout**, **boredom**, and the occasional urge to **slack around**.

> If you are going to achieve excellence in big things, you develop the habit in little matters. Excellence is not an
> exception, it is a prevailing attitude.
**Colin Powell**



> We are what we repeatedly do. Excellence, then, is not an act, but a habit.
**Will Durant**


> Moral excellence comes about as a result of habit. We become just by doing just acts, temperate by doing temperate
> acts, brave by doing brave acts.
**Aristotle**

Without further ado, let's continue diving into the 14 habits that will transform one's career forever and will make you
an **effective developer**:

## 8. Get used to having your pride hurt (in a good sense)

We developers are a strange race of people. We talk about things we only understand, in strange ways that make the
context even more incomprehensible. It is only logical that at some point our work will displease a customer or the
product/ project manager. A feature may not have been implemented correctly, or there might have been disambiguation in
the project requirements.
And that's when our pride kicks in;

_It is **definitely** not my fault! How could I do this mistake? Are they idiots? Do they hate me personally? Why don't
they understand the struggle of re-implementing a feature?_

All these thoughts are common to all software engineers and only serve as a bad advisor, bugging us not to accept the
problem and stick to our pride.
It is crucial to understand that (probably) no one has anything personal either with you or your program indeed. If
there has been a misunderstanding, go ahead and make things clear. If there has been a bug, make sure you document it,
fix it and test it. That is what pragmatic and professional software engineers do.
Avoid sticking to your pride or even having your [Imposter Syndrome](https://en.wikipedia.org/wiki/Impostor_syndrome)
kick in.

> Your job is not only to be a great analyst, programmer, and technician, but also to be a great **professional**.
> Having great soft skills can help you in times that your great technical skills simply can't.

## 9. Leave the campground cleaner than you found it

The famous boy scout rule. This actually applies to every aspect of our everyday lives, so software development couldn't
be an exception.
Many a time are we required to extend the functionality of a codebase and add new features. We set up our development
environment, pull the code from version control and open the project, only to see a bunch of `TODO`s, unused methods and
variables, hardcoded numerical and string values and unused dependencies.

And that's when we sit and think whether we should do a little cleanup, given that we are working in this project
already.
But, on the other hand, we are not sure whether we will break more things than fix. What if the method we will rename (
with a better and more descriptive name) is used in other parts of the code (or even in other projects as a dependency)?
Deciding the level of refactoring a project needs is not easy, Unit and integration tests always help in finding
breaking parts in the code, and so method scope does.
> A public method should never be changed before we are sure that we change any client (code which calls this method)
> accordingly. Protected methods should be easier to change, and we only need to check the subclasses for explicit
> implementations. Private methods are usually the easiest since their scope is limited.

Apart from method renaming and refactoring, there are other chores a responsible developer can do, for example, deleting
commented out or unused code, or unused files. Most professional IDEs have tools that let you know whether a method is
used or not.
Don't be afraid of deleting commented our or obsolete code. Obsolete code only adds to the technical debt of a project
and can always be retrieved by checking out to an older point in time from the version control system.

## 10. Don't be afraid to be involved in non-coding stuff

A typical software engineer's job is to write code. Either when analyzing a user story, writing down requirements or
sketching a Database, our ultimate goal is to write good, robust code that does the job well and efficiently.
However, it is of crucial importance to do non-technical tasks and delve into the magical world of soft skills.
Communicating with managers, testers, and clients can seem daunting and tiring, but it can also be of great value.

Building your soft skill and getting better at communicating and managing people will definitely make you a better
professional. You will be able to communicate user stories better and even explain technical details without using
strict technical language (the one that makes people think we are aliens).

In addition, being able to communicate better with managers and clients will also help you become more efficient when
analyzing a new user story or when estimating the time new functionality will take to complete. You will have a better
grasp of what the client wants since you will be able to ask better questions. ;-)

> Unfortunately, some people believe that soft skills aren�t that important. However, almost every employer I�ve ever
> talked to about this disagrees. In a world where job roles are changing rapidly, soft skills will be one of the few
> constants.

## 11. Be a documentation freak

Adding/ removing plugins and 3rd party libraries, making assumptions in code, adding an extra step in the setup or build
process or using a specific version of a command line tool can be a huge pain in the future, if not documented
correctly.
This rule is tightly tied to the "Always think of the next developer" rule described in
the [first part](https://paulisaris.com/the-14-habits-of-highly-effective-developers-part-1/). You should make sure that
you are not the only developer able to set up the project for development or run the production build.

There is actually a pretty simple rule for that. After finished working with a project, clone the repository to a
different machine and try to set it up by only following the instructions in the readme file.
This will most certainly point out the missing instructions and you will then be able to build robust, professional
documentation.

## 12. Give yourself time to relax and exercise

Sure, 8 hour sleeps and (especially) daily workouts are not common to the usual developer. We tend to slack off after
work, watch series and play video games. Don't even get me started with clean eating and avoiding junk food.
Truth is that a healthy diet and a persistent exercise routine will have a vast effect in your work mood and
performance.

When you exercise, you are also increasing blood flow to the brain, which can help sharpen your awareness and make you
more ready to tackle your next big project. Being in your best physical health will help improve your overall
workability. Not only can exercising help reduce body weight and the risk for certain medical conditions, but you also
will have improved cardiovascular health, which will give you more stamina to your everyday tasks.

> When you exercise, your brain releases serotonin that helps you feel better and improves your state of mind, making
> the stresses of work easier to handle. Serotonin is a neurotransmitter in the brain that sends messages to the body to
> stimulate mood and emotion.

Apart from exercise, have a good night's sleep makes you feel better the next morning. Sleep is often the first thing to
give up when life gets busy with heavy workloads, irregular work schedules, school, and parenting responsibilities. All
of these normal, but increasingly time-consuming realities tend to crowd out the time and peace of mind needed for
healthy sleep which will definitely lead to better working memory and overall performance.

Appearing fresh in a meeting and not showing as a teenager who was just pulled an all-nighter playing videogames will
certainly make you better at your job and how you stand among your co-workers and clients.

## 13. Learn to detach from personal feelings

This is tightly coupled with the 8th rule "Get used to having your pride hurt (in a good sense)". However, it is not
only important learning how to be ok with your "developer ego" being hurt. This rule is about learning how to be an
efficient **professional** and avoiding fret over personal issues at work.

It is extremely hard to come to good terms with it, but the client who keeps complaining about the bugs in the product
does not hate you. The coworker who reviewed your code and found some design flaws and some technical debt issues that
need to be improved does not think that you are a bad person nor do they hate you and want to punch you in the face.
Learn to accept the opinions of other people (even if you do not personally agree with them), and do what is good for
the job and the project.

Of course, this does not mean that you should diminish your personality or accept whatever everyone throws at you. You
have the undeniable right to argue, but always keep in mind how this argument can be as fruitful as possible. Do you
just argue for the sake of the argument and to defend your pride, or do you actually have something to propose that will
make the situation better and lead to a win-win equilibrium...?

## 14. Give fruitful advice

A huge differentiator between Junior and Senior developers is their ability to give good advice and conduct beneficial
code reviews. When asked for a piece of advice or to do a code review, avoid thinking of your way of seeing things, but
concentrate on the bigger picture and the "common good".

Like a good superhero, a good software engineer should be direct and ridiculously honest (without being rude, of course)
when giving advice. Do you see something that needs refactoring? Don't be reluctant and say it. You were asked for your
**professional opinion** and your professional opinion you will deliver!

The most crucial part here is to ask a mini internal question every time you are about to give a piece of advice or
point out a flaw or mistake:
_"Do I have to propose anything better?"_
Advice without a proposal for something even slightly better is no better than a complaint. Make sure that you have
something beneficial to add to a fruitful dialog and always keep in mind the bigger picture.

> When being an advanced software engineer, you not only care for your own development but for the advancement of others
> as well. Avoid keeping advice to yourself and start offering fruitful guidelines to others. You will greatly benefit
> from it as you will be responsible for the growth of an entire team of professionals.

## Conclusion

Forming good personal and professional habits is the highway of making one an effective professional. Advancing one's
career is not something that happens overnight, it takes time and -above all- persistence. Your job now is to try and
apply as many of these habits as possible to your everyday life and start transitioning to a true professional!

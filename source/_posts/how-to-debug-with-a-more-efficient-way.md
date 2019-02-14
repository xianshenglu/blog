---
title: How to Debug with a More Efficient Way
tags:
- debug
- efficiency
categories: debug
comments: true
date: 2018-05-23 09:25:34
---

## Preface

There is always sometimes when I ran into a problem and I was stuck in there. After problems solved, I watched back and thought maybe I can fix the problem alone or it shouldn't take me so much time to work it out. So, I decided to write something about how to debug with a more efficient way.

## Steps

### Calm down first

Are you beginning to sweat now? Take a walk if you are and forget about the origin thought.

### Try to figure out what the question you are facing

#### Problems which are in a big project

Well, I hate this but I still meet this in some cases.

At present, I am trying to extract the problem from the big project and reproduce it in a demo which is as minimal as possible. Then, I will try to figure out what the problem I am actually facing with. After all, it's easier to debug in a minimal demo than a big project. Then, fix the demo and fix the project.

Sometimes the problems can't be or are not easy to be extracted. In that case, I would try to disable other functions which might complicate the problems to make the problems easier to fix.

#### Problems which are out of control

If the error is out of your control, for example, you are working with something you basically have no idea, you had better pay attention to the error information. Then find the document and search for something about the error information.

It is quite common to encounter a new problem when we are using a frame which we don't know much about it. If that happens, the quickest way is to find the document and see if the way we use is not right.

#### Problems seem familiar

If you know something about your error, for example, you are running a demo which doesn't behave as you expected, maybe you should stop and ask yourself a few questions:

- What is my expected output?

- Why should the demo behave like that?

In this way, you might say that it should behave like that because the specs said that it should ..... It doesn't matter if you don't remember the details of the specs. In this case, you should go to check the part of the specs and read the specs carefully again to see if it really defines the behavior you expected. And I still suggest you reading the specs again even that you remember the details.

Don't tell me you don't know where the specs is because this shouldn't happen. Or if it really happens, it is better for you to find the specs as soon as possible.

Usually, you will find that you missed something in the specs or just misunderstand the specs or the specs just didn't define the specific behavior.

However, sometimes you are totally right after reading the specs. In this case, I think it's time to ask in the community like [Stackoverflow][stackoverflow] or just ask the specs working group. Before asking, you had better know something about how to ask.

## Ending

Anyway, the last way is [google][google] and I will update when I figure out something new.

[google]: https://www.google.com
[stackoverflow]: https://stackoverflow.com

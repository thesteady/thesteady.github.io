---
layout: post
title:  "Evaluating Open Source Libraries: Five Questions To Ask"
summary: "When evaluating open source libraries (gems, node modules, etc), how do you know if it's worth bringing into your project? Here are a few helpful questions to consider."
date:   2014-02-17 18:52:35 -0600
categories: tech_blog
---

_This post originally appeared on the [Quick Left blog](https://quickleft.com/blog/evaluating-open-source-libraries-five-questions-to-ask/)._

The beauty of writing software in open source languages (e.g., ruby, javascript) is that we can often re-use code written by others by including gems, node modules, or other files into our projects. When relying on the kindness of our software-writing compadres, it's always a good idea to examine the code closely before deciding to include it in your project. Below are five questions to think about when evaluating an open-source library. No one of these questions will necessarily make or break your decision to use a coding library, but when considered together can help you more critically evaluate your options.

### 1\. Does the library do what you need it to do?

This may sound obvious, but make sure that adding a library is actually providing you some benefit. Does the library include a lot of features you _don't_ need? Perhaps there's a simpler solution that would add less code to your project. Choose the right tool for the job. Don't eat peas with a shovel, and don't dig a ditch with a spoon.

### 2\. What is the quality of the code?

You take the time to ensure your code is well-written and well-tested. Why would you introduce someone else's bad code into your project? Knowing that the library you're adding in is reliable is important. Does the library have passing and _meaningful_ tests? Are there a lot of bug reports on GitHub? This may just signal an active user base, but pay attention to the problems being discussed and how rapidly they're being addressed. Would those issues affect you, too? What does the actual code for the library look like? Does it follow accepted style standards and principles of design, or is it a mess?

### 3\. Is the library actively being developed? Is it "done"?

In the world of web development, things can change quickly. A library that's being actively developed will likely be kept up-to-date with any dependencies and external APIs. When was the last time someone committed code on the library, or a version update was released? Does it have outdated dependencies? Are there GitHub issues that seem abandoned? Conversely, a project may not have much ongoing activity if it is 'feature-complete'.

### 4\. How thoroughly is the library documented?

Knowing that a library provides the functionality you need isn't enough. You have to be able to know _how_ to take advantage of that functionality to make it worthwhile. That's where documentation comes in. Does the library you're interested in have any documentation? Does it look thorough and useful?

Other places to look for help in understanding how a library works include:

*   Tests -- the tests should describe how various methods in the library work and what variables they support.
*   Comments in the code -- comments might summarize expected variables and explain complicated procedures.
*   GitHub issues -- both current and past can give you a good idea of how (or how not) to use the library.

### 5\. Does the library have an active user base?

An active user base means that people other than you are encountering (and hopefully fixing!) bugs, asking and answering questions about it on Stack Overflow, and writing blog posts about how they use the library. This can be especially important if the code is not well-documented. Look for active GitHub issues, forks, and how many people have 'starred' the repo. Can you find information about the library on Stack Overflow? If yes, that means you're more likely to get a response when you have a question to post.

### Summary

Taking the time to critically evaluate libraries before adding them to your project can save you headaches in the long run. Keep in mind that the five questions I've discussed are things that I think about when exploring open source libraries, and are meant as guidelines. I hope you've found them helpful!

What do _you_ consider when looking at open source libraries?
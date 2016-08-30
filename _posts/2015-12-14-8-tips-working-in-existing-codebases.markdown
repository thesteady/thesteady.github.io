---
layout: post
title:  "8 Tips To Get Started In An Existing Codebase"
summary: "Getting started on an existing codebase and feeling overwhelmed? You're not alone! It can be hard to figure out what's going on, and even why things are happening. However, by learning to focus on the right pieces of information, you can be contributing to big apps in no time! I'll discuss my tips for accelerating your ramp up speed."
date:   2015-12-14 18:52:35 -0600
categories: tech_blog
---

_This post originally appeared on the [Quick Left blog](https://quickleft.com/blog/8-tips-get-started-existing-codebase/)._

Application codebases can be BIG. Whether it’s a monolithic Rails app, a service-oriented architecture system with many components, or front-end JavaScript that’s turned into a labyrinth of `this` and `_this`, getting started on a big codebase can be **hard**.

As consultants, we need to jump in and get productive quickly in any codebase we encounter. New developers starting their first job at a company might also face this hurdle. How do you dive into an existing codebase and start getting things done without feeling overwhelmed? It’s a question that codeschool students and junior developers ask me frequently. Here are my 8 tips for getting started in an existing codebase.

1.  **Understand the “WHY” of the application**

Why does this application exist? What problems does it solve for its users? Who uses it, anyway? Is it a “Facebook for Cats” or an analytics dashboard for monitoring your ice cream intake? What are the main, high-level features of the application? Understanding the _business domain_ of the application provides context for why features exist and why code might be written in certain ways.

I recommend spending time understanding the purpose of the application _before_ touching the code. Doing so will give you better context for what the different objects in the code represent.

1.  **Tour the Production App**

If possible, explore the actual production application. This is great for getting a high-level overview. If possible, have someone who knows the application well give you a tour of it. You can learn a lot from someone who knows the features well!

1.  **Do a quick review of the code.**

How is the code organized? Is there an API layer? What endpoints are available? If it's a Rails app, what models are present? Rails models are a great place to gain insight into the business domain. What does the database schema hold? Don’t worry about details at this point. Focus on the broad picture.

1.  **Talk it out.**

If possible, talk to other people who’ve worked on the app. They’ll be able to quickly summarize what the application does, how major features work, and warn you of any parts of the code that are particularly brittle, poorly written, or downright scary. Have questions, or stumped about where to start looking for specific code? Ask early and often!

1.  **Look at the tests.**

There are tests, right? Tests can give you a better understanding of what edge cases the application might be handling, or what business logic has been deemed important. Integration or feature tests will clue you into the most important user workflows.

No tests? _Ouch_. Before you write code for a feature or to fix a bug, write a test describing that behavior. Are you changing a method? Write passing tests _before_ changing any code. You’ll not only improve test coverage and provide some clues to the next person, you’re also getting a deeper understanding of how that piece of the application works. You might even find and fix some bugs!

1.  **Review the application environment.**

Is the application running on an older version of a framework or language? Are there dependencies on external data sources? What npm modules or ruby gems are being included? How do you build and deploy the app? When you’re just starting to get comfortable with the code, you won’t need to know every detail about how to push to production. However, you do need to understand how to get it running on your development machine.

1.  **Put on blinders and start coding.**

It can be hard to understand every component right away. Instead of spending days reading code (it probably won’t really stick, anyway), start by diving in with some small bugs or features, if possible. Doing so can help you focus on just a tiny piece of the app, without worrying how everything else fits together.

1.  **Add documentation as you go.**

Did getting things set up on your machine throw you a few curveballs? Add documentation to the README to make it easier for the next person. Did you stumble across undocumented environment variables? Did you need to have something globally-installed that wasn't mentioned? Make sure the README has all that good stuff. For tips on making README documentation awesome, check out Meeka’s post, [README Love: Quick and Easy Tips](https://quickleft.com/blog/readme-love-quick-easy-tips/).

Start a “Business Domain” glossary, if there isn’t already one. As you begin to understand the business domain of an application, you’re learning what different terms mean in the context of the business and in terms of the application. Start a glossary to keep track of what things mean. You _could_ do this just for yourself, but it can be helpful for other people, too! A couple of notes about this:

*   Terms and meaning can change over time. Would a shared glossary be maintained?
*   Teams aren't always on the same page about what a term means. Are different people telling you different things? Document what the different meanings are, and bring it up! The product owner might not realize that they're interpreting something differently than a developer. Building a glossary can be an effective way to highlight existing communication problems.

### Summary

Jumping into a big, existing codebase isn’t easy. But I promise that it **does** get easier over time! The tips above have proved invaluable for helping me get started on an existing codebase quickly. In short:

*   Don’t panic.
*   Ask others for help.
*   Start with the high-level overview before diving in deep.
*   Tests, tests, tests.
*   And finally, make it easier for the next person by adding tests and documentation.

With these strategies, you’ll be completing user stories in no time! What’s your process for learning how application code works? How do you get productive in a flash? I’d love to hear your thoughts in the comments below!
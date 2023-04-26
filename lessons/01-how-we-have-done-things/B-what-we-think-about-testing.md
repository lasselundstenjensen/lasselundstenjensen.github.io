---
title: "What we think about testing"
description: "Motivation for having automated testing"
---

## What we think about testing



<br />
<br />

#### **What we consider important**

- Tests are first class citizens
- Tests should be runnable locally (shift left)
- Testing frequently must not slow you down (treat as production code)
- Testing should feel like a help
- Tests lead to maintainable code

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **Treat tests as production code**

You want the tests to have the same quality as the production code. 

This means that you should give the same attention to the tests as you do for the production code. 

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

This also means that you should refactor the tests when you refactor the production code.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **Shift-left on testing**

How do we achieve this?

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

Vast majority of the tests are runnable locally. We place high emphasis on testing the behaviour of the components in isolation.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

Integration tests between components and cloud services are in part done via mocking and in part in the cloud.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **Tests should feel like a help**

One benefit from doing above is that we can have fast development—test cycles due to most of the tests being runnable locally.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

By having this "test button" to press, it feels safer to experiment with the code.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

#### **No particular focus on code coverage**

We strive to test what is important with regard to the software architecture and the behavioural requirements.

<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />

---
layout: post
title: [Notes] Google Guice 
date: 2020-12-20
categories: Learning
---

Tagging a constuctor with @Inject is essentially telling Guice where you want a dependency to be provided for you. 
You can also apply it for methods and fields. 
Which one you choose solely depends on class's requirements.

Constuctor injection provides immutability. That also means thread safety. Only one @Inejct is allowed in this case. 

Guice doens't care of targets visiblity. It will inject anything tagged with @Inject regardless of whether is public, private or protected. 
However, injection into private members is usually not needed and probably a bad idea. This leads to crippling the classes testability.
Field injections are frequent offenders in this case since they are usually private members in a class. 


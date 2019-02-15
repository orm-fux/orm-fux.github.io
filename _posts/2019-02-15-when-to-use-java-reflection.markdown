---
layout: post
title:  When to use the Reflection API in Java
date:   2019-02-15 14:15:00
tags: java reflection thoughts
mathjax: false
---

Many years back, when I first came in contact with the Spring Framework, I was asking myself: "How the heck is dependency injection working?" That was also the point when I discovered the wonders of the Reflection API in Java. Ohhh, the wonderful dangerous things you can do with that! Access all the hidden stuff. 

But reflection is hecking dangerous! It allows you to access the internals of code that other developers have explicitly hidden from you. Want to read a private field? You can do that! Want to call a private method? You can do that! Want to change `final` values? Well, go ahead! Want to change the meaning of `false` to `true`? [You can do that, too](https://stackoverflow.com/a/3301720)!

But when is it actually OK to use reflection?

**Reflection is a "no no" when...**

*Forget about reflection in your day-to-day work.* Simply treat it as non-existing. Use dependencies as they are defined. Don't access their hidden internals. They may change. And those changes can easily break your code.

For me another rule of thumb, when not to use reflection, is: Whenever a name would need to be hard coded, reflection is not the way to go. Java is a statically typed language. IDEs help you finding usages of classes, fields, and methods. Once you hard code a name, you'll be introducing a future bug. Don't! Reflection will result in runtime errors. You should always, *always*, prefer compile time errors!

If you need some access to stuff you have coded yourself and think about reflection: Do yourself a favour and think about your code design instead. How could you redesign your code, so that you can access the information you need? It's yours after all.

**Reflection is allowed when...**

So, up until now it sound like you should never use reflection, right? Well, no! I consider reflection to be allowed when you are writing a library, which actually needs information about the class structure. 

A prime example would be the evaluation of annotations. That's also how dependency injection often works: In your library you define your annotations like `@Bean` and `@Autowired`. Now your library has to look for classes, properties, and methods with those annotations, and - based on their presence - do stuff, like instantiating objects and setting property values. But in those cases your library provides the annotations *and* the annotation handlers. No name is hard coded. 

Also, and this is a controversial point, it is in *very rare cases* OK to use reflection when you have to do something based on type, which would otherwise lead to unnecessary code. The particular example that comes to mind is when you a some base class that needs to create an instance of a particular type defined by a sub class. Something like this would be quite bothersome:

```java
public abstract class Base<T extends BaseEntity> {
    
    public void initNewValue() {
        final T value = doInitEntity();
        //do stuff with "value"
    }
    
    protected abstract T doInitEntity();
}

public class SubClass extends Base<SomeEntity> {
    
    protected SomeEntity doInitEntity() {
        return new SomeEntity();
    }
}
```

Each sub-class would need a simple method, which calls a simple constructor only. With reflection you can call the constructor in the base class. End of story. The danger is, of course again, that you are not save from changes. Just think of what would happen, when there is suddenly no no-argument constructor anymore. It would still compile. But a compile error would be a few thousand times better than a runtime error!

**tl;dr:** Don't use it in your day-to-day work! If at all, use it in libraries, which depend on class information.
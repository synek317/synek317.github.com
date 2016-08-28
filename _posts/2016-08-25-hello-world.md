---
layout: post
title: Taming the global devil with c#"
description: ""
category: 
tags: []
---
{% include JB/setup %}

### TL;DR
Making some assumptions I've created something that one could call fully testable global virtual functions that let me not care about any dependencies. All of that at no costs at runtime and with almost no effort at develop time. Fuck yeah.

## The roots

How to KISS when developing application with complex domain? OOP is the ultimate answer since ages. It is so obvious, that some languages - like c#, java and many others - forces us to create object-oriented code. To deal with the mess that we used to create again and again, we use different design patterns that helps us to organize the code well. 'Mature' solutions contain well designed classes following SOLID rules with D = dependency injection being one of the most important part of it.

What are the benefits? The biggest one is probably the possibility to unit test. The most naive - and my favorite - is just passing all dependencies in the constructor:
```csharp
class Reader
{
  private readonly MealConverter mealConverter;
  private readonly DateParser    dateParser;
  // ...

  public Reader(MealConverter mealConverter, DateParser dateParser /*, ... */)
  {
    this.mealConverter = mealConverter;
    this.dateParser    = dateParser;
    //...
  }
}
```

This is extremely simple to test:
```csharp
class ReaderTests
{
  private MealConverter mealConverter;
  private DateParser    dateParser;
  //...

  private Reader target;

  [SetUp]
  public void Before_Each_Test()
  {
    mealConverter = Substitute.For<MealConverter>();
    dateParser    = Subtitute.For<DateParser>();
    //...

    target = new Reader(
      mealConverter: mealConverter,
      dateParser:    dateParser
      //...
    );
  }
}
```

I have created TONS of classes like that. In my teams, we have tried other dependency injection methods, but none of them was as simple as the one above. The great advantage of it is almost no overhead: we only had to pass and remember some references, no additional configuration, no xmls, no reflection. So far so good, but this is LARGE amount of code that one has to write just in order to test his/her class.

## The problem
1. When adding/removing new dependency, I have to modify a lot of places:
  - the class private fields,
  - its constructor parameters,
  - its constructor implementation
  - unit test private fields,
  - unit test `SetUp` method
  - every other place the class is created (ideally it is limited to one method)
2. Moving dependencies around (e.g. temporarily adding logger, decide to parse date in other place) multiplies the number of changes from (1) two times.
3. All that bloat is schematic to the limits.
4. Because it is boring and requires some kind of 'brain context switch', it leads to badly organized code (_I know these should happen in other place but I'm too lazy to move it_)

## The assumptions

Now an important part. I have analyzed the code I was writing for 3 years and I discovered some interesting facts. Caution: this may not be true for your case. These are fundamental for the solution below. If you think that your world is completely different, you can probably stop reading here.

1. A LOT of classes have (or can have) only 2 instances: 1 in production code and 1 in test class.
2. Most of classes are stateless - this is my personal ultimate goal, stateless is good.
3. Almost all constructor parameters are dependencies (collaborators)
4. Inheritance is not heavily used in business logic. When it is, in most cases it may be replaced with composition.

1 + 2 straightly leads to the tought:

> God, if not those !$#%#$%@ unit tests, I could use lovely loose functions instead of methods. Or, at least, static classes because c# forbids loose code.


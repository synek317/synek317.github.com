---
layout: post
title:  "Global variables and Unity"
date:   2020-01-24 21:17:00 +0100
categories: unity
tags: [unity,gamedev,c#]
excerpt_separator:  <!--more-->
---

State is the root of all evil. Global state is even worse. However, if something can simplify your life, then used with caution might be a huge time-saver. In this post, I will show my approach to global data storage in a Unity project. It allows you to store and edit in the Editor all kind of serializable data including references to various Unity objects such as `Sprite`s or `GameObject`s.

<!--more-->

## Warning
First of all, I don't recommend using this to store any kind of mutable state. I haven't tested it, and my intuition and experience say it would lead to very complex issues. The main purpose of this solution is to have one place that stores read-only static info.

## Why would I use it?
Suppose you want to use several const magic numbers - some delay, spacing or treshold. One way is to create a static class with public fields. The downside is that whenever you want to change any value, you have to change it in the code. Following my solution, you will be able to edit the values directly in the Editor.

Another reason might be that you want to store some references to other Unity objects, e.g. `Sprite`s. Normally, you need to add a public property to your component and then assign it some object in the Editor. During the rapid develpment stage it can be annoying if you frequently add and remove objects and components. This can also become an issue if the same information is used by a lot of objects. Imagine having to assign the same 10 references to 20 different objects.

## Why wouldn't I use it?
Because it introduces a global state without any protection against mutations. Depending on the use, it may as well simplify your project as make it more complex.

## Solution

First, add the following `ScriptableObject` to your Assets:

```c#
using UnityEditor;
using UnityEngine;

[CreateAssetMenu(menuName = "Globals")]
public class Globals : ScriptableObject
{
    public static Globals instance { get; private set; }

    [SerializeField]
    public Sprite SomeGlobalSprite;

    /* Add more fields here */

    [InitializeOnLoadMethod]
    static void OnRuntimeMethodLoad()
    {
        instance = Resources.Load<Globals>("Globals");

        if (instance == null)
        {
            throw new System.Exception("Could not load Globals Resource!");
        }

        Debug.Log("Globals initialized!");
    }
}
```

Then, create an instance of it in the `Resources` folder and name it `Globals` (if you choose different name, remember to adjust the code).

And that's all! To set values, select the `Resources/Globals` asset in the Editor and assign whatever you need in the Inspector.

If you are as lazy as me and don't want to type this `Globals.instance.` every time you access one of the global values, you can add a helper class to shorten your code:

```c#
public class MyMonoBehaviour : MonoBehaviour
{
    public static Globals G => Globals.instance;
}

```

and then derive from `MyMonoBehavior` instead of `MonoBehavior` in each component that uses `Globals`.
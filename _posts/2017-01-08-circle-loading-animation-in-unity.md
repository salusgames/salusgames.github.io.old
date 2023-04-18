---
layout: post
title: Circle Loading Animation In Unity
subtitle: Quick and easy
tags: [unity]
comments: true
---

This short guide will show you how to make the circle animation below. It's actually quite simple.

![alt text](../assets/img/circle-loading-animation.gif "Terminal Example")

First you will need to create an image to use for your animation. If you're to lazy you can just use the one I made.  Save it into the Assets folder of your Unity project.

[Download Image](../assets/img/loadingCircle.png "Download Image")

We are going to make the background image first. Create a new UI Image object (Gameobject > UI > Image) call it "Loading Circle". On the Image Component set the Source Image the one you created or downloaded and give it a colour you fancy.

Once you have done that you will need to duplicate it (Right Click > Duplicate). Rename the duplicated gameobject to "Progress", make it a child of the "Loading Circle" gameobject and change its anchor points to stretch on both the X & Y axis. Again change it's colour to something you want.

On the "Progress" gameobject change the Image Type on the Image Component to "Filled", set the Fill Amount as 0.1, and ensure Fill Method is "Radial 360".

All you need to do now is assign the script below to the "Progress" gameobject and you should have a working animation.

### The Code

```C#
using UnityEngine;

public class LoadingCircle : MonoBehaviour
{
    private RectTransform rectComponent;
    private float rotateSpeed = 200f;

    private void Start()
    {
        rectComponent = GetComponent<RectTransform>();
    }

    private void Update()
    {
        rectComponent.Rotate(0f, 0f, rotateSpeed * Time.deltaTime);
    }
}
```
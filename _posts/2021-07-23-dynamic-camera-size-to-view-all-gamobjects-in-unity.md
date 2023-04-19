---
layout: post
title: Dynamic Camera Size To View All GameObjects In Unity
tags: [unity, tutorial]
comments: true
---

While developing [Hexa](https://store.steampowered.com/app/981620/Hexa/) for the Steam store I always thought that it would work well as a mobile game. It was simple in controls and could easily be picked and played for a few minutes at a time.

I started to convert the game to work on mobile devices and quickly came upon a small problem. The levels were too big to show all the Hex's on the screen at once. The player would load a level and have no idea how far the level would extend past the screen they can currently see so wouldn't know to pan the camera around. This wasn't a problem on the PC as generally with the screen sizes an entire level would be viewable.

## The Solution

The solution should be a simple one. Start the level with the camera orthographic size zoomed out so the entire level is viewable and zoom it in. Works great in theory but when used it looks odd when the level could of easily fit on the screen without an zooming needed.

I ended up just quickly coding this little function that just finds all the GameObjects in the scene, gets their position in Unity ScreenSpace and keeps changing the camera's orthographic size until they are all visible. That way no matter how big or small a level is, the player will always get to see the Hex's in a level.

## The Code

This is no way optimal and I wouldn't recommend using it in a scene with a load of GameObjects.

```cs
private void SetMaxOrthographicSize()
{
var highestX = 0f;
var lowestX = 0f;
var highestY = 0f;
var lowestY = 0f;
var zoomBuffer = 2f;

    // Loop through all GameObjects and find the highest & lowest x and y coordinate. In my case every Hex in Hexa must have a 
    // Rotate script attached so I use this to find all the Hex's in the scene.
    foreach (var hex in GameObject.FindObjectsOfType<Rotate>())
    {
        var hexPosition = hex.transform.position;

        if (hexPosition.x > highestX) highestX = hexPosition.x;
        if (hexPosition.x < lowestX) lowestX = hexPosition.x;
        if (hexPosition.y > highestY) highestY = hexPosition.y;
        if (hexPosition.y < lowestY) lowestY = hexPosition.y;
    }

    var highestXViewportPoint = Camera.main.WorldToViewportPoint(new Vector3(highestX + zoomBuffer, 0, 0)).x;
    var lowestXViewportPoint = Camera.main.WorldToViewportPoint(new Vector3(lowestX - zoomBuffer, 0, 0)).x;
    var highestYViewportPoint = Camera.main.WorldToViewportPoint(new Vector3(0, highestY + zoomBuffer, 0)).y;
    var lowestYViewportPoint = Camera.main.WorldToViewportPoint(new Vector3(0, lowestY - zoomBuffer, 0)).y;

    // While the highest & lowest x and y viewport point is not between 0 and 1, increase orthographicSize
    while (highestXViewportPoint > 1 || lowestXViewportPoint < 0 || highestYViewportPoint > 1 || lowestYViewportPoint < 0)
    {
        Camera.main.orthographicSize += 1;
    }
}

```

---
layout: post
title: Microsoft Visual Studio in Linux with Unity
tags: [unity, tutorial, linux]
comments: true
---

I like using Linux for everyday use as well as for developing. The Unity Linux Experimental build works great and up until now I found Visual Studio Code worked for the smaller projects I created. However the bigger project I am working on now needs to use the full use of Microsoft Visual Studio and it's extenstions.

This is a little step by step on how I got Microsoft Visual Studio to work on Linux using Virtual Box and the Unity3D Linux build.

1. First things first you need to install Virtual Box
    * [Arch Linux](https://wiki.archlinux.org/index.php/VirtualBox)
    * [Ubuntu](https://help.ubuntu.com/community/VirtualBox)

2. Once you have install VirtualBox you need to create a new Windows virtual machine.
   [https://docs.oracle.com/cd/E26217_01/E26796/html/qs-create-vm.html](https://docs.oracle.com/cd/E26217_01/E26796/html/qs-create-vm.html)

3. Once you have added the virtual machine you will need to change a few settings in it.
    1. In the **System** tab and up it's **Base Memory** to about 2GB.
    2. In the **Display** tab go to **Screen** and tick **Enable 3D Acceleration**. Then put the **Video Memory** to the highest (Probably 256MB)
    3. You will need to add a shared folder so you can access your Unity project from within the virtual Machine. Go the the **Shared Folders** tab and add the folder that contains your project.

4. Boot into your virtual machine and download the a copy of Microsoft Visual Studio as well as a version of Unity that matches the version you have on your main Linux computer.

5. Load up Unity in your Windows virtual machine and open your Unity project from the shared folder you added (It's probably on the desktop). Click continue on the warning about a version mismatch.

6. When Unity loads up click **Assets > Open C# Project**, Microsoft Visual Studio should now start up. If you get security warning about a untrusted source just click **OK**. That's only showing because of the way the shared folders work.

7. If all is good you should now be able to close Unity in your Windows virtual machine. If your solution fails to load with the error **The Project requires user input** then you need to right click the project in the Solution Explorer and select **Reload Project** as explained [here](https://community.microfocus.com/microfocus/cobol/visual_cobol/w/knowledge_base/27584/the-project-requires-user-input-reload-the-project-for-more-information).

There are quite a few downsides from doing this though:
* You can't link up Visual Studio and Unity for degugging
* You can't open a file in Visual Studio from Unity
* You have to create new scripts from within Visual Studio and not Unity. If you create them in Unity and reload the solution it loses references and you will need to do step 5 & 6 again.

Even with the downsides I think its worth it for the extensions and flexibility you get with Visual Studio. When you get accustomed to the workflow of switching between your VM and native Linux machine it isn't actually any slower. When I need to debug I just use Visual Studio Code for everything else I use the full version on Visual Studio in the Windows VM.

I hope this helps people that want to use Visual Studio on their Linux computers without having to dual boot into Windows.
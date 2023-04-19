---
layout: post
title: Simple Steam Workshop Implementation In Unity
tags: [unity, tutorial, steam]
comments: true
---

While working on my new game [Hexa](https://store.steampowered.com/app/981620/Hexa/) I came to the point where I needed to implement the Steam Workshop. I started to go about it the same way I did with [Layers](http://store.steampowered.com/app/661330/Layers/) by googling code and hacking at it until it worked and fit my need. I shouldn't need to explain why this is a bad idea.

In the end I thought I would bite the bullet and get my head around how the Steam API worked and create a simple class that I can reuse in my games.

This uses the Steamworks.NET package which you will need to add to your Unity project.

## Adding the Steamworks package

1. Go to https://github.com/rlabrecque/Steamworks.NET/releases and download the latest release,  make sure you download the .unitypackage. You will then need to import this into your Unity project.

2. In the root of your project folder open the steam_appid.txt file and replace the 480 with your Steam AppId. Relaunch Unity.

3. On your initial scene, create a new game object, call it something like "Steam Manager" and add the SteamManager.cs script found in Scripts>Steamworks.NET

That's all you need to integrate Steamworks into your project. Just make sure the Steam client is running in the background you should be able to hit play in the editor and have no errors.

## The Class

I'm sure there are far better ways out there to upload content to the Steam Workshop but this class works for me. Nice and simple.

```cs
using UnityEngine;
using Steamworks;
using System.Collections.Generic;

internal struct SteamWorkshopItem
{
public string ContentFolderPath;
public string Description;
public string PreviewImagePath;
public string[] Tags;
public string Title;
}

public class SteamWorkshop : MonoBehaviour
{
private SteamWorkshopItem currentSteamWorkshopItem;
private PublishedFileId_t publishedFileID;
public static SteamWorkshop Instance { get; private set; }

    public List<string> GetListOfSubscribedItemsPaths()
    {
        var subscribedCount = SteamUGC.GetNumSubscribedItems();
        PublishedFileId_t[] subscribedFiles = new PublishedFileId_t[subscribedCount];
        SteamUGC.GetSubscribedItems(subscribedFiles, (uint)subscribedFiles.Length);

        ulong sizeOnDisk = 0;
        string installLocation = string.Empty;
        uint timeStamp = 0;

        var result = new List<string>();

        foreach (var file in subscribedFiles)
        {
            SteamUGC.GetItemInstallInfo(file, out sizeOnDisk, out installLocation, 1024, out timeStamp);
            result.Add(installLocation);
        }

        return result;
    }

    public void UploadContent(string itemTitle, string itemDescription, string contentFolderPath, string[] tags, string previewImagePath)
    {
        currentSteamWorkshopItem = new SteamWorkshopItem
        {
            Title = itemTitle,
            Description = itemDescription,
            ContentFolderPath = contentFolderPath,
            Tags = tags,
            PreviewImagePath = previewImagePath
        };

        CreateItem();
    }

    private void Awake()
    {
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);
        }
        else
        {
            Destroy(gameObject);
        }
    }

    private void CreateItem()
    {
        var steamAPICall = SteamUGC.CreateItem(SteamUtils.GetAppID(), EWorkshopFileType.k_EWorkshopFileTypeCommunity);
        var steamAPICallResult = CallResult<CreateItemResult_t>.Create();
        steamAPICallResult.Set(steamAPICall, CreateItemResult);
    }

    private void CreateItemResult(CreateItemResult_t param, bool bIOFailure)
    {
        if (param.m_eResult == EResult.k_EResultOK)
        {
            publishedFileID = param.m_nPublishedFileId;
            UpdateItem();
        }
        else
        {
            Debug.Log("Couldn't create a new item");
        }
    }

    private void UpdateItem()
    {
        var updateHandle = SteamUGC.StartItemUpdate(SteamUtils.GetAppID(), publishedFileID);

        SteamUGC.SetItemTitle(updateHandle, currentSteamWorkshopItem.Title);
        SteamUGC.SetItemDescription(updateHandle, currentSteamWorkshopItem.Description);
        SteamUGC.SetItemContent(updateHandle, currentSteamWorkshopItem.ContentFolderPath);
        SteamUGC.SetItemTags(updateHandle, currentSteamWorkshopItem.Tags);
        SteamUGC.SetItemPreview(updateHandle, currentSteamWorkshopItem.PreviewImagePath);
        SteamUGC.SetItemVisibility(updateHandle, ERemoteStoragePublishedFileVisibility.k_ERemoteStoragePublishedFileVisibilityPublic);

        var steamAPICall = SteamUGC.SubmitItemUpdate(updateHandle, "");
        var steamAPICallResult = CallResult<SubmitItemUpdateResult_t>.Create();
        steamAPICallResult.Set(steamAPICall, UpdateItemResult);
    }

    private void UpdateItemResult(SubmitItemUpdateResult_t param, bool bIOFailure)
    {
        if (param.m_eResult == EResult.k_EResultOK)
        {
            Debug.Log("Sucessfully submitted item to Steam");
        }
        else
        {
            Debug.Log("Couldn't submit the item to Steam");
        }
    }
}
```
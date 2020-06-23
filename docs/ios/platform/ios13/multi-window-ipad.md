---
title: Xamarin 中的多個 Windows for iPad
description: 在 iPad 應用程式中新增多個視窗支援。
ms.prod: xamarin
ms.assetid: 524b6f2e-dbdf-11e9-8a34-2a2ae2dbcce4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/20/2019
ms.openlocfilehash: ce7df59d41efdd2d151fd2ea73cf26b40ee7fa10
ms.sourcegitcommit: 834466c9d9cf35e9659e467ce0123e5f5ade6138
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129905"
---
# <a name="multiple-windows-for-ipad"></a>適用於 iPad 的多視窗

iOS 13 現在支援 iPad 上相同應用程式的並存視窗。 這會啟用 windows 之間的新體驗和拖放互動。 本檔說明如何設定您的應用程式以支援這項功能，並引進這些新功能。 

## <a name="project-configuration"></a>專案設定

若要為多個視窗設定專案，請修改 `info.plist` 以宣告 `NSUserActivityTypes` 通知 iOS 您的應用程式將會處理此類型的活動，並為 `UIApplicationSceneManifest` `UIApplicationSupportsMultipleScenes` 多個視窗啟用，並 `UISceneConfigurations` 將您的場景與分鏡腳本建立關聯。

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>com.xamarin.Gallery.openDetail</string>
</array>
<key>UIApplicationSceneManifest</key>
<dict>
    <key>UIApplicationSupportsMultipleScenes</key>
    <true/>
    <key>UISceneConfigurations</key>
    <dict>
        <key>UIWindowSceneSessionRoleApplication</key>
        <array>
            <dict>
                <key>UISceneConfigurationName</key>
                <string>Default Configuration</string>
                <key>UISceneDelegateClassName</key>
                <string>SceneDelegate</string>
                <key>UISceneStoryboardFile</key>
                <string>Main</string>
            </dict>
        </array>
    </dict>
</dict>
```

## <a name="opening-multiple-windows"></a>開啟多個視窗

當您的應用程式在 iPad 上開啟並執行時，有幾種方式可以開啟該應用程式的多個 windows，iOS 會將其視為預設值。

- **應用程式公開**-在您的應用程式開啟時，請將 dock 中的應用程式圖示點按以進入此模式。
- **滑動**-將應用程式圖示從停駐移到執行中應用程式的頂端，使其具有浮動視窗。
- **分割畫面**-將應用程式圖示從 dock 拖曳至 iPad 螢幕的邊緣，以建立新的並排視窗。

您可以從應用程式內取得其他進入多視窗模式的互動。

**從應用程式拖曳**-在您的應用程式中使用拖曳互動來啟動新的， `NSUserActivity` 就像在先前的範例中拖曳應用程式圖示一樣。

使用[拖放互動][0]時，您會建立， `NSUserActivity` 並將要傳遞的資料與您要求 iOS 開啟的新視窗產生關聯。

```csharp
public UIDragItem [] GetItemsForBeginningDragSession (UICollectionView collectionView, IUIDragSession session, NSIndexPath indexPath)
{
    var selectedPhoto = GetPhoto (indexPath);

    var userActivity = selectedPhoto.OpenDetailUserActivity ();
    var itemProvider = new NSItemProvider (UIImage.FromFile (selectedPhoto.Name));
    itemProvider.RegisterObject (userActivity, NSItemProviderRepresentationVisibility.All);

    var dragItem = new UIDragItem (itemProvider) {
        LocalObject = selectedPhoto
    };

    return new [] { dragItem };
}
```

在上述程式碼中， `selectedPhoto` 模型物件有方法可傳回呼叫的 `NSUserActivity` `OpenDetailUserActivity()` 。 當拖曳手勢完成時，具有的會透過 `UIDragItem` 呈現 `userActivity` `NSItemProvider` 。

**明確動作**-按鈕或連結上的使用者手勢提供開啟新視窗的功能。

在中， `UIApplication` 您可以藉由呼叫來啟動新的 `UISceneSession` `RequestSceneSessionActivation` 。 如果現有的場景已經存在，您應該使用它。 根據預設，系統會為您建立新的場景。

```csharp
public void ItemSelected(UICollectionView collectionView, NSIndexPath indexPath)
{
    var userActivity = selectedPhoto.OpenDetailUserActivity ();

    UIApplication.SharedApplication.RequestSceneSessionActivation(
        sceneSession: null,
        userActivity: userActivity,
        options: null,
        errorHandler: null
    );
}
```

在此範例中， `userActivity` 是唯一傳遞至方法的值，以便 `RequestSceneSessionActivation` 根據明確的使用者動作開啟應用程式的新視窗，在此案例中為的 `ItemSelected` 處理常式 `UICollectionView` 。

## <a name="related-links"></a>相關連結

- [在 Xamarin 中拖放][0]

[0]: ~/ios/platform/introduction-to-ios11/drag-and-drop.md

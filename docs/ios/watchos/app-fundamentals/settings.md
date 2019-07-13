---
title: 使用 watchOS 在 Xamarin 中的設定
description: 本文件說明如何使用 Xamarin 在 watchOS 設定。 它會討論在 iPhone 上使用應用程式，以及 Apple Watch 應用程式中的這些設定監看式應用程式方案，新增的設定。
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a8fe2c2765676db52c23fd7c475f218f14697caf
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675235"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>使用 watchOS 在 Xamarin 中的設定

Apple Watch 應用程式可以使用相同的設定功能，為 iOS 應用程式-[設定] 使用者介面會顯示在**Apple Watch** iPhone 應用程式，但值的可存取您的 iPhone 應用程式和中也監看式延伸模組。

![](settings-images/intro.png "Apple Watch 應用程式可以使用相同的設定功能，為 iOS 應用程式")

設定會儲存在可存取的 iOS 應用程式和監看式應用程式擴充功能所定義的共用的檔案位置**應用程式群組**。 您應該[設定應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md)之前新增的設定，使用下列指示。

## <a name="add-settings-in-a-watch-solution"></a>在 監看式解決方案中新增設定

在  **iPhone 應用程式**方案中 (*不*watch 應用程式或擴充功能):

1. 以滑鼠右鍵按一下**新增 > 新增檔案...** ，然後選擇  **Settings.bundle** (您無法編輯中的名稱**新檔案**對話方塊):

   [![](settings-images/settings-add-sml.png "加入新的設定套件組合")](settings-images/settings-add.png#lightbox)

2. 將名稱變更為**設定 Watch.bundle** (選取，然後輸入**Command + R**重新命名):

   ![](settings-images/settings-rename.png "重新命名套件組合")

3. 加入新的金鑰`ApplicationGroupContainerIdentifier`要**Root.plist**值設為您設定之後，（例如應用程式群組。 `group.com.xamarin.WatchSettings` 在範例中）：

   [![](settings-images/settings-appgroup-sml.png "新增 Root.plist ApplicationGroupContainerIdentifier 機")](settings-images/settings-appgroup.png#lightbox)

4. 編輯**Settings-Watch.bundle/Root.plist**範本檔案來包含您想要使用的選項包含一個群組。
  文字欄位、 切換開關和滑桿的預設值 （您可以刪除並取代為您自己的設定）：

  [![](settings-images/rootplist-sml.png "編輯 Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>使用監看式應用程式中設定

若要存取使用者所選取的值，建立`NSUserDefaults`執行個體使用的應用程式群組，並指定`NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Apple Watch 應用程式

[![](settings-images/settings-app-sml.png "在 iPhone 上新的 Apple Watch 應用程式")](settings-images/settings-app.png#lightbox)

使用者會透過新的設定與互動**Apple Watch**他們的 iPhone 上的應用程式。 此應用程式可讓使用者顯示/隱藏上監看式，以及設定可讓您公開使用的編輯應用程式**設定 Watch.bundle**。

![](settings-images/applewatch-1.png "應用程式設定的範例") ![](settings-images/applewatch-2.png "應用程式設定的範例")



## <a name="related-links"></a>相關連結

- [Apple 的設定文件](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)

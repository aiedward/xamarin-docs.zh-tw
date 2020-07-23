---
title: 使用 Xamarin 中的 watchOS 設定
description: 本檔說明如何使用 Xamarin 中的 watchOS 設定。 其中討論如何將設定新增至 watch 應用程式解決方案、使用應用程式中的設定，以及 iPhone 上的 Apple Watch 應用程式。
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: c5ad40c375320ed21acb3f0a75c5c66f2efc7824
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938627"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>使用 Xamarin 中的 watchOS 設定

Apple Watch 應用程式可以使用與 iOS 應用程式相同的設定功能-[設定] 使用者介面會顯示在**Apple Watch** iphone 應用程式中，但這些值可同時在您的 iphone 應用程式和監看式延伸模組中存取。

![Apple Watch 應用程式可以使用與 iOS 應用程式相同的設定功能](settings-images/intro.png)

這些設定會儲存在可供 iOS 應用程式和監看式應用程式延伸模組（由**應用程式群組**所定義）存取的共用檔案位置。 您應該使用下列指示，在新增設定之前[設定應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md)。

## <a name="add-settings-in-a-watch-solution"></a>在監看式解決方案中新增設定

在解決方案的**iPhone 應用程式**中（*不*是監看式應用程式或擴充功能）：

1. 以滑鼠右鍵按一下 [**加入 > 新增檔案 ...** ]，然後選擇 [**設定**] [配套] （您無法在 [**新增**檔案] 對話方塊中編輯名稱）：

   [![加入新的設定配套](settings-images/settings-add-sml.png)](settings-images/settings-add.png#lightbox)

2. 將名稱變更為 **[設定-監看包]** （選取並輸入**Command + R**以重新命名）：

   ![重新命名配套](settings-images/settings-rename.png)

3. 將新的金鑰新增 `ApplicationGroupContainerIdentifier` 至**plist** ，並將值設為您已設定的應用程式群組（例如， `group.com.xamarin.WatchSettings`在範例中）：

   [![將 ApplicationGroupContainerIdentifier 索引鍵新增至根目錄。 plist](settings-images/settings-appgroup-sml.png)](settings-images/settings-appgroup.png#lightbox)

4. 編輯 [ **Settings-Watch]/[plist** ] 以包含您想要使用的選項-範本檔案包含一個群組。
  [欄位]、[切換開關] 和 [滑杆] （您可以刪除並以您自己的設定取代）：

  [![編輯 Settings-Watch。 plist](settings-images/rootplist-sml.png)](settings-images/rootplist.png#lightbox)

## <a name="use-settings-in-the-watch-app"></a>使用監看式應用程式中的設定

若要存取使用者選取的值，請 `NSUserDefaults` 使用應用程式群組建立實例，並指定 `NSUserDefaultsType.SuiteName` ：

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Apple Watch 應用程式

[![IPhone 上的新 Apple Watch 應用程式](settings-images/settings-app-sml.png)](settings-images/settings-app.png#lightbox)

使用者會透過其 iPhone 上的新**Apple Watch**應用程式與設定進行互動。 此應用程式可讓使用者顯示/隱藏監看式上的應用程式，也可以編輯使用**設定-watch**所公開的設定。

![應用程式設定的範例](settings-images/applewatch-1.png) ![應用程式設定的範例](settings-images/applewatch-2.png)

## <a name="related-links"></a>相關連結

- [Apple 的設定檔](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)

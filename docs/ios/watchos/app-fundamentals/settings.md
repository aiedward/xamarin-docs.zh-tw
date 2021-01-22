---
title: 在 Xamarin 中使用 watchOS 設定
description: 本檔說明如何使用 Xamarin 中的 watchOS 設定。 它討論將設定新增至 watch 應用程式解決方案、使用應用程式中的這些設定，以及 iPhone 上的 Apple Watch 應用程式。
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 7a3839cb7c747879f1549fb8f6715fded62d45bb
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697718"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>在 Xamarin 中使用 watchOS 設定

Apple Watch apps 可以使用與 iOS 應用程式相同的設定功能-[設定] 使用者介面會顯示在 **Apple Watch** iphone 應用程式中，但這些值可同時在您的 iphone 應用程式和 Watch 延伸模組中存取。

![Apple Watch apps 可以使用與 iOS 應用程式相同的設定功能](settings-images/intro.png)

這些設定會儲存在可供 iOS 應用程式和 watch 應用程式延伸模組（由 **應用程式群組** 定義）存取的共用檔案位置。 您應該先使用下列指示 [設定應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md) ，然後再新增設定。

## <a name="add-settings-in-a-watch-solution"></a>在監看式解決方案中新增設定

在解決方案的 **iPhone 應用程式** 中 (*不* 是 watch 應用程式或延伸模組) ：

1. 以滑鼠右鍵按一下 [ **新增] > [新增** 檔案]，然後選擇 [ **設定** ]， (您無法在 [ **新增** 檔案] 對話方塊中編輯名稱) ：

   [![新增設定套件組合](settings-images/settings-add-sml.png)](settings-images/settings-add.png#lightbox)

2. 將名稱變更為 [ **設定]-Watch** (選取並輸入 **Command + R** 以重新命名) ：

   ![重新命名套件組合](settings-images/settings-rename.png)

3. 將新的金鑰新增 `ApplicationGroupContainerIdentifier` 至 **plist** ，並將值設定為您已設定的應用程式群組， (例如 `group.com.xamarin.WatchSettings` 在範例) ：

   [![將 ApplicationGroupContainerIdentifier 索引鍵新增至 plist。](settings-images/settings-appgroup-sml.png)](settings-images/settings-appgroup.png#lightbox)

4. 編輯 **Settings-Watch 中的 plist** ，以包含您想要使用的選項-範本檔案包含群組。
  [預設值]、[切換開關] 和 [滑杆] (您可以刪除並取代為您自己的設定) ：

  [![編輯 Settings-Watch，plist](settings-images/rootplist-sml.png)](settings-images/rootplist.png#lightbox)

## <a name="use-settings-in-the-watch-app"></a>使用 Watch 應用程式中的設定

若要存取使用者選取的值，請 `NSUserDefaults` 使用應用程式群組建立實例，並指定 `NSUserDefaultsType.SuiteName` ：

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Apple Watch 應用程式

[![IPhone 上新的 Apple Watch 應用程式](settings-images/settings-app-sml.png)](settings-images/settings-app.png#lightbox)

使用者會透過其 iPhone 上的新 **Apple Watch** 應用程式與設定互動。 此應用程式可讓使用者顯示/隱藏 watch 上的應用程式，也可以編輯使用 **設定-監看式** 所公開的設定。

![螢幕擷取畫面顯示應用程式中的 WatchKitSettings。](settings-images/applewatch-1.png) ![螢幕擷取畫面顯示應用程式中的 WatchTodo。](settings-images/applewatch-2.png)

## <a name="related-links"></a>相關連結

- [Apple 的設定檔](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)

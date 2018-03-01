---
title: "使用的設定"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 3ff8800f4e8690069f5394193d11552d917baffe
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-settings"></a>使用的設定

Apple Watch 應用程式可以使用相同的設定功能為 iOS 應用程式-設定使用者介面會顯示在**Apple Watch** iPhone 應用程式，但這些值會在 iPhone 應用程式和也 watch 擴充功能中存取。

![](settings-images/intro.png "Apple Watch 應用程式可以使用相同的設定功能為 iOS 應用程式")

設定會儲存在 iOS 應用程式和監看式應用程式擴充功能所定義能夠存取共用的檔案位置**應用程式群組**。 您應該[設定應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md)之前將使用下列指示的設定。

## <a name="add-settings-in-a-watch-solution"></a>加入監看式解決方案的設定

在**iPhone 應用程式**方案中 (*不*監看式應用程式或延伸模組):

1. 以滑鼠右鍵按一下**新增 > 新的檔案...**選擇**Settings.bundle** (您無法編輯中的名稱**新檔案**對話方塊):

   [ ![](settings-images/settings-add-sml.png "加入新的設定組合")](settings-images/settings-add.png)

2. 將名稱變更為**設定 Watch.bundle** (選取，然後輸入**命令 + R**重新命名):

   ![](settings-images/settings-rename.png "重新命名組合")

3. 加入新機碼`ApplicationGroupContainerIdentifier`至**Root.plist**值設為您設定，（例如應用程式群組 `group.com.xamarin.WatchSettings` 在範例中）：

   [ ![](settings-images/settings-appgroup-sml.png "新增 Root.plist ApplicationGroupContainerIdentifier 機碼")](settings-images/settings-appgroup.png)

4. 編輯**Settings-Watch.bundle/Root.plist**範本檔案包含您想要使用的選項包含群組。
  textfield、 切換開關和滑桿的預設值 （您可以刪除和取代您自己的設定）：

  [ ![](settings-images/rootplist-sml.png "編輯 Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png)


## <a name="use-settings-in-the-watch-app"></a>監看式應用程式中使用的設定

若要存取使用者所選取的值，建立`NSUserDefaults`執行個體使用的應用程式群組並指定`NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Apple Watch 應用程式

[ ![](settings-images/settings-app-sml.png "在 iPhone 上新的 Apple Watch 應用程式")](settings-images/settings-app.png)

使用者透過新的設定與互動**Apple Watch**其 iPhone 上的應用程式。 此應用程式可讓使用者顯示/隱藏應用程式在監看式，以及設定公開使用的編輯**設定 Watch.bundle**。

![](settings-images/applewatch-1.png "應用程式設定的範例") ![ ](settings-images/applewatch-2.png "應用程式設定的範例")



## <a name="related-links"></a>相關連結

- [Apple 的設定文件](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)

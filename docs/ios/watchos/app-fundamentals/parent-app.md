---
title: 在 Xamarin 中使用 watchOS 父應用程式
description: 本檔說明如何在 Xamarin 中使用 watchOS 的父系應用程式。 其中討論 watchOS 應用程式延伸模組、iOS 應用程式、共用儲存體等。
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 3e11b163d16be9711bf09102e3ab8604d98299d7
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304958"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>在 Xamarin 中使用 watchOS 父應用程式

監看式應用程式和其配套的 iOS 應用程式之間有不同的通訊方式：

- 監看應用程式可以在 iPhone 上的父應用程式上[執行程式碼](#run-code)。

- 監看式擴充功能可以與父 iPhone 應用程式[共用儲存位置](#shared-storage)。

- 使用 [遞交] 將資料從通知傳遞至監看式應用程式，並將使用者傳送至應用程式中的特定介面控制器。

父系應用程式有時也稱為容器應用程式。

## <a name="run-code"></a>執行程式碼

這兩個範例示範如何使用 `WCSession` 來執行程式碼，並在監看式應用程式和配對的 iPhone 之間傳送訊息：

- [觀賞連線能力](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchconnectivity/)
- [SimpleWatchConnectivity](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-simplewatchconnectivity/) 

## <a name="shared-storage"></a>共用儲存

如果您設定[應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md)，則 iOS 8 延伸模組（包括監看式擴充功能）可以與父應用程式共用資料。

### <a name="nsuserdefaults"></a>使用 nsuserdefaults

下列程式碼可以同時以 watch 應用程式延伸模組和父 iPhone 應用程式撰寫，讓它們可以參考一組通用的 `NSUserDefaults`：

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files" />

### <a name="files"></a>檔案

IOS 應用程式和監看式延伸模組也可以使用通用檔案路徑來共用檔案。

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

注意：如果路徑是 `null` 則請檢查[應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md)設定，以確定布建設定檔已正確設定，並已下載/安裝在開發電腦上。

如需詳細資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)檔。

## <a name="related-links"></a>相關連結

- [Apple 的 WKInterfaceController 參考](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 與您的內含應用程式共用資料](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)

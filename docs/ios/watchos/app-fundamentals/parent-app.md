---
title: 在 Xamarin 中使用 watchOS 父應用程式
description: 本檔說明如何在 Xamarin 中使用 watchOS 父應用程式。 其中討論 watchOS 應用程式延伸模組、iOS 應用程式、共用儲存體等等。
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 002c57a1549201018cb2068f000a038f686eb2c0
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436738"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>在 Xamarin 中使用 watchOS 父應用程式

Watch 應用程式與它所配套的 iOS 應用程式之間有不同的通訊方式：

- Watch 應用程式可以在 iPhone 上的父應用程式上 [執行程式碼](#run-code) 。

- 監看延伸模組可以與上層 iPhone 應用程式 [共用儲存位置](#shared-storage) 。

- 使用「遞交」將資料從通知傳遞至 watch 應用程式，並將使用者傳送至應用程式中的特定介面控制器。

父應用程式有時也稱為容器應用程式。

## <a name="run-code"></a>執行程式碼

這兩個範例示範如何使用 `WCSession` 來執行程式碼，並在監看式應用程式和配對的 iPhone 之間傳送訊息：

- [觀賞連線能力](/samples/xamarin/ios-samples/watchos-watchconnectivity/)
- [SimpleWatchConnectivity](/samples/xamarin/ios-samples/watchos-simplewatchconnectivity/) 

## <a name="shared-storage"></a>共用儲存

如果您設定 [應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md) ，則 iOS 8 擴充功能 (包括監看延伸模組) 可與父應用程式共用資料。

### <a name="nsuserdefaults"></a>NSUserDefaults

下列程式碼可以同時寫入 watch 應用程式擴充功能和上層 iPhone 應用程式，讓它們可以參考一組通用的 `NSUserDefaults` ：

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

<a name="files"></a>

### <a name="files"></a>檔案儲存體

IOS 應用程式和監看延伸模組也可以使用通用檔案路徑共用檔案。

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

注意：如果路徑接著， `null` 請檢查 [應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md) 設定，確定已正確設定布建設定檔，並已下載/安裝在開發電腦上。

如需詳細資訊，請參閱 [應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) 檔。

## <a name="related-links"></a>相關連結

- [Apple 的 WKInterfaceController 參考](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 的共用資料與您的包含應用程式](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
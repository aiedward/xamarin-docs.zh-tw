---
title: 在 Xamarin 中使用 watchOS 父應用程式
description: 本檔說明如何在 Xamarin 中使用 watchOS 的父系應用程式。 其中討論 WatchKit 應用程式延伸模組、iOS 應用程式、共用儲存體等。
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 08637fe13b28565e7c6ab1c89b291c6db3b81025
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001481"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>在 Xamarin 中使用 watchOS 父應用程式

> [!IMPORTANT]
> 使用下列範例來存取父應用程式僅適用于 watchOS 1 監看式應用程式。

監看式應用程式和其配套的 iOS 應用程式之間有不同的通訊方式：

- 監看式擴充功能可以針對在 iPhone 上背景執行的父應用程式[呼叫方法](#code)。

- 監看式擴充功能可以與父 iPhone 應用程式[共用儲存位置](#storage)。

- 使用遞交將資料從概覽或通知傳遞至監看式應用程式，並將使用者傳送至應用程式中的特定介面控制器。

父系應用程式有時也稱為容器應用程式。

<a name="code" />

## <a name="run-code"></a>執行程式碼

在[GpsWatch 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gpswatch)中會示範監看式擴充功能與父 iPhone 應用程式之間的通訊。
您的監看式擴充功能可以要求父系 iOS 應用程式使用 `OpenParentApplication` 方法來代表其進行某些處理。

這特別適用于長時間執行的工作（包括網路要求）-只有父系 iOS 應用程式可以利用背景處理來完成這些工作，並將抓取的資料儲存在監看式延伸模組可存取的位置。

### <a name="watch-kit-app-extension"></a>監看套件應用程式延伸模組

呼叫 watch 應用程式延伸模組中的 `WKInterfaceController.OpenParentApplication`。 它會傳回 `bool`，指出是否已成功傳送方法要求。 檢查回應 `Action` 中的 `error` 參數，判斷在 iPhone 應用程式中執行的方法期間是否發生任何錯誤。

```csharp
WKInterfaceController.OpenParentApplication (new NSDictionary (), (replyInfo, error) => {
    if(error != null) {
        Console.WriteLine (error);
        return;
    }
    Console.WriteLine ("parent app responded");
    // do something with replyInfo[] dictionary
});
```

### <a name="ios-app"></a>iOS 應用程式

來自 watch 應用程式延伸模組的所有呼叫都會透過 iPhone 應用程式的 `HandleWatchKitExtensionRequest` 方法來路由傳送。
如果您在監看式應用程式中提出不同的要求，則此方法必須查詢 `userInfo` 字典，以決定如何處理要求。

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    // ... other AppDelegate methods
    public override void HandleWatchKitExtensionRequest
        (UIApplication application, NSDictionary userInfo, Action<NSDictionary> reply)
    {
        var status = 2;
        // do something in the background, and respond
        reply (new NSDictionary (
            "count", NSNumber.FromInt32 ((int)status),
            "value2", new NSString("some-info")
            ));
    }
}
```

<a name="storage" />

## <a name="shared-storage"></a>共用存放裝置

如果您設定[應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md)，則 iOS 8 延伸模組（包括監看式擴充功能）可以與父應用程式共用資料。

<a name="nsuserdefaults" />

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

## <a name="wormholesharp"></a>WormHoleSharp

適用于 watchOS 1 的熱門開放原始碼機制（以[MMWormHole](https://github.com/mutualmobile/MMWormhole)為基礎），可在父應用程式和監看式應用程式之間傳遞資料或命令。

您可以在 iOS 應用程式中使用類似下列的應用程式群組來設定「旋渦」，並監看延伸模組：

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

下載C#版本[WormHoleSharp](https://github.com/Clancey/WormHoleSharp)。

## <a name="related-links"></a>相關連結

- [GpsWatch （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WormHoleSharp （範例）](https://github.com/Clancey/WormHoleSharp)
- [Apple 的 WKInterfaceController 參考](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 與您的內含應用程式共用資料](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)

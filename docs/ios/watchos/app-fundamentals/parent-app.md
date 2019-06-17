---
title: 運用 watchOS 父系應用程式，在 Xamarin 中使用
description: 本文件說明如何使用 Xamarin 在 watchOS 父系應用程式。 它討論 WatchKit 應用程式擴充功能、 iOS 應用程式，共用存放裝置和更多功能。
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6b3a6f45d78c0febb2aacf4f7693bc6e328c3ec0
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740952"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>運用 watchOS 父系應用程式，在 Xamarin 中使用

> [!IMPORTANT]
> 存取父應用程式只會使用下列範例適用於 watchOS 1 監看式應用程式。


有不同的方式，watch 應用程式和其隨附的 iOS 應用程式之間進行通訊：

- 監看式延伸模組可以[呼叫的方法](#code)針對父應用程式的 iPhone 上背景中執行。

- 監看式延伸模組可以[共用儲存體位置](#storage)與父代的 iPhone 應用程式。

- 您可以使用遞移式來將資料從摘要或通知傳遞至監看式應用程式，將使用者傳送至應用程式中的特定介面控制站。

父應用程式有時也稱為容器應用程式。


<a name="code" />

## <a name="run-code"></a>執行程式碼

監控擴充功能和父 iPhone 應用程式之間通訊所示[GpsWatch 範例](https://developer.xamarin.com/samples/monotouch/WatchKit/GpsWatch/)。
您的監看式延伸模組可以要求父 iOS 應用程式進行一些處理，其代表使用`OpenParentApplication`方法。

針對長時間執行工作 （包括網路要求）-只有父代的 iOS 應用程式可以利用背景處理完成這些工作，並將擷取的資料儲存在監看式延伸模組可存取的位置，這會特別有用。



### <a name="watch-kit-app-extension"></a>監看式套件應用程式擴充功能

呼叫`WKInterfaceController.OpenParentApplication`在您的監看式應用程式擴充功能。 它會傳回`bool`，指出是否已傳送的方法要求，成功與否。 請檢查`error`回應參數`Action`以判斷是否有任何發生在 iPhone 應用程式中執行的方法。

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

從監看式應用程式擴充功能的所有呼叫皆都導向的 iPhone 應用程式透過`HandleWatchKitExtensionRequest`方法。
如果您正在監看式應用程式中不同的要求，則這個方法將會需要查詢`userInfo`字典，以便判斷如何處理要求。


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

如果您設定[應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md)則 iOS 8 的擴充功能 （包括監看式延伸模組） 可以與父應用程式共用資料。

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>使用 NSUserDefaults

下列程式碼可以寫成監看式應用程式擴充功能和父 iPhone 應用程式，使它們可以參考一組常用的`NSUserDefaults`:

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

IOS 應用程式和監看式延伸模組也可以共用使用常見的檔案路徑的檔案。

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

注意： 如果路徑是`null`然後核取[應用程式群組設定](~/ios/watchos/app-fundamentals/app-groups.md)確保佈建設定檔是否已正確設定，並已在開發電腦上下載/安裝。

如需詳細資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文件。

## <a name="wormholesharp"></a>WormHoleSharp

熱門的開放原始碼機制，適用於 watchOS 1 (根據[MMWormHole](https://github.com/mutualmobile/MMWormhole)) 父應用程式和 watch 應用程式之間傳遞資料或命令。

您可以設定 WormHole 使用 iOS 應用程式中的這類應用程式群組，並觀看延伸模組：

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

下載C#版本[WormHoleSharp](https://github.com/Clancey/WormHoleSharp)。



## <a name="related-links"></a>相關連結

- [GpsWatch （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WormHoleSharp （範例）](https://github.com/Clancey/WormHoleSharp)
- [Apple 的 WKInterfaceController 參考](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 的應用程式包含共用資料](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)

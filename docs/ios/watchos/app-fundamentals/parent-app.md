---
title: 使用父應用程式
description: IOS 和監看式 watchOS 1 應用程式之間共用的資料
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 769847cccb3e21fea4d8f45d8e5d0c0fb59bdd43
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-the-parent-application"></a>使用父應用程式

_IOS 和監看式 watchOS 1 應用程式之間共用的資料_

> [!IMPORTANT]
> 存取父應用程式使用下列範例只適用於 watchOS 1 監看式應用程式。


有不同的方式來監看式應用程式和其隨附的 iOS 應用程式之間進行通訊：

- 監看式延伸模組可以[呼叫的方法](#code)針對父應用程式，在 iPhone 上的 在背景中執行。

- 監看式延伸模組可以[共用的儲存位置](#storage)與父 iPhone 應用程式。

- 將資料從摘要或通知傳遞至監看式應用程式，將使用者傳送至應用程式中的特定介面控制站使用遞交。

父應用程式有時也稱為容器應用程式。


<a name="code" />

## <a name="run-code"></a>執行程式碼

Watch 擴充功能和父 iPhone 應用程式之間的通訊中示範[GpsWatch 範例](https://developer.xamarin.com/samples/GpsWatch)。
您監看的延伸模組可以要求父 iOS 應用程式進行一些處理，其代表使用`OpenParentApplication`方法。

針對長時間執行工作 （包括網路要求）-只有父系的 iOS 應用程式可以充分利用背景處理完成這些工作，並將擷取的資料儲存在監看式延伸模組可存取的位置，這是特別有用。



### <a name="watch-kit-app-extension"></a>監看式套件應用程式擴充功能

呼叫`WKInterfaceController.OpenParentApplication`中監看式的應用程式擴充功能。 它會傳回`bool`，指出是否方法要求已傳送成功與否。 請檢查`error`回應中的參數`Action`來判斷是否有任何發生在 iPhone 應用程式中執行的方法。

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


### <a name="ios-app"></a>iOS App

透過在 iPhone 應用程式進行路由傳送來自監看式應用程式擴充功能的所有呼叫`HandleWatchKitExtensionRequest`方法。
如果您正在監看式應用程式中不同的要求，則這個方法需要查詢`userInfo`字典，以便判斷如何處理要求。


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

如果您設定[應用程式群組](~/ios/watchos/app-fundamentals/app-groups.md)則 iOS 8 擴充功能 （包括監看式擴充功能） 可共用父應用程式資料。

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>NSUserDefaults

可以在監看式應用程式擴充功能和父 iPhone 應用程式中撰寫以下的程式碼，使其可以參考一組常用的`NSUserDefaults`:

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

IOS 應用程式和監看式擴充功能也可以共用檔案使用共通的檔案路徑。

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

注意： 如果路徑是`null`然後檢查[應用程式群組設定](~/ios/watchos/app-fundamentals/app-groups.md)確定佈建設定檔是否已正確設定且已在開發電腦上下載/安裝。

如需詳細資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文件。

## <a name="wormholesharp"></a>WormHoleSharp

WatchOS 1 的熱門開放原始碼機制 (根據[MMWormHole](https://github.com/mutualmobile/MMWormhole)) 父應用程式和監看式應用程式之間傳遞資料或命令。

您可以設定蟲孔使用 iOS 應用程式中的 像這樣的應用程式群組，並監看的延伸模組：

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

下載的 C# 版本[WormHoleSharp](https://github.com/Clancey/WormHoleSharp)。



## <a name="related-links"></a>相關連結

- [GpsWatch （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WormHoleSharp （範例）](https://github.com/Clancey/WormHoleSharp)
- [Apple 的 WKInterfaceController 參考](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 的應用程式包含共用資料](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)

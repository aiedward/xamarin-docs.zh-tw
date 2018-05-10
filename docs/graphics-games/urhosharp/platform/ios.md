---
title: UrhoSharp iOS 和 tvOS 支援
description: iOS 和特定的安裝程式和功能來 UrhoSharp tvOS。
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: 322297e7782a06a2d900b12cd5afc5f469009f69
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="urhosharp-ios-and-tvos-support"></a>UrhoSharp iOS 和 tvOS 支援

_iOS 和 tvOS 特定的安裝程式和功能_

雖然 Urho 是可攜式類別程式庫，並可讓相同的 API，用於在各種平台間之遊戲的邏輯，您仍然需要初始化 Urho 驅動程式中的平台特定，並在某些情況下，您會想要利用平台特定的功能.

在下列頁面中，假設`MyGame`是的 sublcass`Application`類別。

## <a name="ios-and-tvos"></a>iOS 和 tvOS

**支援的架構：** armv7 arm64，i386

## <a name="creating-a-project"></a>建立專案

建立 iOS 專案，然後將資料加入至 [資源] 目錄，並確定所有檔案都有**BundleResource**為**建置動作**。

![安裝程式的專案](ios-images/image-4.png "將資料加入 [資源] 目錄")

## <a name="configuring-and-launching-urho"></a>設定並啟動 Urho

加入 using 陳述式`Urho`和`Urho.iOS`命名空間，然後新增此程式碼初始化 Urho，以及啟動您的應用程式：

```csharp
new MyGame().Run();
```

請注意，由於需要 iOS`FinishedLaunching`若要完成，您應該排入佇列的呼叫`Run()`方法完成之後執行，這是常見慣用語：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    LaunchGame();
    return true;
}

async void LaunchGame()
{
    await Task.Yield();
    new SamplyGame().Run();
}
```

很重要，因為預設 iOS PNG 最佳化工具會產生 Urho 可以正確目前使用的影像，停用 PNG 最佳化

## <a name="custom-embedding-of-urho"></a>自訂內嵌 Urho

您可以另外讓 Urho 接管整個應用程式畫面中，並使用它做為您的應用程式的元件，您可以建立`UrhoSurface`即`UIView`，您可以內嵌在您現有的應用程式。

這是您需要執行：

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

這將因此裝載 Urho 類別，則需要執行動作：

```csharp
new MyGame().Run ();
```


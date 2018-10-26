---
title: UrhoSharp iOS 和 tvOS 支援
description: 本文件討論 iOS 和 tvOS 支援 UrhoSharp。 說明如何建立專案、 設定和啟動 Urho，以及執行 Urho 自訂內嵌。
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: f15ae458c6bd613b59700908ad7c121315e377ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108271"
---
# <a name="urhosharp-ios-and-tvos-support"></a>UrhoSharp iOS 和 tvOS 支援

雖然 Urho 是可攜式類別庫，並可讓相同的 API，來使用於各種平台的遊戲邏輯，您仍然需要初始化 Urho，在您的平台特定的驅動程式，以及在某些情況下，您會想要充分利用平台特定功能.

在下列頁面中，假設`MyGame`是的 sublcass`Application`類別。

## <a name="ios-and-tvos"></a>iOS 和 tvOS

**支援的架構：** armv7 arm64，i386

## <a name="creating-a-project"></a>建立專案

建立 iOS 專案，然後將資料加入至 [資源] 目錄，並確定所有檔案都有**BundleResource**作為**建置動作**。

![專案安裝程式](ios-images/image-4.png "將資料加入至 [資源] 目錄")

## <a name="configuring-and-launching-urho"></a>設定和啟動 Urho

加入 using 陳述式`Urho`和`Urho.iOS`命名空間，然後新增此程式碼初始化 Urho，以及啟動您的應用程式：

```csharp
new MyGame().Run();
```

請注意，因為 iOS 預期`FinishedLaunching`若要完成，您應該排入佇列的呼叫`Run()`方法完成之後執行，這是一般慣用句：

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

很重要，因為預設 iOS 的 PNG 最佳化工具將會產生 Urho 不目前正確可取用的映像，停用 PNG 最佳化

## <a name="custom-embedding-of-urho"></a>自訂內嵌 Urho

您可以或者讓 Urho 接管整個應用程式 畫面中，並將它作為您的應用程式的元件，您可以建立`UrhoSurface`即`UIView`，您可以將它內嵌在您現有的應用程式。

這是您想要做：

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

這將會裝載您 Urho 類別，因此，然後您會執行：

```csharp
new MyGame().Run ();
```


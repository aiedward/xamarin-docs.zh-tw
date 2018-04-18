---
title: UrhoSharp Android 支援
description: Android 的設定和 UrhoSharp 的功能。
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: 8d6abdac258e7a5b66e12367751652c5769405b0
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="urhosharp-android-support"></a>UrhoSharp Android 支援

_Android 的特定設定和功能_

雖然 Urho 是可攜式類別程式庫，並可讓相同的 API，用於在各種平台間之遊戲的邏輯，您仍然需要初始化 Urho 驅動程式中的平台特定，並在某些情況下，您會想要利用平台特定的功能.

在下列頁面中，假設`MyGame`是子類別的`Application`類別。

## <a name="architectures"></a>架構

**支援的架構**: x86、 armeabi，armeabi v7a

## <a name="create-a-project"></a>建立專案

建立 Android 專案，並加入 UrhoSharp NuGet 封裝。

新增資料包含您的資產**資產**目錄，並確定所有檔案都有**AndroidAsset**為**建置動作**。

![安裝程式的專案](android-images/image-3.png "包含資產目錄資產加入資料")

## <a name="configure-and-launching-urho"></a>設定並啟動 Urho

加入 using 陳述式`Urho`和`Urho.Android`命名空間，然後新增此程式碼初始化 Urho，以及啟動您的應用程式。

在 MyGame 類別中實作時，執行遊戲時，最簡單的方式是呼叫

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

這會開啟與遊戲的全螢幕活動做為內容。

## <a name="custom-embedding-of-urho"></a>自訂內嵌 Urho

您可以另外讓 Urho 接管整個應用程式畫面中，並使用它做為您的應用程式的元件，您可以建立`SurfaceView`透過：

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

您也必須在事件轉送給幾個您活動 UrhoSharp，例如：

```csharp
protected override void OnPause()
{
    UrhoSurface.OnPause();
    base.OnPause();
}
```

您必須執行相同： `OnResume`， `OnPause`， `OnLowMemory`， `OnDestroy`，`DispatchKeyEvent`和`OnWindowFocusChanged`。

這會顯示啟動遊戲的一般活動：

```csharp
[Activity(Label = "MyUrhoApp", MainLauncher = true,
    Icon = "@drawable/icon", Theme = "@android:style/Theme.NoTitleBar.Fullscreen",
    ConfigurationChanges = ConfigChanges.KeyboardHidden | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Portrait)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        var mLayout = new AbsoluteLayout(this);
        var surface = UrhoSurface.CreateSurface<MyUrhoApp>(this);
        mLayout.AddView(surface);
        SetContentView(mLayout);
    }

    protected override void OnResume()
    {
        UrhoSurface.OnResume();
        base.OnResume();
    }

    protected override void OnPause()
    {
        UrhoSurface.OnPause();
        base.OnPause();
    }

    public override void OnLowMemory()
    {
        UrhoSurface.OnLowMemory();
        base.OnLowMemory();
    }

    protected override void OnDestroy()
    {
        UrhoSurface.OnDestroy();
        base.OnDestroy();
    }

    public override bool DispatchKeyEvent(KeyEvent e)
    {
        if (!UrhoSurface.DispatchKeyEvent(e))
            return false;
        return base.DispatchKeyEvent(e);
    }

    public override void OnWindowFocusChanged(bool hasFocus)
    {
        UrhoSurface.OnWindowFocusChanged(hasFocus);
        base.OnWindowFocusChanged(hasFocus);
    }
}
```


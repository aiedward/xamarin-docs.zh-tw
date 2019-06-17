---
title: UrhoSharp Android 支援
description: 本文件說明 Android 專用的安裝程式和功能相關 UrhoSharp 的資訊。 特別的是，它支援的架構，會討論如何建立專案時，設定及啟動 Urho，和自訂內嵌 Urho。
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: e7371fa85fd5955e9a0fd285adb32844001821b3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61285166"
---
# <a name="urhosharp-android-support"></a>UrhoSharp Android 支援

_Android 的特定安裝程式和功能_

雖然 Urho 是可攜式類別庫，並可讓相同的 API，來使用於各種平台的遊戲邏輯，您仍然需要初始化 Urho，在您的平台特定的驅動程式，以及在某些情況下，您會想要充分利用平台特定功能.

在下列頁面中，假設`MyGame`是子類別的`Application`類別。

## <a name="architectures"></a>架構

**支援的架構**: x86、 armeabi，armeabi-v7a

## <a name="create-a-project"></a>建立專案

建立 Android 專案，並新增 UrhoSharp NuGet 套件。

新增資料包含您的資產**資產**目錄，並確定所有檔案都有**AndroidAsset**作為**建置動作**。

![專案安裝程式](android-images/image-3.png "加入的資料，其中包含的資產的資產目錄")

## <a name="configure-and-launching-urho"></a>設定並啟動 Urho

加入 using 陳述式`Urho`和`Urho.Android`命名空間，然後新增此程式碼初始化 Urho，以及啟動您的應用程式。

MyGame 類別中實作執行遊戲時，最簡單的方式是呼叫

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

這會開啟與遊戲的全螢幕活動做為內容。

## <a name="custom-embedding-of-urho"></a>自訂內嵌 Urho

您可以或者讓 Urho 接管整個應用程式 畫面中，並將它作為您的應用程式的元件，您可以建立`SurfaceView`透過：

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


---
title: 檢查裝置方向
description: 本文說明如何使用 Xamarin.Forms DependencyService 類別從共用程式碼存取裝置方向。
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: d8763c6fe8e330181c836bc8d10923ea676a07c1
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740939"
---
# <a name="checking-device-orientation"></a>檢查裝置方向

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)

本文會引導您使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService)，使用每個平台上的原生 API，從共用程式碼檢查裝置方向。 本逐步解說是根據 Ali Özgür 的現有 `DeviceOrientation` 外掛程式。 請參閱 [GitHub 存放庫](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation)以取得詳細資訊。

- **[建立介面](#Creating_the_Interface)** &ndash; 了解如何在共用程式碼建立介面。
- **[iOS 實作](#iOS_Implementation)** &ndash; 了解如何在適用於 iOS 的原生程式碼中實作介面。
- **[Android 實作](#Android_Implementation)** &ndash; 了解如何在適用於 Android 的原生程式碼中實作介面。
- **[UWP 實作](#WindowsImplementation)** &ndash; 了解如何在適用於通用 Windows 平台 (UWP) 的原生程式碼中實作介面。
- **[在共用程式碼中實作](#Implementing_in_Shared_Code)** &ndash; 了解如何使用 `DependencyService` 從共用程式碼呼叫原生實作。

使用 `DependencyService` 的應用程式會有下列結構：

![](device-orientation-images/orientation-diagram.png "DependencyService 應用程式結構")

> [!NOTE]
> 在共用程式碼中，可以偵測裝置是在直向還是橫向方向中，如[裝置方向](~/xamarin-forms/user-interface/layouts/device-orientation.md#Reacting_to_Changes_in_Orientation)中所示。 本文中所述的方法會使用原生功能，以取得方向的詳細資訊，包括裝置是否面朝下。

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>建立介面

首先，在共用程式碼中建立介面，表達您打算實作的功能。 針對此範例，介面包括一個單一方法：

```csharp
namespace DependencyServiceSample.Abstractions
{
    public enum DeviceOrientations
    {
        Undefined,
        Landscape,
        Portrait
    }

    public interface IDeviceOrientation
    {
        DeviceOrientations GetOrientation();
    }
}
```

在共用程式碼中針對這個介面撰寫程式碼，可讓 Xamarin.Forms 應用程式存取每個平台上的裝置方向 API。

> [!NOTE]
> 實作介面的類別必須具有無參數的建構函式，才能使用 `DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 實作

介面必須實作在每個平台特定應用程式專案中。 請注意，此類別具有無參數的建構函式，以便讓 `DependencyService` 可以建立新的執行個體：

```csharp
using UIKit;
using Foundation;

namespace DependencyServiceSample.iOS
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation(){ }

        public DeviceOrientations GetOrientation()
        {
            var currentOrientation = UIApplication.SharedApplication.StatusBarOrientation;
            bool isPortrait = currentOrientation == UIInterfaceOrientation.Portrait
                || currentOrientation == UIInterfaceOrientation.PortraitUpsideDown;

            return isPortrait ? DeviceOrientations.Portrait: DeviceOrientations.Landscape;
        }
    }
}
```

最後，將這個 `[assembly]` 屬性新增在類別上方 (且在任何已定義的命名空間外)，包括任何必要的 `using` 陳述式：

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

這個屬性會將類別註冊為 `IDeviceOrientation` 介面的實作，這表示 `DependencyService.Get<IDeviceOrientation>` 可以用在共用程式碼中來建立其執行個體。

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 實作

下例程式碼會在 Android 上實作 `IDeviceOrientation`：

```csharp
using DependencyServiceSample.Droid;
using Android.Hardware;

namespace DependencyServiceSample.Droid
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public static void Init() { }

        public DeviceOrientations GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            var rotation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = rotation == SurfaceOrientation.Rotation90 || rotation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientations.Landscape : DeviceOrientations.Portrait;
        }
    }
}
```

將這個 `[assembly]` 屬性新增在類別上方 (且在任何已定義的命名空間外)，包括任何必要的 `using` 陳述式：

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

這個屬性會將類別註冊為 `IDeviceOrientaiton` 介面的實作，這表示 `DependencyService.Get<IDeviceOrientation>` 可以用在共用程式碼中來建立其執行個體。

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台實作

下列程式碼會在通用 Windows 平台上實作 `IDeviceOrientation` 介面：

```csharp
namespace DependencyServiceSample.WindowsPhone
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public DeviceOrientations GetOrientation()
        {
            var orientation = Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
            if (orientation == Windows.UI.ViewManagement.ApplicationViewOrientation.Landscape) {
                return DeviceOrientations.Landscape;
            }
            else {
                return DeviceOrientations.Portrait;
            }
        }
    }
}
```

將 `[assembly]` 屬性新增在類別上方 (且在任何已定義的命名空間外)，包括任何必要的 `using` 陳述式：

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

這個屬性會將類別註冊為 `DeviceOrientationImplementation` 介面的實作，這表示 `DependencyService.Get<IDeviceOrientation>` 可以用在共用程式碼中來建立其執行個體。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在，我們可以撰寫並測試存取 `IDeviceOrientation` 介面的共用程式碼。 這個簡單的頁面包含一個按鈕，它會根據裝置方向更新自己的文字。 它使用 `DependencyService` 來取得 `IDeviceOrientation` 介面的執行個體 &ndash; 在執行階段，這個執行個體將會是具有原生 SDK 完整存取權的平台特定實作：

```csharp
public MainPage ()
{
    var orient = new Button {
        Text = "Get Orientation",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    orient.Clicked += (sender, e) => {
       var orientation = DependencyService.Get<IDeviceOrientation>().GetOrientation();
       switch(orientation){
           case DeviceOrientations.Undefined:
                orient.Text = "Undefined";
                break;
           case DeviceOrientations.Landscape:
                orient.Text = "Landscape";
                break;
           case DeviceOrientations.Portrait:
                orient.Text = "Portrait";
                break;
       }
    };
    Content = orient;
}
```

在 iOS、Android 或 Windows 平台上執行此應用程式，然後按下按鈕即會以裝置方向來更新按鈕的文字。

![](device-orientation-images/orientation.png "裝置方向範例")


## <a name="related-links"></a>相關連結

- [使用 DependencyService (範例)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Dependency Service (Samples)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/) (相依性服務 (範例))
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)

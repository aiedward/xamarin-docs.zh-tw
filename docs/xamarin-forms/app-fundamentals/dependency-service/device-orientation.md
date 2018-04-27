---
title: 檢查裝置方向
description: 使用共用的程式碼 DependencyService 存取裝置方向
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 09cd92b436be97f5490ac74890e4b0723bcd5701
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2018
---
# <a name="checking-device-orientation"></a>檢查裝置方向

本文將引導您使用[ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/)檢查共用的程式碼在每個平台上使用原生應用程式開發介面裝置方向。 本逐步解說根據現有`DeviceOrientation`Ali Özgür 的外掛程式。 請參閱[GitHub 儲存機制](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation)如需詳細資訊。

- **[建立介面](#Creating_the_Interface)** &ndash;了解如何在介面中的共用程式碼建立。
- **[iOS 實作](#iOS_Implementation)** &ndash;了解如何在原生 iOS 程式碼中實作介面。
- **[Android 實作](#Android_Implementation)** &ndash;了解如何在原生程式碼中實作的介面，適用於 Android。
- **[UWP 實作](#WindowsImplementation)** &ndash;深入了解如何在原生程式碼中的通用 Windows 平台 (UWP) 上實作介面。
- **[在共用程式碼中實作](#Implementing_in_Shared_Code)** &ndash;了解如何使用`DependencyService`來呼叫原生實作共用的程式碼。

應用程式使用`DependencyService`會有下列結構：

![](device-orientation-images/orientation-diagram.png "DependencyService 應用程式結構")

> [!NOTE]
> 您可偵測裝置是否在縱向或橫向中，共用的程式碼中所示的 [裝置 Orientation]/guides/xamarin-forms/user-interface/layouts/device-orientation/#changes-in-orientation)。 本文章中所述的方法會使用原生功能，來取得詳細資訊包括裝置是否面朝下的方向。

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>建立介面

首先，建立介面，在共用程式碼中表達您打算實作的功能。 例如，介面包含單一方法：

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

撰寫程式碼中的共用程式碼是此介面可讓 Xamarin.Forms 應用程式存取裝置方向應用程式開發介面，以在每個平台上。

> [!NOTE]
> 實作介面的類別都必須有參數的建構函式，才能使用`DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 實作

必須實作介面，每個平台專屬的應用程式專案中。 請注意，此類別的無參數建構函式，讓`DependencyService`可以建立新的執行個體：

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

最後，加入下列`[assembly]`屬性上方類別 （和任何已定義的命名空間之外），包括任何必要`using`陳述式：

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

此屬性做為實作的註冊類別`IDeviceOrientation`介面，這表示`DependencyService.Get<IDeviceOrientation>`可以共用的程式碼中用來建立它的執行個體。

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 的實作

下列程式碼會實作`IDeviceOrientation`在 Android 上：

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

加入這個`[assembly]`屬性上方類別 （和任何已定義的命名空間之外），包括任何必要`using`陳述式：

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

此屬性做為實作的註冊類別`IDeviceOrientaiton`介面，這表示`DependencyService.Get<IDeviceOrientation>`可用於共用程式碼可以建立它的執行個體。

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台實作

下列程式碼會實作`IDeviceOrientation`通用 Windows 平台上的介面：

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

新增`[assembly]`屬性上方類別 （和任何已定義的命名空間之外），包括任何必要`using`陳述式：

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

此屬性做為實作的註冊類別`DeviceOrientationImplementation`介面，這表示`DependencyService.Get<IDeviceOrientation>`可用於共用程式碼可以建立它的執行個體。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在我們可以撰寫並測試存取的共用程式碼`IDeviceOrientation`介面。 這個簡單的頁面將包含更新自己文字，根據裝置方向的按鈕。 它會使用`DependencyService`來取得執行個體的`IDeviceOrientation`介面&ndash;在執行階段，這個執行個體將會具有完整存取權的原生 SDK 的平台特定實作：

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

在 iOS、 Android 或 Windows 平台上執行此應用程式，以及按下的按鈕會導致更新裝置的方向與按鈕的文字。

![](device-orientation-images/orientation.png "裝置方向範例")


## <a name="related-links"></a>相關連結

- [使用 DependencyService （範例）](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService （範例）](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)

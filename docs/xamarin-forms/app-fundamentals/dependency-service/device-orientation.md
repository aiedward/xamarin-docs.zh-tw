---
title: 檢查裝置方向
description: 這篇文章說明如何使用 Xamarin.Forms DependencyService 類別來存取共用的程式碼的裝置方向。
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 620404a217b2e8a31192ae6613dcec023ac366cd
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995637"
---
# <a name="checking-device-orientation"></a>檢查裝置方向

這篇文章會引導您使用[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)來檢查裝置方向，從每個平台上使用原生 Api 的共用程式碼。 本逐步解說根據現有`DeviceOrientation`Ali Özgür 外掛程式。 請參閱[GitHub 存放庫](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation)如需詳細資訊。

- **[建立介面](#Creating_the_Interface)** &ndash;了解如何在介面中的共用程式碼建立。
- **[iOS 實作](#iOS_Implementation)** &ndash;了解如何在適用於 iOS 的原生程式碼中實作的介面。
- **[Android 的實作](#Android_Implementation)** &ndash;了解如何在原生程式碼中實作的介面，適用於 Android。
- **[UWP 實作](#WindowsImplementation)** &ndash;了解如何在原生程式碼適用於通用 Windows 平台 (UWP) 中實作介面。
- **[在共用程式碼中實作](#Implementing_in_Shared_Code)** &ndash;了解如何使用`DependencyService`呼叫原生實作共用的程式碼。

應用程式使用`DependencyService`會有下列結構：

![](device-orientation-images/orientation-diagram.png "DependencyService 應用程式結構")

> [!NOTE]
> 可偵測裝置是否在直向或橫向方向中，共用的程式碼中所示的 [裝置 Orientation]/guides/xamarin-forms/user-interface/layouts/device-orientation/#changes-in-orientation)。 這篇文章中所述的方法會使用原生功能，以取得詳細資訊包括裝置是否面朝下的方向。

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>建立介面

首先，表達您打算實作的功能的共用程式碼中建立的介面。 對於此範例中，介面會包含單一方法：

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

針對這個介面，在共用的程式碼中撰寫程式碼，可讓 Xamarin.Forms 應用程式存取每個平台上的裝置方向 Api。

> [!NOTE]
> 實作介面的類別必須具有無參數的建構函式，才能使用`DependencyService`。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 的實作

每個平台專屬的應用程式專案中，就必須實作的介面。 請注意，此類別具有無參數建構函式，讓`DependencyService`可以建立新的執行個體：

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

最後，新增這`[assembly]`屬性在類別上方 （和任何已定義的命名空間之外），包括任何必要`using`陳述式：

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

這個屬性會註冊類別實作`IDeviceOrientation`介面，這表示`DependencyService.Get<IDeviceOrientation>`可以共用的程式碼中用來建立它的執行個體。

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

新增這`[assembly]`屬性在類別上方 （和任何已定義的命名空間之外），包括任何必要`using`陳述式：

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

這個屬性會註冊類別實作`IDeviceOrientaiton`介面，這表示`DependencyService.Get<IDeviceOrientation>`可用於共用程式碼可以建立它的執行個體。

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

新增`[assembly]`屬性在類別上方 （和任何已定義的命名空間之外），包括任何必要`using`陳述式：

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

這個屬性會註冊類別實作`DeviceOrientationImplementation`介面，這表示`DependencyService.Get<IDeviceOrientation>`可用於共用程式碼可以建立它的執行個體。

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在，我們可以撰寫並測試存取的共用程式碼`IDeviceOrientation`介面。 這個簡單的頁面包含一個按鈕，更新它自己裝置方向為基礎的文字。 它會使用`DependencyService`若要取得的執行個體`IDeviceOrientation`介面&ndash;在執行階段，這個執行個體將會是具有完整存取權的原生 SDK 的平台特定實作：

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

在 iOS、 Android 或 Windows 平台上執行此應用程式，以及按下按鈕會導致更新裝置的方向與按鈕的文字。

![](device-orientation-images/orientation.png "裝置方向範例")


## <a name="related-links"></a>相關連結

- [使用 DependencyService （範例）](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService （範例）](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)

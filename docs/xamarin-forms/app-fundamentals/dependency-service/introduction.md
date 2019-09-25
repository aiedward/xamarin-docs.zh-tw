---
title: Xamarin.Forms DependencyService 簡介
description: 此文章說明如何使用 Xamarin.Forms DependencyService 類別叫用原生平台的功能。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: b27b4b0c3c5662c6cc1c2c151dd9ebe1523da3a4
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71198512"
---
# <a name="xamarinforms-dependencyservice-introduction"></a>Xamarin.Forms DependencyService 簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 類別是服務定位器，讓 Xamarin.Forms 應用程式能夠從共用程式碼叫用原生平台功能。

使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 叫用元生平台功能的程序為：

1. 在共用程式碼中建立原生平台功能的介面。 如需詳細資訊，請參閱[建立介面](#create-an-interface)。
1. 在必要的平台專案中實作介面。 如需詳細資訊，請參閱[在每個平台上實作介面](#implement-the-interface-on-each-platform)。
1. 向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄平台實作。 這讓 Xamarin.Forms 在執行階段能夠定位平台實作。 如需詳細資訊，請參閱[登錄平台實作](#register-the-platform-implementations)。
1. 從共用程式碼解析平台實作並叫用它們。 如需詳細資訊，請參閱[解析平台實作](#resolve-the-platform-implementations)。

下列圖表顯示在 Xamarin.Forms 中叫用平台功能的方式：

![使用 Xamarin.Forms DependencyService 類別之服務位置的概觀](introduction-images/dependency-service.png "DependencyService 服務位置")

## <a name="create-an-interface"></a>建立介面

若要從共用程式碼叫用原生平台功能，第一步是建立介面，定義與原生平台功能互動的 API。 此介面應該放在共用程式碼專案中。

下列範例示範可擷取裝置方向之 API 的介面：

```csharp
public interface IDeviceOrientationService
{
    DeviceOrientation GetOrientation();
}
```

## <a name="implement-the-interface-on-each-platform"></a>在每個平台上實作介面

建立定義與原生平台功能互動之 API 的介面之後，該介面必須在每個平台專案中實作。

### <a name="ios"></a>iOS

下列程式碼範例顯示 iOS 上 `IDeviceOrientationService` 介面的實作：

```csharp
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            UIInterfaceOrientation orientation = UIApplication.SharedApplication.StatusBarOrientation;

            bool isPortrait = orientation == UIInterfaceOrientation.Portrait ||
                orientation == UIInterfaceOrientation.PortraitUpsideDown;
            return isPortrait ? DeviceOrientation.Portrait : DeviceOrientation.Landscape;
        }
    }
}
```

### <a name="android"></a>Android

下列程式碼範例顯示 Android 上 `IDeviceOrientationService` 介面的實作：

```csharp
namespace DependencyServiceDemos.Droid
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            SurfaceOrientation orientation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = orientation == SurfaceOrientation.Rotation90 ||
                orientation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

下列程式碼範例顯示通用 Windows 平台 (UWP) 上 `IDeviceOrientationService` 介面的實作：

```csharp
namespace DependencyServiceDemos.UWP
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ApplicationViewOrientation orientation = ApplicationView.GetForCurrentView().Orientation;
            return orientation == ApplicationViewOrientation.Landscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

## <a name="register-the-platform-implementations"></a>登錄平台實作

在每個平台專案中實作該介面之後，平台實作必須向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄，讓 Xamarin.Forms 可以在執行階段找到它們。 這通常是透過 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 來執行的，這表示所指定型別會提供介面的實作。

下列範例示範使用 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 登錄 `IDeviceOrientationService` 介面的 iOS 實作：

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DependencyServiceDemos.iOS.DeviceOrientationService))]
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ...
        }
    }
}
```

在此範例中，[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄 `DeviceOrientationService`。 同樣地，其他平台上的 `IDeviceOrientationService` 介面實作應該向 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 登錄。

如需向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄平台實作的詳細資訊，請參閱 [Xamarin.Forms DependencyService 登錄與解析](registration-and-resolution.md)。

## <a name="resolve-the-platform-implementations"></a>解析平台實作

向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄平台實作之後，必須先解析實作，才能叫用它們。 這通常在共用程式碼中使用 [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法來執行。

下列程式碼範例顯示呼叫 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法來解析 `IDeviceOrientationService` 介面，然後叫用其 `GetOrientation` 方法：

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

如需向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 解析平台實作的詳細資訊，請參閱 [Xamarin.Forms DependencyService 登錄與解析](registration-and-resolution.md)。

## <a name="related-links"></a>相關連結

- [DependencyService 示範 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Xamarin.Forms DependencyService 登錄與解析](registration-and-resolution.md)

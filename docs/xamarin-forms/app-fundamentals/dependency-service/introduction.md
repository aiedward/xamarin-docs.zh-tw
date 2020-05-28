---
title: Xamarin.FormsDependencyService 簡介
description: 本文說明如何使用 Xamarin.Forms DependencyService 類別來叫用原生平臺功能。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f4d43a0c9c4878733d65b170c27e744b397aa4d0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138355"
---
# <a name="xamarinforms-dependencyservice-introduction"></a>Xamarin.FormsDependencyService 簡介

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

[`DependencyService`](xref:Xamarin.Forms.DependencyService)類別是一種服務定位器，可讓 Xamarin.Forms 應用程式從共用程式碼叫用原生平臺功能。

使用來叫用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 原生平臺功能的程式是：

1. 在共用程式碼中建立原生平台功能的介面。 如需詳細資訊，請參閱[建立介面](#create-an-interface)。
1. 在必要的平台專案中實作介面。 如需詳細資訊，請參閱[在每個平台上實作介面](#implement-the-interface-on-each-platform)。
1. 使用註冊平臺的執行 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 這可讓 Xamarin.Forms 在執行時間找出平臺的實現。 如需詳細資訊，請參閱[登錄平台實作](#register-the-platform-implementations)。
1. 從共用程式碼解析平台實作並叫用它們。 如需詳細資訊，請參閱[解析平台實作](#resolve-the-platform-implementations)。

下圖顯示如何在應用程式中叫用原生平臺功能 Xamarin.Forms ：

![使用 DependencyService 類別的服務位置總覽 Xamarin.Forms](introduction-images/dependency-service.png "DependencyService 服務位置")

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

在每個平臺專案中執行介面之後，平臺的程式必須向註冊 [`DependencyService`](xref:Xamarin.Forms.DependencyService) ，以便 Xamarin.Forms 在執行時間找到它們。 這通常會使用來執行 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) ，這表示指定的類型會提供介面的執行。

下列範例顯示 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 如何使用來註冊介面的 iOS 執行 `IDeviceOrientationService` ：

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

在此範例中，會 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) `DeviceOrientationService` 向註冊 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 同樣地， `IDeviceOrientationService` 其他平臺上的介面的執行應該會向註冊 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 。

如需使用註冊平臺部署的詳細資訊 [`DependencyService`](xref:Xamarin.Forms.DependencyService) ，請參閱[ Xamarin.Forms DependencyService 註冊與解決方法](registration-and-resolution.md)。

## <a name="resolve-the-platform-implementations"></a>解析平台實作

使用在中註冊平臺的之後 [`DependencyService`](xref:Xamarin.Forms.DependencyService) ，必須先解析執行，然後再叫用。 這通常是使用方法在共用程式碼中執行 [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 。

下列程式碼顯示呼叫 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法以解析 `IDeviceOrientationService` 介面，然後叫用其方法的範例 `GetOrientation` ：

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

如需使用來解析平臺執行的詳細資訊 [`DependencyService`](xref:Xamarin.Forms.DependencyService) ，請參閱[ Xamarin.Forms DependencyService 註冊與解決方法](registration-and-resolution.md)。

## <a name="related-links"></a>相關連結

- [DependencyService 示範 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Xamarin.FormsDependencyService 註冊與解決](registration-and-resolution.md)

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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "71198512"
---
# <a name="xamarinforms-dependencyservice-introduction"></a>Xamarin.Forms DependencyService 簡介

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

該[`DependencyService`](xref:Xamarin.Forms.DependencyService)類是一個服務定位器,使 Xamarin.Forms 應用程式能夠從共享代碼調用本機平臺功能。

使用[`DependencyService`](xref:Xamarin.Forms.DependencyService)呼叫本機平台功能的過程是:

1. 在共用程式碼中建立原生平台功能的介面。 如需詳細資訊，請參閱[建立介面](#create-an-interface)。
1. 在必要的平台專案中實作介面。 如需詳細資訊，請參閱[在每個平台上實作介面](#implement-the-interface-on-each-platform)。
1. 將平台實現註冊到[`DependencyService`](xref:Xamarin.Forms.DependencyService)。 這讓 Xamarin.Forms 在執行階段能夠定位平台實作。 如需詳細資訊，請參閱[登錄平台實作](#register-the-platform-implementations)。
1. 從共用程式碼解析平台實作並叫用它們。 如需詳細資訊，請參閱[解析平台實作](#resolve-the-platform-implementations)。

下列圖表顯示在 Xamarin.Forms 中叫用平台功能的方式：

![使用 Xamarin.表單類相依項服務類別的服務位置概述](introduction-images/dependency-service.png "相依服務服務位置")

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

在每個平台項目中實現介面后,平臺實現必須註冊到[`DependencyService`](xref:Xamarin.Forms.DependencyService),以便 Xamarin.Forms 可以在運行時找到它們。 這通常使用執行,[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)指示指定的類型提供介面的實現。

下面的範例顯示了使用[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)`IDeviceOrientationService`註冊介面的 iOS 實現:

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

這個範例中,註冊[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)到`DeviceOrientationService` [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 同樣,其他平台上介面`IDeviceOrientationService`的實現也應註冊到[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)。

有關將平台實現註冊到的詳細資訊,[`DependencyService`](xref:Xamarin.Forms.DependencyService)請參閱[Xamarin.窗體依賴項服務註冊和解決](registration-and-resolution.md)。

## <a name="resolve-the-platform-implementations"></a>解析平台實作

在將平台實現註冊后,[`DependencyService`](xref:Xamarin.Forms.DependencyService)必須先在調用 之前解析實現。 這通常在使用方法[`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)的共享代碼中執行。

以下代碼顯示了呼叫[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)方法`IDeviceOrientationService`解析 介面,然後呼`GetOrientation`叫其 方法的範例:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

有關使用 解析平台實現的詳細資訊[`DependencyService`](xref:Xamarin.Forms.DependencyService), 請參考[Xamarin.窗體相依項服務註冊與解決](registration-and-resolution.md)。

## <a name="related-links"></a>相關連結

- [DependencyService 示範 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Xamarin.Forms DependencyService 登錄與解析](registration-and-resolution.md)

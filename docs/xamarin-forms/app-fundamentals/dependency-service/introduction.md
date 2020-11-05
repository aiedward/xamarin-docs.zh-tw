---
title: Xamarin.Forms DependencyService 簡介
description: 本文說明如何使用 Xamarin.Forms DependencyService 類別來叫用原生平臺功能。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1234584dda50746e45d121eb03a3c6abafd8473a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371504"
---
# <a name="no-locxamarinforms-dependencyservice-introduction"></a>Xamarin.Forms DependencyService 簡介

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/dependencyservice/)

[`DependencyService`](xref:Xamarin.Forms.DependencyService)類別是服務定位器，可讓 Xamarin.Forms 應用程式從共用程式碼叫用原生平臺功能。

使用來叫用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 原生平臺功能的程式是：

1. 在共用程式碼中建立原生平台功能的介面。 如需詳細資訊，請參閱[建立介面](#create-an-interface)。
1. 在必要的平台專案中實作介面。 如需詳細資訊，請參閱[在每個平台上實作介面](#implement-the-interface-on-each-platform)。
1. 使用註冊平臺執行 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 這可讓您 Xamarin.Forms 在執行時間找出平臺執行。 如需詳細資訊，請參閱[登錄平台實作](#register-the-platform-implementations)。
1. 從共用程式碼解析平台實作並叫用它們。 如需詳細資訊，請參閱[解析平台實作](#resolve-the-platform-implementations)。

下圖顯示如何在應用程式中叫用原生平臺功能 Xamarin.Forms ：

![使用：：：非 loc (Xamarin. Forms) ：：： DependencyService 類別的服務位置總覽](introduction-images/dependency-service.png "DependencyService 服務位置")

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

在每個平臺專案中執行介面之後，必須向註冊平臺 [`DependencyService`](xref:Xamarin.Forms.DependencyService) ，使 Xamarin.Forms 其可在執行時間找到它們。 這通常是使用來執行 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) ，這表示指定的型別會提供介面的實作為。

下列範例顯示 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 如何使用註冊介面的 iOS 實作為 `IDeviceOrientationService` ：

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

在此範例中，會 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) `DeviceOrientationService` 向註冊 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 同樣地， `IDeviceOrientationService` 其他平臺上的介面實作為註冊 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 。

如需使用註冊平臺的詳細資訊 [`DependencyService`](xref:Xamarin.Forms.DependencyService) ，請參閱[ Xamarin.Forms DependencyService 註冊和解決](registration-and-resolution.md)方式。

## <a name="resolve-the-platform-implementations"></a>解析平台實作

使用完成平臺執行的註冊之後 [`DependencyService`](xref:Xamarin.Forms.DependencyService) ，必須先解決這些必須解決的問題。 這通常是使用方法在共用程式碼中執行 [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 。

下列程式碼顯示呼叫 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法來解析介面，然後叫用 `IDeviceOrientationService` 其方法的範例 `GetOrientation` ：

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

如需使用來解析平臺執行的詳細資訊 [`DependencyService`](xref:Xamarin.Forms.DependencyService) ，請參閱[ Xamarin.Forms DependencyService 註冊和解決](registration-and-resolution.md)方式。

## <a name="related-links"></a>相關連結

- [DependencyService 示範 (範例)](/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Xamarin.Forms DependencyService 註冊和解析](registration-and-resolution.md)
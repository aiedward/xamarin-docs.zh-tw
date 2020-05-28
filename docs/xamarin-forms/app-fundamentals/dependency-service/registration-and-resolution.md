---
title: Xamarin.FormsDependencyService 註冊與解決
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
ms.openlocfilehash: 50d77e9ba41767aa1f676bf21994431844fc4530
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138768"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Xamarin.FormsDependencyService 註冊與解決

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

使用來叫用 Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 原生平臺功能時，必須向註冊平臺執行 `DependencyService` ，然後從共用程式碼加以解析，以叫用它們。

## <a name="register-platform-implementations"></a>登錄平台實作

平臺程式必須向註冊， [`DependencyService`](xref:Xamarin.Forms.DependencyService) Xamarin.Forms 才能在執行時間找到它們。

您可以使用或方法來執行註冊 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 。

> [!IMPORTANT]
> 使用 .NET native 編譯的 UWP 專案發行組建，應該使用方法來註冊平臺的執行 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 。

### <a name="registration-by-attribute"></a>透過屬性登錄

[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)可以用來向註冊平臺執行 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 該屬性表示所指定型別會提供介面的具體實作。

下列範例顯示 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 如何使用來註冊介面的 iOS 執行 `IDeviceOrientationService` ：

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DeviceOrientationService))]
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

在此範例中，會 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) `DeviceOrientationService` 向註冊 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 這會導致具象型別向它所實作的介面登錄。

同樣地， `IDeviceOrientationService` 其他平臺上的介面的執行應該會向註冊 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 。

> [!NOTE]
> 向註冊， [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 會在命名空間層級執行。

### <a name="registration-by-method"></a>透過方法登錄

[`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*)方法可以用來向註冊平臺的執行 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。

下列範例顯示 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 如何使用方法來註冊介面的 iOS 執行 `IDeviceOrientationService` ：

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new App());
        DependencyService.Register<IDeviceOrientationService, DeviceOrientationService>();
        return base.FinishedLaunching(app, options);
    }
}
```

在此範例中， [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法會針對介面註冊實體類型 `DeviceOrientationService` `IDeviceOrientationService` 。 或者，您也可以使用方法的多載， [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 向註冊平臺執行 [`DependencyService`](xref:Xamarin.Forms.DependencyService) ：

```csharp
DependencyService.Register<DeviceOrientationService>();
```

在此範例中， [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法會 `DeviceOrientationService` 向註冊 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 這會導致具象型別向它所實作的介面登錄。

同樣地，您 `IDeviceOrientationService` 可以使用方法來註冊其他平臺上介面的執行 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 。

> [!IMPORTANT]
> 使用方法註冊 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 必須在平臺專案中執行，然後才能從共用程式碼叫用平臺執行所提供的功能。

## <a name="resolve-the-platform-implementations"></a>解析平台實作

必須先解析平台實作，才能叫用它。 這通常是使用方法在共用程式碼中執行 [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 。 不過，它也可以使用方法來完成 [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 。

根據預設， [`DependencyService`](xref:Xamarin.Forms.DependencyService) 只會解析具有無參數的函式的平臺執行。 不過，您可以在中插入相依性解析方法， Xamarin.Forms 以使用相依性插入容器或 factory 方法來解析平臺的執行。 這種方法可用來解析具有參數建構函式的平台實作。 如需詳細資訊，請參閱[中 Xamarin.Forms ](~/xamarin-forms/internals/dependency-resolution.md)的相依性解析。

> [!IMPORTANT]
> 叫用尚未向註冊的平臺執行時， [`DependencyService`](xref:Xamarin.Forms.DependencyService) 會導致擲回 `NullReferenceException` 。

### <a name="resolve-using-the-getlttgt-method"></a>使用 Get&lt;T&gt; 方法來解析

[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)方法會在執行時間抓取介面的平臺實 `T` 作為，並將其實例建立為 singleton。 此執行個體會在應用程式的存留期內存留，任何解析相同平台實作的後續呼叫，都會擷取相同執行個體。

下列程式碼顯示呼叫 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法以解析 `IDeviceOrientationService` 介面，然後叫用其方法的範例 `GetOrientation` ：

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)根據預設，方法會將介面的平臺實作為單一實例來建立 `T` 。 不過，可以變更此行為。 如需詳細資訊，請參閱[管理已解析物件的存留期](#manage-the-lifetime-of-resolved-objects)。

### <a name="resolve-using-the-resolvelttgt-method"></a>使用 Resolve&lt;T&gt; 方法來解析

[`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)方法 `T` 會在執行時間使用已插入至類別的相依性解析方法，來抓取介面的平臺執行 Xamarin.Forms [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) 。 如果尚未將相依性解析方法插入 Xamarin.Forms ，此 `Resolve<T>` 方法將會回到呼叫 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法來抓取平臺的執行。 如需在中插入相依性解析方法的詳細資訊 Xamarin.Forms ，請參閱[中 Xamarin.Forms ](~/xamarin-forms/internals/dependency-resolution.md)的相依性解析。

下列程式碼顯示呼叫 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法以解析 `IDeviceOrientationService` 介面，然後叫用其方法的範例 `GetOrientation` ：

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> 當 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法回到呼叫 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法時，它預設會建立介面的平臺實 `T` 作為單一實例。 不過，可以變更此行為。 如需詳細資訊，請參閱[管理已解析物件的存留期](#manage-the-lifetime-of-resolved-objects)。

## <a name="manage-the-lifetime-of-resolved-objects"></a>管理已解析物件的存留期

類別的預設行為 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 是將平臺實作為解析為單次個體。 因此，平台實作會在應用程式存留期內存留。

這個行為是使用 [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) 和方法上的選擇性引數所指定 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 。 [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget)列舉會定義兩個成員：

- `GlobalInstance`，此成員會將平台實作以 Singleton 傳回。
- `NewInstance`，此成員會傳回平台實作的新執行個體。 平台執行個體的存留期接著會由應用程式負責管理。

[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)和 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法都會將其選擇性引數設定為 [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget) ，因此平臺實作為一律會解析為單次個體。 您可以變更此行為，藉由指定 [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) 作為和方法的引數來建立平臺實例的新實例 `Get<T>` `Resolve<T>` ：

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

在此範例中，會 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 為介面建立平臺執行的新實例 `ITextToSpeechService` 。 解析 `ITextToSpeechService` 的任何後續呼叫，都會建立新執行個體。

一律建立平台實作的新執行個體，會使應用程式變得必須負責管理執行個體的存留期。 這表示如果您訂閱平台實作中定義的事件，當不再需要平台實作時，您應該取消訂閱事件。 此外，這表示平台實作可能需要實作 `IDisposable`，並在 `Dispose` 方法中清除其資源。 範例應用程式在其 `TextToSpeechService` 平台實作中示範此案例。

當應用程式完成使用實作 `IDisposable` 的平台實作時，它應該呼叫該物件的 `Dispose` 實作。 達成此目的的其中一個方法是使用 `using` 陳述式：

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
using (service as IDisposable)
{
    await service.SpeakAsync("Hello world");
}
```

在此範例中，叫用 `SpeakAsync` 方法之後，`using` 陳述式會自動處置平台實作物件。 這會導致物件的 `Dispose` 方法被叫用，它會執行所需的清理。

如需呼叫物件 `Dispose` 方法的詳細資訊，請參閱[使用實作 IDisposable 的物件](/dotnet/standard/garbage-collection/using-objects)。

## <a name="related-links"></a>相關連結

- [DependencyService 示範 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [中的相依性解析Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md)

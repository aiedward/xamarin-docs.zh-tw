---
title: Xamarin.Forms DependencyService 登錄與解析
description: 此文章說明如何使用 Xamarin.Forms DependencyService 類別叫用原生平台的功能。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: a295c04d3b8819864b80ff56e215d22cc6ec0765
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658281"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Xamarin.Forms DependencyService 登錄與解析

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/DependencyServiceDemos)

使用 Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 來叫用原生平台功能時，平台實作必須向 `DependencyService` 登錄，然後從共用程式碼解析以叫用它們。

## <a name="register-platform-implementations"></a>登錄平台實作

平台實作必須向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄，讓 Xamarin.Forms 可以在執行階段找到它們。

登錄可使用 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 或 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法來執行。

> [!IMPORTANT]
> 使用 .NET 原生編譯的 UWP 專案發行組建，應該使用 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法登錄平台實作。

### <a name="registration-by-attribute"></a>透過屬性登錄

[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 可用於向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄平台實作。 該屬性表示所指定型別會提供介面的具體實作。

下列範例示範使用 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 登錄 `IDeviceOrientationService` 介面的 iOS 實作：

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

在此範例中，[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄 `DeviceOrientationService`。 這會導致具象型別向它所實作的介面登錄。

同樣地，其他平台上的 `IDeviceOrientationService` 介面實作應該向 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 登錄。

> [!NOTE]
> 向 [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) 登錄是在命名空間層級執行。

### <a name="registration-by-method"></a>透過方法登錄

[`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法可用於向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄平台實作。

下列範例示範使用 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法來登錄 `IDeviceOrientationService` 介面的 iOS 實作：

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

在此範例中，[`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法向 `IDeviceOrientationService` 介面登錄 `DeviceOrientationService` 具象型別。 或者，[`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法的多載也可用於向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄平台實作：

```csharp
DependencyService.Register<DeviceOrientationService>();
```

在此範例中，[`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄 `DeviceOrientationService`。 這會導致具象型別向它所實作的介面登錄。

同樣地，其他平台上的 `IDeviceOrientationService` 介面實作可以使用 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法登錄。

> [!IMPORTANT]
> 在從共用程式碼呼叫平台實作提供的功能之前，必須先在平台專案中，使用 [`Register`](xref:Xamarin.Forms.DependencyService.Register*) 方法執行登錄。

## <a name="resolve-the-platform-implementations"></a>解析平台實作

必須先解析平台實作，才能叫用它。 這通常在共用程式碼中使用 [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法來執行。 不過，也可以使用 [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法來完成。

根據預設，[`DependencyService`](xref:Xamarin.Forms.DependencyService) 僅會解析具有無參數建構函式的平台實作。 不過，您可以將相依性解析方法插入 Xamarin.Forms，其會使用相依性插入容器或 Factory 方法來解析平台實作。 這種方法可用來解析具有參數建構函式的平台實作。 如需詳細資訊，請參閱 [Xamarin.Forms 中的相依性解析](~/xamarin-forms/internals/dependency-resolution.md)。

> [!IMPORTANT]
> 叫用尚未向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄的平台實作會導致擲回 `NullReferenceException`。

### <a name="resolve-using-the-getlttgt-method"></a>使用 Get&lt;T&gt; 方法來解析

[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法會在執行階段擷取 `T` 介面的平台實作，並將其執行個體建立為 Singleton。 此執行個體會在應用程式的存留期內存留，任何解析相同平台實作的後續呼叫，都會擷取相同執行個體。

下列程式碼範例顯示呼叫 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法來解析 `IDeviceOrientationService` 介面，然後叫用其 `GetOrientation` 方法：

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> 根據預設，[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法會將介面 `T` 的平台實作執行個體建立為 Singleton。 不過，可以變更此行為。 如需詳細資訊，請參閱[管理已解析物件的存留期](#manage-the-lifetime-of-resolved-objects)。

### <a name="resolve-using-the-resolvelttgt-method"></a>使用 Resolve&lt;T&gt; 方法來解析

[`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法會在執行階段使用以 [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) 類別插入 Xamarin.Forms 的相依性解析方法，擷取介面 `T` 的平台實作。 如果相依性解析方法尚未插入 Xamarin.Forms，`Resolve<T>` 方法會改為呼叫 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法來擷取平台實作。 如需將相依性解析方法插入 Xamarin.Forms 的詳細資訊，請參閱 [Xamarin.Forms 中的相依性解析](~/xamarin-forms/internals/dependency-resolution.md)。

下列程式碼範例顯示呼叫 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法來解析 `IDeviceOrientationService` 介面，然後叫用其 `GetOrientation` 方法：

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> 根據預設，當 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法改為呼叫 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 方法時，它會將介面 `T` 平台實作的執行個體建立為 Singleton。 不過，可以變更此行為。 如需詳細資訊，請參閱[管理已解析物件的存留期](#manage-the-lifetime-of-resolved-objects)。

## <a name="manage-the-lifetime-of-resolved-objects"></a>管理已解析物件的存留期

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 類別的預設行為是要將平台實作解析為 Singleton。 因此，平台實作會在應用程式存留期內存留。

此行為是以 [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 上的 [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) 選用引數和 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 來指定。 [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) 列舉定義兩個成員：

- `GlobalInstance`，此成員會將平台實作以 Singleton 傳回。
- `NewInstance`，此成員會傳回平台實作的新執行個體。 平台執行個體的存留期接著會由應用程式負責管理。

[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) 與 [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 方法都將其選擇性引數設定為 [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget)，因此平台實作一律會解析為 Singleton。 若要變更此行為，可將 [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) 指定為 `Get<T>` 與 `Resolve<T>` 方法的引數，以建立平台實作的新執行個體：

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

在此範例中，[`DependencyService`](xref:Xamarin.Forms.DependencyService) 建立 `ITextToSpeechService` 介面平台執行個體的新執行個體。 解析 `ITextToSpeechService` 的任何後續呼叫，都會建立新執行個體。

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

- [DependencyService 示範 (範例)](https://developer.xamarin.com/samples/xamarin-forms/DependencyServiceDemos)
- [Xamarin.Forms 中的相依性解析](~/xamarin-forms/internals/dependency-resolution.md)

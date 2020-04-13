---
title: Xamarin.Forms DependencyService 登錄與解析
description: 此文章說明如何使用 Xamarin.Forms DependencyService 類別叫用原生平台的功能。
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: 6e666c16c9b1afc3478f524cae2f84d6704319c2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70199218"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Xamarin.Forms DependencyService 登錄與解析

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

使用 Xamarin.Forms[`DependencyService`](xref:Xamarin.Forms.DependencyService)呼叫本機平臺功能時,`DependencyService`必須向註冊平台實現,然後從共享代碼解析以調用它們。

## <a name="register-platform-implementations"></a>登錄平台實作

平台實現必須註冊到[`DependencyService`](xref:Xamarin.Forms.DependencyService), 以便 Xamarin.Forms 可以在執行時找到它們。

可以使用 或[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)[`Register`](xref:Xamarin.Forms.DependencyService.Register*)方法 執行註冊。

> [!IMPORTANT]
> 使用 .NET 本機編譯的 UWP 專案的[`Register`](xref:Xamarin.Forms.DependencyService.Register*)發佈版本應使用 方法註冊平台實現。

### <a name="registration-by-attribute"></a>透過屬性登錄

[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)可用於將平台取得註冊到[`DependencyService`](xref:Xamarin.Forms.DependencyService)。 該屬性表示所指定型別會提供介面的具體實作。

下面的範例顯示了使用[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)`IDeviceOrientationService`註冊介面的 iOS 實現:

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

這個範例中,註冊[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)到`DeviceOrientationService` [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 這會導致具象型別向它所實作的介面登錄。

同樣,其他平台上介面`IDeviceOrientationService`的實現也應註冊到[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)。

> [!NOTE]
> 在命名空間[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)級別對執行與的註冊。

### <a name="registration-by-method"></a>透過方法登錄

這些方法[`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*)可用於將平台實現註冊到[`DependencyService`](xref:Xamarin.Forms.DependencyService)。

下面的範例顯示了使用方法[`Register`](xref:Xamarin.Forms.DependencyService.Register*)`IDeviceOrientationService`註冊 介面的 iOS 實現:

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

這個方法[`Register`](xref:Xamarin.Forms.DependencyService.Register*)根據介面註冊具體類型,`DeviceOrientationService``IDeviceOrientationService`該方法根據介面註冊具體類型。 或,[`Register`](xref:Xamarin.Forms.DependencyService.Register*)該方法的重載可用於將平台取得註冊[`DependencyService`](xref:Xamarin.Forms.DependencyService)到 :

```csharp
DependencyService.Register<DeviceOrientationService>();
```

這個選項,[`Register`](xref:Xamarin.Forms.DependencyService.Register*)方法`DeviceOrientationService`將註冊到 。 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 這會導致具象型別向它所實作的介面登錄。

同樣,可以在其他平台上實現`IDeviceOrientationService`介面,也可以向這些[`Register`](xref:Xamarin.Forms.DependencyService.Register*)方法 註冊。

> [!IMPORTANT]
> 在從共享[`Register`](xref:Xamarin.Forms.DependencyService.Register*)代碼調用平臺實現提供的功能之前,必須在平臺專案中執行與方法的註冊。

## <a name="resolve-the-platform-implementations"></a>解析平台實作

必須先解析平台實作，才能叫用它。 這通常在使用方法[`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)的共享代碼中執行。 但是,也可以使用方法[`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)完成。

預設情況下,[`DependencyService`](xref:Xamarin.Forms.DependencyService)將僅解析具有無參數建構函數的平台實現。 不過，您可以將相依性解析方法插入 Xamarin.Forms，其會使用相依性插入容器或 Factory 方法來解析平台實作。 這種方法可用來解析具有參數建構函式的平台實作。 如需詳細資訊，請參閱 [Xamarin.Forms 中的相依性解析](~/xamarin-forms/internals/dependency-resolution.md)。

> [!IMPORTANT]
> 調用尚未向[`DependencyService`](xref:Xamarin.Forms.DependencyService)中註冊的平台實現將導致引發`NullReferenceException`。

### <a name="resolve-using-the-getlttgt-method"></a>使用 Get&lt;T&gt; 方法來解析

該方法[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)在運行時檢索`T`介面 的平台實現,並將其實例創建為單例。 此執行個體會在應用程式的存留期內存留，任何解析相同平台實作的後續呼叫，都會擷取相同執行個體。

以下代碼顯示了呼叫[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)方法`IDeviceOrientationService`解析 介面,然後呼`GetOrientation`叫其 方法的範例:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> 默認情況下[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*),該方法將創建介面`T`的平台實現實例。 不過，可以變更此行為。 如需詳細資訊，請參閱[管理已解析物件的存留期](#manage-the-lifetime-of-resolved-objects)。

### <a name="resolve-using-the-resolvelttgt-method"></a>使用 Resolve&lt;T&gt; 方法來解析

該方法[`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)使用注入到 Xamarin.Forms`T`[`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver)的依賴項解析方法在運行時檢索介面的平台實現。 如果依賴項解析方法尚未注入 Xamarin.Forms,`Resolve<T>`則該方法將回退到[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)調用 方法來檢索平台實現。 如需將相依性解析方法插入 Xamarin.Forms 的詳細資訊，請參閱 [Xamarin.Forms 中的相依性解析](~/xamarin-forms/internals/dependency-resolution.md)。

以下代碼顯示了呼叫[`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)方法`IDeviceOrientationService`解析 介面,然後呼`GetOrientation`叫其 方法的範例:

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

或者，此程式碼也可以壓縮成單一行：

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> 當[`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)該方法回退到調用方法[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)時 ,默認情況下,它`T`創建介面 的平台實現實例作為單個實例。 不過，可以變更此行為。 如需詳細資訊，請參閱[管理已解析物件的存留期](#manage-the-lifetime-of-resolved-objects)。

## <a name="manage-the-lifetime-of-resolved-objects"></a>管理已解析物件的存留期

類的[`DependencyService`](xref:Xamarin.Forms.DependencyService)默認行為是將平臺實現解析為單例。 因此，平台實作會在應用程式存留期內存留。

此行為使用和[`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget)[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)[`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)上的可選參數指定。 Entat[`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget)

- `GlobalInstance`，此成員會將平台實作以 Singleton 傳回。
- `NewInstance`，此成員會傳回平台實作的新執行個體。 平台執行個體的存留期接著會由應用程式負責管理。

和 方法都將其可選參數設置[`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget)為 ,因此平台實現始終以 singletons[`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)[`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*)身份解析。 可以更改此行為,以便透過將與[`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget)`Get<T>``Resolve<T>`方法指定為 參數來建立新的平台實現實例:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

在此範例中,為[`DependencyService`](xref:Xamarin.Forms.DependencyService)`ITextToSpeechService`介面創建平臺實現的新實例。 解析 `ITextToSpeechService` 的任何後續呼叫，都會建立新執行個體。

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
- [Xamarin.Forms 中的相依性解析](~/xamarin-forms/internals/dependency-resolution.md)

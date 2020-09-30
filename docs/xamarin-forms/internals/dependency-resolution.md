---
title: 中的相依性解析 Xamarin.Forms
description: 本文說明如何將相依性解析方法插入， Xamarin.Forms 讓應用程式的相依性插入容器能夠控制自訂轉譯器、效果和 DependencyService 執行的建立和存留期。
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6d4c9be2166881824e798e9cb801a5720ab55178
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557852"
---
# <a name="dependency-resolution-in-no-locxamarinforms"></a>中的相依性解析 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_本文說明如何將相依性解析方法插入， Xamarin.Forms 讓應用程式的相依性插入容器能夠控制自訂轉譯器、效果和 DependencyService 執行的建立和存留期。本文中的程式碼範例取自 [使用容器](/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo) 的相依性解析範例。_

在 Xamarin.Forms 使用模型視圖 ViewModel (MVVM) 模式的應用程式內容中，相依性插入容器可用來註冊和解析視圖模型，以及註冊服務並將其插入至 View 模型。 在建立模型的過程中，容器會插入所需的任何相依性。 如果尚未建立這些相依性，容器會先建立並解析相依性。 如需相依性插入的詳細資訊，包括將相依性插入視圖模型的範例，請參閱相依性 [插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

以傳統方式執行平臺專案中類型的建立和存留期控制 Xamarin.Forms ，這會使用 `Activator.CreateInstance` 方法來建立自訂轉譯器、效果和執行的實例 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 可惜的是，這會限制開發人員對於這些類型的建立和存留期的控制，以及將相依性插入這些類型的能力。 您可以藉由將相依性解析方法插入至 Xamarin.Forms 控制項來控制如何建立類型（由應用程式的相依性插入容器或）來變更此行為 Xamarin.Forms 。 不過請注意，不需要將相依性解析方法插入 Xamarin.Forms 。 Xamarin.Forms 如果未插入相依性解析方法，則會繼續建立及管理平臺專案中類型的存留期。

> [!NOTE]
> 雖然本文著重于將相依性解析方法插入，以使用相依性 Xamarin.Forms 插入容器來解析已註冊的型別，但是您也可以插入相依性解析方法，使用 factory 方法來解析已註冊的型別。 如需詳細資訊，請參閱 [使用 Factory 方法](/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo) 的相依性解析範例。

## <a name="injecting-a-dependency-resolution-method"></a>插入相依性解析方法

[`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver)類別可讓您 Xamarin.Forms 使用方法，將相依性解析方法插入到中 [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) 。 然後，當 Xamarin.Forms 需要特定型別的實例時，相依性解析方法會有機會提供實例。 如果相依性解析方法 `null` 針對要求的類型傳回，則會 Xamarin.Forms 切換回嘗試使用方法建立類型實例本身 `Activator.CreateInstance` 。

下列範例顯示如何使用方法來設定相依性解析方法 [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) ：

```csharp
using Autofac;
using Xamarin.Forms.Internals;
...

public partial class App : Application
{
    // IContainer and ContainerBuilder are provided by Autofac
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();

    public App()
    {
        ...
        DependencyResolver.ResolveUsing(type => container.IsRegistered(type) ? container.Resolve(type) : null);
        ...
    }
    ...
}
```

在此範例中，相依性解析方法會設定為使用 Autofac 相依性插入容器的 lambda 運算式，以解析已向容器註冊的任何類型。 否則， `null` 將會傳回，這會導致 Xamarin.Forms 嘗試解析型別。

> [!NOTE]
> 相依性插入容器所使用的 API 是容器特有的。 本文中的程式碼範例會使用 Autofac 做為相依性插入容器，以提供 `IContainer` 和 `ContainerBuilder` 類型。 替代的相依性插入容器可以平均使用，但會使用不同于此處所呈現的 Api。

請注意，在應用程式啟動期間，不需要設定相依性解析方法。 您可以隨時設定它。 唯一的條件約束是，在 Xamarin.Forms 應用程式嘗試取用儲存在相依性插入容器中的類型時，必須知道相依性解析方法的相關資訊。 因此，如果應用程式在啟動時將需要相依性插入容器中的服務，就必須在應用程式的生命週期早期設定相依性解析方法。 同樣地，如果相依性插入容器負責管理特定的建立和存留期 [`Effect`](xref:Xamarin.Forms.Effect) ， Xamarin.Forms 就必須知道相依性解析方法，才能嘗試建立使用該方法的視圖 `Effect` 。

> [!WARNING]
> 註冊和解析具有相依性插入容器的型別會有效能成本，因為容器使用反映來建立每種類型，尤其是在針對應用程式中的每個頁面導覽重建相依性時。 如果有許多或深度相依性，則建立的成本可能會大幅增加。

## <a name="registering-types"></a>註冊類型

類型必須先向相依性插入容器註冊，才能透過相依性解析方法加以解析。 下列程式碼範例顯示範例應用程式在 `App` 類別中針對 Autofac 容器公開的註冊方法：

```csharp
using Autofac;
using Autofac.Core;
...

public partial class App : Application
{
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();
    ...

    public static void RegisterType<T>() where T : class
    {
        builder.RegisterType<T>();
    }

    public static void RegisterType<TInterface, T>() where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>().As<TInterface>();
    }

    public static void RegisterTypeWithParameters<T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where T : class
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        });
    }

    public static void RegisterTypeWithParameters<TInterface, T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        }).As<TInterface>();
    }

    public static void BuildContainer()
    {
        container = builder.Build();
    }
    ...
}
```

當應用程式使用相依性解析方法來解析容器中的類型時，通常會從平臺專案執行類型註冊。 這可讓平臺專案註冊自訂轉譯器、效果和執行的類型 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。

從平臺專案註冊的型別， `IContainer` 必須建立物件（藉由呼叫方法來完成） `BuildContainer` 。 這個方法會在實例上叫用 Autofac 的 `Build` 方法 `ContainerBuilder` ，它會建立新的相依性插入容器，其中包含已進行的註冊。

在接下來的章節中， `Logger` 會將實介面的類別插入類別的函式中 `ILogger` 。 `Logger`類別會使用 `Debug.WriteLine` 方法來執行簡單的記錄功能，並用來示範如何將服務插入自訂轉譯器、效果和執行 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。

### <a name="registering-custom-renderers"></a>註冊自訂轉譯器

範例應用程式包含播放 web 影片的頁面，其 XAML 來源如下列範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

此 `VideoPlayer` 視圖會依類別在每個平臺上執行 `VideoPlayerRenderer` ，以提供播放影片的功能。 如需這些自訂轉譯器類別的詳細資訊，請參閱 [執行影片播放](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)程式。

在 iOS 和通用 Windows 平臺 (UWP) 上， `VideoPlayerRenderer` 類別具有下列需要引數的函式 `ILogger` ：

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平臺上，使用相依性插入容器的型別註冊是由 `RegisterTypes` 方法所執行，這是在平臺使用方法載入應用程式之前叫用的 `LoadApplication(new App())` 。 下列範例顯示 `RegisterTypes` iOS 平臺上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

在此範例中， `Logger` 實體型別是透過對應來註冊其介面型別，而 `VideoPlayerRenderer` 類型則直接註冊，而不需要介面對應。 當使用者流覽至包含該視圖的頁面時，將會叫用相依性 `VideoPlayer` 解析方法，以 `VideoPlayerRenderer` 從相依性插入容器解析型別，而這也會解析該型別並將其插入至函式 `Logger` `VideoPlayerRenderer` 。

`VideoPlayerRenderer`Android 平臺上的函式會稍微複雜一點，因為它 `Context` 除了引數之外還需要引數 `ILogger` ：

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

下列範例顯示 `RegisterTypes` Android 平臺上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

在此範例中， `App.RegisterTypeWithParameters` 方法會向相依性 `VideoPlayerRenderer` 插入容器註冊。 註冊方法可確保 `MainActivity` 實例會插入做為 `Context` 引數，而且該 `Logger` 型別將會插入做為 `ILogger` 引數。

### <a name="registering-effects"></a>註冊效果

範例應用程式包含使用觸控追蹤效果的頁面，可 [`BoxView`](xref:Xamarin.Forms.BoxView) 在頁面周圍拖曳實例。 [`Effect`](xref:Xamarin.Forms.Effect)會 `BoxView` 使用下列程式碼加入至：

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

`TouchEffect`類別是在 [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 每個平臺上由為的類別所執行的 `TouchEffect` `PlatformEffect` 。 Platform `TouchEffect` 類別提供在頁面周圍拖曳的功能 `BoxView` 。 如需這些效果類別的詳細資訊，請參閱 [從效果叫用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。

在所有平臺上， `TouchEffect` 類別都有下列需要引數的函式 `ILogger` ：

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平臺上，使用相依性插入容器的型別註冊是由 `RegisterTypes` 方法所執行，這是在平臺使用方法載入應用程式之前叫用的 `LoadApplication(new App())` 。 下列範例顯示 `RegisterTypes` Android 平臺上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

在此範例中， `Logger` 實體型別是透過對應來註冊其介面型別，而 `TouchEffect` 類型則直接註冊，而不需要介面對應。 當使用者流覽至包含附加至該實例的實例時，將會叫用相依性 [`BoxView`](xref:Xamarin.Forms.BoxView) `TouchEffect` 解析方法，以從相依性 `TouchEffect` 插入容器解析平臺類型，這也會解析並將 `Logger` 類型插入至 `TouchEffect` 函式。

### <a name="registering-dependencyservice-implementations"></a>註冊 DependencyService 的實施

範例應用程式包含一個頁面，該頁面會 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 在每個平臺上使用執行程式，以允許使用者從裝置的圖片庫挑選相片。 `IPhotoPicker`介面會定義由執行程式所執行的功能 `DependencyService` ，如下列範例所示：

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

在每個平臺專案中， `PhotoPicker` 類別會 `IPhotoPicker` 使用平臺 api 來實行介面。 如需有關這些相依性服務的詳細資訊，請參閱 [從圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)。

在 iOS 和 UWP 上， `PhotoPicker` 類別具有下列需要引數的函式 `ILogger` ：

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平臺上，使用相依性插入容器的型別註冊是由 `RegisterTypes` 方法所執行，這是在平臺使用方法載入應用程式之前叫用的 `LoadApplication(new App())` 。 下列範例顯示 `RegisterTypes` UWP 上的方法：

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

在此範例中， `Logger` 實體型別是透過對應來註冊其介面型別，而且 `PhotoPicker` 也會透過介面對應來註冊型別。

`PhotoPicker`Android 平臺上的函式會稍微複雜一點，因為它 `Context` 除了引數之外還需要引數 `ILogger` ：

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

下列範例顯示 `RegisterTypes` Android 平臺上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

在此範例中， `App.RegisterTypeWithParameters` 方法會向相依性 `PhotoPicker` 插入容器註冊。 註冊方法可確保 `MainActivity` 實例會插入做為 `Context` 引數，而且該 `Logger` 型別將會插入做為 `ILogger` 引數。

當使用者流覽至 [相片挑選] 頁面並選擇選取相片時， `OnSelectPhotoButtonClicked` 會執行處理常式：

```csharp
async void OnSelectPhotoButtonClicked(object sender, EventArgs e)
{
    ...
    var photoPickerService = DependencyService.Resolve<IPhotoPicker>();
    var stream = await photoPickerService.GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }
    ...
}
```

叫用方法時，將會叫用相依性 [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) 解析方法來解析相依性 `PhotoPicker` 插入容器中的型別，這也會解析該型別並將其插入至函式 `Logger` `PhotoPicker` 。

> [!NOTE]
> [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*)從應用程式的相依性插入容器解析型別時，必須使用方法 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。

## <a name="related-links"></a>相關連結

- [使用容器 (範例) 的相依性解析 ](/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [執行影片播放影片](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [從效果叫用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [從圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
---
title: 在 Xamarin.Forms 中的相依性解析
description: 這篇文章說明如何將 Xamarin.Forms 插入的相依性解析方法，使應用程式的相依性插入容器具有控制建立和自訂轉譯器、 效果及 DependencyService 實作的存留期。
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
ms.openlocfilehash: 6df393d59207cea9c316189059f8d0e08a5e5137
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290073"
---
# <a name="dependency-resolution-in-xamarinforms"></a>在 Xamarin.Forms 中的相依性解析

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_這篇文章說明如何將 Xamarin.Forms 插入的相依性解析方法，使應用程式的相依性插入容器具有控制建立和自訂轉譯器、 效果及 DependencyService 實作的存留期。這篇文章中的程式碼範例取自[使用容器的相依性解析](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)範例。_

在內容中使用的 Model View ViewModel (MVVM) 模式的 Xamarin.Forms 應用程式，來註冊及解析檢視模型，以及註冊服務和插入檢視模型可以使用相依性插入容器。 檢視模型在建立期間，容器會插入任何所需的相依性。 如果尚未建立這些相依性，會建立容器，並先解析相依性。 如需相依性插入，包括範例相依性插入檢視模型的詳細資訊，請參閱[相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

建立和 Xamarin.Forms，它會使用傳統上執行的平台專案中類型的存留期`Activator.CreateInstance`方法用來建立執行個體的自訂轉譯器的效果，並[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)實作。 不幸的是，這會限制開發人員控制建立和存留期的這些型別，並將它們插入的相依性的能力。 至 Xamarin.Forms 應用程式的相依性插入容器，或透過 Xamarin.Forms，控制如何將會建立類型 – 插入相依性解析方法，就可以變更此行為。 不過請注意沒有將 Xamarin.Forms 中插入相依性解析方法的需求。 Xamarin.Forms 會繼續建立和管理的平台專案中的型別存留期，如果不插入相依性解析方法。

> [!NOTE]
> 雖然這篇文章著重於解決使用相依性插入容器的已註冊的類型的 Xamarin.Forms 中插入相依性解析方法，您也可將使用 factory 方法來解析的相依性解析方法已註冊的型別。 如需詳細資訊，請參閱 <<c0> [ 使用 Factory 方法的相依性解析](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo)範例。

## <a name="injecting-a-dependency-resolution-method"></a>插入相依性解析方法

[ `DependencyResolver` ](xref:Xamarin.Forms.Internals.DependencyResolver)類別提供的功能，將相依性解析方法插入 Xamarin.Forms，使用[ `ResolveUsing` ](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*)方法。 然後，當 Xamarin.Forms 需要特定類型的執行個體時，相依性解析方法有機會提供執行個體。 如果相依性解析方法傳回`null`要求的型別，如 Xamarin.Forms 改回嘗試建立類型執行個體本身使用`Activator.CreateInstance`方法。

下列範例示範如何設定具有的相依性解析方法[ `ResolveUsing` ](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*)方法：

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

在此範例中，相依性解析方法設為 lambda 運算式來解決任何已註冊容器的類型使用 Autofac 相依性插入容器。 否則，`null`會傳回，從而導致 Xamarin.Forms 嘗試解析的型別。

> [!NOTE]
> 容器的特定相依性插入容器所使用的 API。 這篇文章中的程式碼範例會使用 Autofac 作為相依性插入容器，可提供`IContainer`和`ContainerBuilder`型別。 替代的相依性插入容器同樣無法使用，但會使用不同的 Api，比此處所提供。

請注意，不需要應用程式啟動期間設定相依性解析方法。 它可以在任何時間設定。 唯一的條件約束是 Xamarin.Forms 需要知道相依性解析方法，應用程式會嘗試使用類型儲存在相依性插入容器中的時間。 因此，如果應用程式將需要在啟動期間的相依性插入容器中沒有服務，相依性解析方法必須在應用程式的生命週期中及早設定。 同樣地，如果相依性插入容器管理建立的特定存留期[ `Effect` ](xref:Xamarin.Forms.Effect)，Xamarin.Forms 必須了解相依性解析方法，嘗試建立檢視之前，會使用該`Effect`。

> [!WARNING]
> 註冊和解析的相依性插入容器的型別具有效能的容器使用反映來建立每個類型，因為成本，尤其是相依性會為每個應用程式中的頁面巡覽重新建構。 如果有許多或深層相依性，可以大幅增加成本的建立。

## <a name="registering-types"></a>註冊類型

類型必須向相依性插入容器之前它可透過相依性解析方法加以解決。 下列程式碼範例顯示註冊方法的範例應用程式會顯示，在`App`Autofac 容器類別：

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

當應用程式會使用相依性解析方法，來解決從容器的類型時，型別註冊通常會執行從平台專案中。 這可讓平台專案中自訂轉譯器的效果，註冊類型以及[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)實作。

從平台專案中，下列型別註冊`IContainer`物件必須先建置，這透過呼叫`BuildContainer`方法。 這個方法會叫用的 Autofac`Build`方法`ContainerBuilder`執行個體，建立新的相依性插入容器，其中包含已註冊。

下列各節，在`Logger`可實作類別`ILogger`介面插入類別建構函式。 `Logger`類別會實作簡單的記錄功能使用`Debug.WriteLine`方法，以及用來示範服務如何插入自訂轉譯器，效果並[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)實作。

### <a name="registering-custom-renderers"></a>註冊自訂轉譯器

範例應用程式包含播放網頁視訊，下列範例所示的 XAML 來源的頁面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

`VideoPlayer`檢視由每個平台上實作`VideoPlayerRenderer`類別，可播放視訊時，提供的功能。 如需有關這些自訂轉譯器類別的詳細資訊，請參閱 <<c0> [ 實作視訊播放程式](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)。

在 iOS 和通用 Windows 平台 (UWP) 上`VideoPlayerRenderer`類別擁有下列建構函式，這需要`ILogger`引數：

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平台，以執行與相依性插入容器的型別註冊`RegisterTypes`方法之前載入的應用程式的平台叫用`LoadApplication(new App())`方法。 下列範例所示`RegisterTypes`iOS 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

在此範例中，`Logger`具象型別透過其介面型別中，針對對應註冊和`VideoPlayerRenderer`的介面對應不直接註冊型別。 當使用者導覽到包含的頁面`VideoPlayer`檢視中，會叫用相依性解析方法，來解決`VideoPlayerRenderer`類型從相依性插入容器，這也會解決並插入`Logger`輸入`VideoPlayerRenderer`建構函式。

`VideoPlayerRenderer`建構函式的 Android 平台而言稍嫌複雜，因為它需要`Context`引數，除了`ILogger`引數：

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

下列範例所示`RegisterTypes`Android 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

在此範例中，`App.RegisterTypeWithParameters`方法會註冊`VideoPlayerRenderer`與相依性插入容器。 註冊方法可確保`MainActivity`執行個體將會插入做為`Context`引數，且`Logger`型別將會插入做為`ILogger`引數。

### <a name="registering-effects"></a>註冊效果

範例應用程式包含將使用觸控追蹤效果的頁面[ `BoxView` ](xref:Xamarin.Forms.BoxView)周圍頁面的執行個體。 [ `Effect` ](xref:Xamarin.Forms.Effect)新增至`BoxView`使用下列程式碼：

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

`TouchEffect`類別是[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)的每個平台上實作`TouchEffect`類別，具有`PlatformEffect`。 平台`TouchEffect`類別提供的拖曳功能`BoxView`周圍的頁面。 如需有關這些效果類別的詳細資訊，請參閱[叫用事件效果](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。

在所有平台，`TouchEffect`類別具有下列建構函式，這需要`ILogger`引數：

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平台，以執行與相依性插入容器的型別註冊`RegisterTypes`方法之前載入的應用程式的平台叫用`LoadApplication(new App())`方法。 下列範例所示`RegisterTypes`Android 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

在此範例中，`Logger`具象型別透過其介面型別中，針對對應註冊和`TouchEffect`的介面對應不直接註冊型別。 當使用者導覽到包含的頁面[ `BoxView` ](xref:Xamarin.Forms.BoxView)具有執行個體`TouchEffect`附加到它，相依性解析方法也會叫用解決平台`TouchEffect`類型從相依性插入容器，這也會解決並插入`Logger`輸入`TouchEffect`建構函式。

### <a name="registering-dependencyservice-implementations"></a>註冊 DependencyService 實作

範例應用程式包含使用的網頁[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)上每個平台，以允許使用者從裝置的圖片庫中挑選相片的實作。 `IPhotoPicker`介面會定義所實作的功能`DependencyService`實作中，與下列範例所示：

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

在每個平台專案中，`PhotoPicker`類別會實作`IPhotoPicker`使用平台 Api 介面。 如需有關這些相依性服務的詳細資訊，請參閱 <<c0> [ 挑選相片圖片庫從](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)。

在 iOS 和 UWP`PhotoPicker`類別擁有下列建構函式，這需要`ILogger`引數：

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

在所有平台，以執行與相依性插入容器的型別註冊`RegisterTypes`方法之前載入的應用程式的平台叫用`LoadApplication(new App())`方法。 下列範例所示`RegisterTypes`UWP 上的方法：

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

在此範例中，`Logger`具象型別透過其介面型別中，針對對應註冊和`PhotoPicker`透過的介面對應，也會登錄型別。

`PhotoPicker`建構函式的 Android 平台而言稍嫌複雜，因為它需要`Context`引數，除了`ILogger`引數：

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

下列範例所示`RegisterTypes`Android 平台上的方法：

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

在此範例中，`App.RegisterTypeWithParameters`方法會註冊`PhotoPicker`與相依性插入容器。 註冊方法可確保`MainActivity`執行個體將會插入做為`Context`引數，且`Logger`型別將會插入做為`ILogger`引數。

當使用者瀏覽至相片挑選頁面，並選擇要選取一張照片，`OnSelectPhotoButtonClicked`處理常式會執行：

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

當[ `DependencyService.Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*)叫用方法時，會叫用來解析的相依性解析方法`PhotoPicker`類型從相依性插入容器，這也會解決並插入`Logger`類型到`PhotoPicker`建構函式。

> [!NOTE]
> [ `Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*)解析來自透過應用程式的相依性插入容器的型別時，就必須使用方法[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)。

## <a name="related-links"></a>相關連結

- [使用容器 （範例） 的相依性解析](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [實作影片播放程式](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [叫用事件的效果](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [從 圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)

---
標題：「iOS 上的主要執行緒控制更新」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，讓控制項配置和轉譯更新在主執行緒上執行。」
assetid： 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/24/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="main-thread-control-updates-on-ios"></a>IOS 上的主要執行緒控制項更新

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定可讓控制項版面配置和轉譯更新在主執行緒上執行，而不是在背景執行緒上執行。 這應該很少需要，但在某些情況下，可能會導致損毀。 透過將可系結屬性設定為，在 XAML 中使用它 `Application.HandleControlUpdatesOnMainThread` `true` ：

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

`Application.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `Application.SetHandleControlUpdatesOnMainThread`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制是否在主執行緒上執行控制項版面配置和轉譯更新，而不是在背景執行緒上執行。 此外， `Application.GetHandleControlUpdatesOnMainThread` 方法可以用來傳回是否在主執行緒上執行控制項版面配置和轉譯更新。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

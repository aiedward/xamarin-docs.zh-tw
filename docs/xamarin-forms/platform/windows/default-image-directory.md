---
標題：「Windows 上的預設影像目錄」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的來定義專案中將從中載入影像資產的目錄。」
assetid： 537A032B-74DD-4D43-864E-7D7113286D0D ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：01/16/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="default-image-directory-on-windows"></a>Windows 上的預設映射目錄

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此通用 Windows 平臺平臺特定會定義專案中將從中載入影像資產的目錄。 它是在 XAML 中使用，方法是將設定 `Application.ImageDirectory` 為 `string` ，其代表包含影像資產的專案目錄：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
             ...
             windows:Application.ImageDirectory="Assets">
    ...
</Application>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
Application.Current.On<Windows>().SetImageDirectory("Assets");
```

`Application.On<Windows>`方法會指定此平臺特定只會在通用 Windows 平臺上執行。 `Application.SetImageDirectory`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 用來指定要從中載入影像的專案目錄。 此外， `GetImageDirectory` 方法可以用來傳回 `string` ，其代表包含應用程式映射資產的專案目錄。

結果是應用程式中使用的所有映射都會從指定的專案目錄載入。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)

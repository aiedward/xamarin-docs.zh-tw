---
標題：「iOS 上命名的字型大小的協助工具調整」描述：「平臺詳細資訊可讓您使用僅適用于特定平臺的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來停用已命名字型大小的協助工具縮放。
assetid： B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：06/28/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>IOS 上已命名字型大小的協助工具調整

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會停用所命名字型大小的協助工具調整。 將可系結屬性設定為，即可在 XAML 中使用它 `Application.EnableAccessibilityScalingForNamedFontSizes` `false` ：

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

`Application.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `Application.SetEnableAccessibilityScalingForNamedFontSizes`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來停用 iOS 協助工具設定所縮放的命名字型大小。 此外， `Application.GetEnableAccessibilityScalingForNamedFontSizes` 方法可以用來傳回已命名的字型大小是否由 iOS 協助工具設定縮放。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

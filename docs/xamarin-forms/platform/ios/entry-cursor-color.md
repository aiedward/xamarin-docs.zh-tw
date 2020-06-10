---
title： "iOS 上的輸入資料指標色彩" 描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來設定專案的游標色彩。」
assetid： 867D70BA-53F9-4434-8094-85D71DCECC2D ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/24/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="entry-cursor-color-on-ios"></a>IOS 上的專案游標色彩

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會將的游標色彩設 [`Entry`](xref:Xamarin.Forms.Entry) 為指定的色彩。 將可系結屬性設定為，即可在 XAML 中使用它 [`Entry.CursorColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty) [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

`Entry.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `Entry.SetCursorColor` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetCursorColor （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。Entry}、 Xamarin.Forms 。Color））方法，在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中，將游標色彩設為指定的 [`Color`](xref:Xamarin.Forms.Color) 。 此外，[ `Entry.GetCursorColor` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. GetCursorColor （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。Entry}））方法可以用來抓取目前的資料指標色彩。

結果是，中的游標色彩 [`Entry`](xref:Xamarin.Forms.Entry) 可以設定為特定的 [`Color`](xref:Xamarin.Forms.Color) ：

![](entry-cursor-color-images/entry-cursorcolor.png "Entry Cursor Color")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

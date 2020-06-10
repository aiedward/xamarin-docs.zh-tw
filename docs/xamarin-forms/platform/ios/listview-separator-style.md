---
title： "iOS 上的 ListView 分隔符號樣式" 描述： "平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，控制 ListView 中的資料格之間的分隔符號是否使用 ListView 的完整寬度。」
assetid： A4CB45CE-9FB7-47ED-8C3D-93E39BF282E4 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：10/24/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="listview-separator-style-on-ios"></a>IOS 上的 ListView 分隔符號樣式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺專屬控制中的資料格之間的分隔符號是否 [`ListView`](xref:Xamarin.Forms.ListView) 使用的完整寬度 `ListView` 。 它會在 XAML 中使用，方法是將 [`ListView.SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) 附加屬性設為列舉的值 [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

`ListView.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `ListView.SetSeparatorStyle` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetSeparatorStyle （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。ListView}、 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SeparatorStyle））方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來控制中的資料格之間的分隔符號是否 [`ListView`](xref:Xamarin.Forms.ListView) 使用的完整寬度 `ListView` ，且 [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) 列舉提供兩個可能的值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default)–表示預設的 iOS 分隔符號行為。 這是中的預設行為 Xamarin.Forms 。
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth)–表示將從的某個邊緣繪製到另一個的分隔符號 `ListView` 。

結果是指定的 [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) 值會套用至 [`ListView`](xref:Xamarin.Forms.ListView) ，以控制資料格之間的分隔符號寬度：

![](listview-separator-style-images/listview-separatorstyle.png "ListView SeparatorStyle Platform-Specific")

> [!NOTE]
> 當分隔符號樣式設定為之後 `FullWidth` ，就無法 `Default` 在執行時間將其變更回。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

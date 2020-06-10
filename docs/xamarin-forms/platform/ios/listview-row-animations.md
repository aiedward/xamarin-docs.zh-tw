---
title： "iOS 上的 ListView 資料列動畫" 描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，控制是否要在更新 ListView 專案集合時停用資料列動畫。」
assetid： E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：02/21/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="listview-row-animations-on-ios"></a>IOS 上的 ListView 資料列動畫

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺專屬的控制項會在專案集合更新時，是否要停用資料列動畫 [`ListView`](xref:Xamarin.Forms.ListView) 。 將可系結屬性設定為，即可在 XAML 中使用它 `ListView.RowAnimationsEnabled` `false` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
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

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

`ListView.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `ListView.SetRowAnimationsEnabled`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可用來控制更新專案集合時，是否要停用資料列動畫 [`ListView`](xref:Xamarin.Forms.ListView) 。 此外， `ListView.GetRowAnimationsEnabled` 方法可以用來傳回上的資料列動畫是否停用 `ListView` 。

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView)預設會啟用資料列動畫。 因此，在中插入新的資料列時，就會發生動畫 `ListView` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

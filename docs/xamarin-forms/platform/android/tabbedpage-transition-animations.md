---
title：「Android 上的 TabbedPage 頁面轉換動畫」描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，在流覽 TabbedPage 中的頁面時停用轉換動畫。」
assetid： 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. date： 07/10/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="tabbedpage-page-transition-animations-on-android"></a>Android 上的 TabbedPage 頁面轉換動畫

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當您以程式設計方式或在中使用索引標籤列流覽頁面時，會使用此 Android 平臺特定來停用轉換動畫 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 將可系結屬性設定為，即可在 XAML 中使用它 `TabbedPage.IsSmoothScrollEnabled` `false` ：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

`TabbedPage.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 `TabbedPage.SetIsSmoothScrollEnabled`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 可用來控制在的頁面之間流覽時，是否會顯示轉換動畫 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 此外， `TabbedPage` 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` 也具有下列方法：

- `IsSmoothScrollEnabled`，用來抓取在中的頁面之間流覽時，是否會顯示轉換動畫 `TabbedPage` 。
- `EnableSmoothScroll`，在中的頁面之間流覽時，用來啟用轉換動畫 `TabbedPage` 。
- `DisableSmoothScroll`，在中的頁面之間流覽時，用來停用轉換動畫 `TabbedPage` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)

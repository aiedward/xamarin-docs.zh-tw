---
title： "iOS 上的搜尋列樣式" 描述：「平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來控制搜尋列是否有背景。」
assetid： 3D512DD6-078E-4BC6-926E-62BA6F4DE640 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：03/05/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="searchbar-style-on-ios"></a>IOS 上的搜尋列樣式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺專屬控制項是否 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 有背景。 它會在 XAML 中使用，方法是將可系結 `SearchBar.SearchBarStyle` 屬性設定為列舉的值 `UISearchBarStyle` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ios:SearchBar.SearchBarStyle="Minimal"
                   Placeholder="Enter search term" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SearchBar searchBar = new SearchBar { Placeholder = "Enter search term" };
searchBar.On<iOS>().SetSearchBarStyle(UISearchBarStyle.Minimal);
```

`SearchBar.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `SearchBar.SetSearchBarStyle`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制是否 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 有背景。 `UISearchBarStyle`列舉提供三個可能的值：

- `Default`表示 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 具有預設樣式。 這是可系結屬性的預設值 `SearchBar.SearchBarStyle` 。
- `Prominent`表示 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 具有半透明的背景，而且搜尋欄位是不透明的。
- `Minimal`表示沒有 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 背景，而且搜尋欄位是透明的。

此外， `SearchBar.GetSearchBarStyle` 方法可以用來傳回套用 `UISearchBarStyle` 至的 `SearchBar` 。

結果是將指定的 `UISearchBarStyle` 成員套用至 [`SearchBar`](xref:Xamarin.Forms.SearchBar) ，以控制是否 `SearchBar` 具有背景：

![IOS 上搜尋列樣式的螢幕擷取畫面](searchbar-style-images/searchbar-styles.png "IOS 上的搜尋列樣式")

下列螢幕擷取畫面顯示套用 `UISearchBarStyle` 至 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 已設定屬性之物件的成員 `BackgroundColor` ：

![IOS 上具有背景色彩之搜尋列樣式的螢幕擷取畫面](searchbar-style-images/searchbar-background-styles.png "IOS 上具有背景色彩的搜尋列樣式")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

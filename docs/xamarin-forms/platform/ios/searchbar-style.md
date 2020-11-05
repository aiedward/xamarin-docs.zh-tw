---
title: IOS 上的 SearchBar 樣式
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，來控制 SearchBar 是否有背景。
ms.prod: xamarin
ms.assetid: 3D512DD6-078E-4BC6-926E-62BA6F4DE640
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9cb48c5ddca6f5e6f6aeba76d27d9cd2c95dd450
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373675"
---
# <a name="searchbar-style-on-ios"></a>IOS 上的 SearchBar 樣式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定的控制是否 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 有背景。 它是在 XAML 中使用，方法是將可系結 `SearchBar.SearchBarStyle` 屬性設定為 `UISearchBarStyle` 列舉值：

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

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SearchBar searchBar = new SearchBar { Placeholder = "Enter search term" };
searchBar.On<iOS>().SetSearchBarStyle(UISearchBarStyle.Minimal);
```

`SearchBar.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 在 `SearchBar.SetSearchBarStyle` 命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制是否 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 有背景。 `UISearchBarStyle`列舉提供三個可能的值：

- `Default` 表示 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 具有預設樣式。 這是可系結屬性的預設值 `SearchBar.SearchBarStyle` 。
- `Prominent` 指出 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 具有半透明背景，而且搜尋欄位是不透明的。
- `Minimal` 指出沒有 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 背景，而且搜尋欄位是透明的。

此外， `SearchBar.GetSearchBarStyle` 方法可以用來傳回套用 `UISearchBarStyle` 至的 `SearchBar` 。

結果是將指定的 `UISearchBarStyle` 成員套用至 [`SearchBar`](xref:Xamarin.Forms.SearchBar) ，以控制是否 `SearchBar` 有背景：

![IOS 上 SearchBar 樣式的螢幕擷取畫面](searchbar-style-images/searchbar-styles.png "IOS 上的 SearchBar 樣式")

下列螢幕擷取畫面顯示套用 `UISearchBarStyle` 至 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 已設定屬性之物件的成員 `BackgroundColor` ：

![在 iOS 上使用背景色彩 SearchBar 樣式的螢幕擷取畫面](searchbar-style-images/searchbar-background-styles.png "在 iOS 上使用背景色彩 SearchBar 樣式")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
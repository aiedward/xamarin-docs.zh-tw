---
title: " SearchBar Windows 上的拼寫檢查"
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，讓 SearchBar 與拼寫檢查引擎互動。
ms.prod: xamarin
ms.assetid: 7974C91F-7502-4DB3-B0E9-C45E943DDA26
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b97ce2015ba632d100b946a5b287f0eae737123c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368683"
---
# <a name="searchbar-spell-check-on-windows"></a>SearchBar Windows 上的拼寫檢查

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這通用 Windows 平臺平臺特定的可讓與 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 拼寫檢查引擎進行互動。 它是在 XAML 中使用，方法是將 [`SearchBar.IsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

`SearchBar.On<Windows>`方法指定此平臺特定只會在通用 Windows 平臺上執行。 [ `SearchBar.SetIsSpellCheckEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. SearchBar. SetIsSpellCheckEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。SearchBar}、system.string) # A3 方法，在 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空間中，開啟和關閉拼寫檢查。 此外， `SearchBar.SetIsSpellCheckEnabled` 方法可以藉由呼叫 [ `SearchBar.GetIsSpellCheckEnabled` ] (x：來切換拼寫檢查 Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. SearchBar. GetIsSpellCheckEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。SearchBar} ) # A3 方法，以傳回是否啟用拼寫檢查：

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

結果是，輸入的文字可以經過 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 拼寫檢查，並對使用者指出不正確的拼寫：

![SearchBar 拼寫檢查平臺特定](searchbar-spell-check-images/searchbar-spellcheck.png "SearchBar 拼寫檢查平臺特定")

> [!NOTE]
> `SearchBar`命名空間中的類別 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 也有 [`EnableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) 和 [`DisableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) 可用來分別在上啟用和停用拼寫檢查的方法 `SearchBar` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
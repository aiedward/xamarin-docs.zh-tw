---
title: IOS 上的大型頁面標題
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，將頁面標題顯示為 NavigationPage 巡覽列上的大型標題。
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 162683c4fabb0a8b6deed1fb30bd7a7dece1f597
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562284"
---
# <a name="large-page-titles-on-ios"></a>IOS 上的大型頁面標題

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)針對使用 ios 11 或更新版本的裝置，此 iOS 平臺特定可用來將頁面標題顯示為巡覽列上的大型標題。 大型標題會靠左對齊，並使用較大的字型，並在使用者開始滾動內容時轉換成標準標題，以便有效率地使用螢幕房地產。 不過，以橫向的方向，標題會返回導覽列的中央，以將內容配置優化。 它是在 XAML 中使用，方法是將 `NavigationPage.PrefersLargeTitles` 附加屬性設定為 `boolean` 值：

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

或者，您也可以使用流暢的 API 從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

`NavigationPage.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `NavigationPage.SetPrefersLargeTitle`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 會控制是否已啟用大型標題。

如果已在上啟用大型標題 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，則導覽堆疊中的所有頁面都會顯示大型標題。 您可以藉由將 `Page.LargeTitleDisplay` 附加屬性設定為列舉的值，在頁面上覆寫此行為 `LargeTitleDisplayMode` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

或者，您可以使用流暢的 API，從 c # 覆寫頁面行為：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

`Page.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `Page.SetLargeTitleDisplay`命名空間中的方法會 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 控制上的大型標題列為 [`Page`](xref:Xamarin.Forms.Page) ，並 `LargeTitleDisplayMode` 提供三個可能的值給列舉：

- `Always` –強制使用大型格式的導覽列和字型大小。
- `Automatic` –使用相同的樣式 (大型或小型) 作為導覽堆疊中的上一個專案。
- `Never` –強制使用一般的小型格式導覽列。

此外， `SetLargeTitleDisplay` 方法可以藉由呼叫 `LargeTitleDisplay` 方法（傳回目前的）來切換列舉值 `LargeTitleDisplayMode` ：

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

結果會將指定的套用 `LargeTitleDisplayMode` 至 [`Page`](xref:Xamarin.Forms.Page) ，以控制大型標題列為：

![模糊效果平臺特定](page-large-title-images/large-title.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
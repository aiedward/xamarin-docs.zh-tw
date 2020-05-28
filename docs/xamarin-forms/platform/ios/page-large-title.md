---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0db20620870340386ccd0cedf7f98cb2975527ba
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128028"
---
# <a name="large-page-titles-on-ios"></a>IOS 上的大型頁面標題

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定是用來在的導覽列上，將頁面標題顯示為大型標題 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，適用于使用 iOS 11 或更新版本的裝置。 較大的標題會靠左對齊，並使用較大的字型，並在使用者開始滾動內容時轉換成標準標題，以便有效率地使用畫面的房地產。 不過，在橫向方向，標題會回到導覽列的中央，以優化內容配置。 它會在 XAML 中使用，方法是將 `NavigationPage.PrefersLargeTitles` 附加屬性設定為 `boolean` 值：

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

或者，也可以使用 Fluent API 從 c # 取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

`NavigationPage.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `NavigationPage.SetPrefersLargeTitle`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 會控制是否要啟用大型標題。

如果已在上啟用大型標題 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，則導覽堆疊中的所有頁面都會顯示大型標題。 將 `Page.LargeTitleDisplay` 附加屬性設為列舉的值，即可在頁面上覆寫這個行為 `LargeTitleDisplayMode` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

或者，您可以使用 Fluent API，從 c # 覆寫頁面行為：

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

`Page.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `Page.SetLargeTitleDisplay`命名空間中的方法會 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 控制上的大型標題列為，而 [`Page`](xref:Xamarin.Forms.Page) 列舉會 `LargeTitleDisplayMode` 提供三個可能的值：

- `Always`-強制導覽列和字型大小使用大型格式。
- `Automatic`–使用與導覽堆疊中的上一個專案相同的樣式（大型或小型）。
- `Never`-強制使用一般、小型的格式導覽列。

此外， `SetLargeTitleDisplay` 方法可以藉由呼叫方法來切換列舉值，這會傳回 `LargeTitleDisplay` 目前的 `LargeTitleDisplayMode` ：

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

結果是指定的會套用 `LargeTitleDisplayMode` 至 [`Page`](xref:Xamarin.Forms.Page) ，以控制大型標題列為：

![](page-large-title-images/large-title.png "Blur Effect Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

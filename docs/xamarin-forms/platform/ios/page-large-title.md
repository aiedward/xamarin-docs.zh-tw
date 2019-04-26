---
title: 在 iOS 上的大型頁面標題
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台專屬 NavigationPage 的巡覽列上的大型標題以顯示頁面標題。
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ad1c7f94467c6f32b9108ab7f6abe85d31679d3a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952114"
---
# <a name="large-page-titles-on-ios"></a>在 iOS 上的大型頁面標題

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 iOS 平台專屬用來顯示網頁的標題為的巡覽列上的大型標題[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，適用於使用 iOS 11 或更新版本的裝置。 大型標題靠左對齊，而使用較大的字型，且會轉換成標準的標題使用者一開始捲動的內容，以便有效率地使用螢幕面積。 但是，在橫向模式下，標題將返回到導航欄的中心以優化內容佈局。 它由在 XAML 中設定`NavigationPage.PrefersLargeTitles`附加屬性`boolean`值：

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

或者可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `NavigationPage.SetPrefersLargeTitle`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可讓您控制是否要啟用大型標題。

前提是大型的項目上啟用[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，在巡覽堆疊中的所有頁面會都顯示大型的標題。 此行為可覆寫頁面上設定`Page.LargeTitleDisplay`附加屬性的值`LargeTitleDisplayMode`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

從 C# 使用 fluent API 或者，覆寫頁面行為：

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

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetLargeTitleDisplay`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間上控制項的大型標題行為[ `Page` ](xref:Xamarin.Forms.Page)，與`LargeTitleDisplayMode`提供三個可能的列舉型別值：

- `Always` – 強制字型與導覽列中使用大型的格式大小。
- `Automatic` – 為上一個項目，在巡覽堆疊中使用相同的樣式 （大型或小型）。
- `Never` -強制執行規則、 小型格式導覽列的使用。

颾魤 ㄛ`SetLargeTitleDisplay`方法可用來切換列舉值，藉由呼叫`LargeTitleDisplay`方法，以傳回目前`LargeTitleDisplayMode`:

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

結果是，指定`LargeTitleDisplayMode`套用至[ `Page` ](xref:Xamarin.Forms.Page)，大型標題行為：

![](page-large-title-images/large-title.png "模糊效果平台專屬")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

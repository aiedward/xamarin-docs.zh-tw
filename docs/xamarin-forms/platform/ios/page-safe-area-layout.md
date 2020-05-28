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
ms.openlocfilehash: 5ca30481fbc0e5631ff75000c688dd805793e670
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128044"
---
# <a name="safe-area-layout-guide-on-ios"></a>IOS 上的安全區域版面配置指南

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定的是用來確保頁面內容位於螢幕區域上，對於使用 iOS 11 和更新版本的所有裝置而言都是安全的。 具體來說，它有助於確保內容不會被圓角的裝置角落、主指示器或 iPhone X 上的感應器機架裁剪。它會在 XAML 中使用，方法是將 `Page.UseSafeArea` 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

`Page.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `Page.SetUseSafeArea`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可控制是否啟用安全區域版面配置輔助線。

結果是頁面內容可以放在螢幕上所有 Iphone 的安全區域中：

[![](page-safe-area-images/safe-area-layout.png "Safe Area Layout Guide")](page-safe-area-images/safe-area-layout-large.png#lightbox "Safe Area Layout Guide")

> [!NOTE]
> Apple 定義的安全區域用 Xamarin.Forms 來設定 [`Page.Padding`](xref:Xamarin.Forms.Page.Padding) 屬性，而且會覆寫這個屬性已設定的任何先前的值。

您可以 [`Thickness`](xref:Xamarin.Forms.Thickness) 使用 `Page.SafeAreaInsets` 命名空間中的方法，來自訂 safe 區域 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 。 然後，您可以視需要修改它，然後重新指派至 `Padding` 頁面的「參數」中的屬性，或覆 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 寫：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

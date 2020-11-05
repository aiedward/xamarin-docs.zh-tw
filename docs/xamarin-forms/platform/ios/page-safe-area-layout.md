---
title: IOS 上的安全區域版面配置指南
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，確保頁面內容位於螢幕的某個區域，該區域對使用 iOS 11 和更新版本的所有裝置都是安全的。
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f5907e5493ff43c6a69dc4a8a8e5d2b4fc78210e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374871"
---
# <a name="safe-area-layout-guide-on-ios"></a>IOS 上的安全區域版面配置指南

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定是用來確保頁面內容位於螢幕上的某個區域，該區域對使用 iOS 11 和更新版本的所有裝置都是安全的。 具體來說，它有助於確保不會將內容由圓角、主指示器或 iPhone X 上的感應器機架裁剪。它是在 XAML 中使用，方法是將 `Page.UseSafeArea` 附加屬性設定為 `boolean` 值：

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

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

`Page.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `Page.SetUseSafeArea`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 會控制是否已啟用安全區域版面配置指南。

結果是，頁面內容可以位於螢幕上可安全 Iphone 的區域：

[![安全區域版面配置輔助線](page-safe-area-images/safe-area-layout.png)](page-safe-area-images/safe-area-layout-large.png#lightbox "安全區域版面配置輔助線")

> [!NOTE]
> Apple 所定義的安全區域是用 Xamarin.Forms 來設定 [`Page.Padding`](xref:Xamarin.Forms.Page.Padding) 屬性，而且會覆寫這個屬性先前設定的任何先前值。

您可以 [`Thickness`](xref:Xamarin.Forms.Thickness) 使用 `Page.SafeAreaInsets` 命名空間中的方法，來自訂安全區域 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 。 然後，您可以視需要修改它，並將其重新指派給覆 `Padding` 寫中的屬性 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) ：

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

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
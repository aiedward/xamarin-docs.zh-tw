---
title: IOS 上的安全區域版面配置指南
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 iOS 平臺專屬的, 以確保頁面內容位於螢幕上的某個區域, 而這些裝置在使用 iOS 11 和更新版本的所有裝置上都是安全的。
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: c6a2ec5a4d1466b7118e6cc7b03cc5518b27e2fb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644535"
---
# <a name="safe-area-layout-guide-on-ios"></a>IOS 上的安全區域版面配置指南

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定的是用來確保頁面內容位於螢幕區域上, 對於使用 iOS 11 和更新版本的所有裝置而言都是安全的。 具體來說，它會幫助，以確定該內容不由裝置圓角邊角、 家用的指標或在 iPhone X 上的感應器外罩裁剪。它由在 XAML 中設定`Page.UseSafeArea`附加屬性`boolean`值：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetUseSafeArea`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可讓您控制是否要啟用安全區域版面配置輔助線。

結果是螢幕的網頁內容可以位於安全適用於所有 Iphone 區域：

[![](page-safe-area-images/safe-area-layout.png "安全區域版面配置輔助線")](page-safe-area-images/safe-area-layout-large.png#lightbox "安全區域版面配置輔助線")

> [!NOTE]
> Apple 所定義的安全區域設定時，會在 Xamarin.Forms [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding)屬性，而且會覆寫這個屬性的所有已設定的舊值。

安全區域可以藉由擷取自訂其[ `Thickness` ](xref:Xamarin.Forms.Thickness)具有值`Page.SafeAreaInsets`方法，從[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間。 然後可以修改為所需和重新指派給`Padding`頁面的建構函式中的屬性或[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)覆寫：

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

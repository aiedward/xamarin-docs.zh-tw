---
title: 在 iOS 上 TabbedPage 半透明索引標籤列
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來設定 TabbedPage 上索引標籤列的半透明度模式。
ms.prod: xamarin
ms.assetid: 9581AE81-9557-47AD-8B07-25A1AC5F055B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 55fda0be2e260c5aa4a34ab2dcc1ac3cac33b92a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070295"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>在 iOS 上 TabbedPage 半透明索引標籤列

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定是用來設定[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)上索引標籤列的半透明度模式。 它會在 XAML 中使用，方法是將 `TabbedPage.TranslucencyMode` 可系結屬性設定為 `TranslucencyMode` 列舉值：

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

或者，也可以C#使用 Fluent API 來取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

`TabbedPage.On<iOS>` 方法指定此平臺特定的只會在 iOS 上執行。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間中的 `TabbedPage.SetTranslucencyMode` 方法，會藉由指定下列其中一個 `TranslucencyMode` 列舉值，來設定[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)上索引標籤列的半透明度模式：

- `Default`，將索引標籤列設定為預設半透明度模式。 此為 `TabbedPage.TranslucencyMode` 屬性的預設值。
- `Translucent`，將索引標籤列設定為半透明。
- `Opaque`，將索引標籤列設定為不透明。

此外，`GetTranslucencyMode` 方法可以用來抓取套用至[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)之 `TranslucencyMode` 列舉的目前值。

結果是可以設定[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)上索引標籤列的 [半透明度] 模式：

![IOS 上半透明和不透明索引標籤欄的螢幕擷取畫面](tabbedpage-translucent-tabbar-images/translucencymodes.png "半透明和不透明的索引標籤列")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

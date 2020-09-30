---
title: 在 iOS 上 TabbedPage 半透明索引標籤列
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，以在 TabbedPage 上設定索引標籤列的半透明度模式。
ms.prod: xamarin
ms.assetid: 9581AE81-9557-47AD-8B07-25A1AC5F055B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bfd581eebba259ecf7e4e9a426f7382c8a3d1d5a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562544"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>在 iOS 上 TabbedPage 半透明索引標籤列

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定用來設定上索引標籤列的半透明度模式 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 它是在 XAML 中使用，方法是將可系結 `TabbedPage.TranslucencyMode` 屬性設定為 `TranslucencyMode` 列舉值：

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

`TabbedPage.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `TabbedPage.SetTranslucencyMode`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來藉 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 由指定下列其中一個列舉值，來設定上索引標籤列的半透明度模式 `TranslucencyMode` ：

- `Default`，將索引標籤列設定為預設的半透明度模式。 此為 `TabbedPage.TranslucencyMode` 屬性的預設值。
- `Translucent`，此選項會將索引標籤列設定為半透明。
- `Opaque`，將索引標籤列設定為不透明。

此外， `GetTranslucencyMode` 方法可以用來取得套用至之列舉的目前值 `TranslucencyMode` [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。

結果是可以設定上索引標籤列的半透明度模式 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ：

![IOS 上半透明和不透明索引標籤列的螢幕擷取畫面](tabbedpage-translucent-tabbar-images/translucencymodes.png "半透明和不透明的索引標籤列")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
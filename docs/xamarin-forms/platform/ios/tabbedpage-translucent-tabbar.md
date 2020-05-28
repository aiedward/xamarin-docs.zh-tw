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
ms.openlocfilehash: 8127191aab80d81fc2e532e3d5e14931b834eeae
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137029"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>在 iOS 上 TabbedPage 半透明索引標籤列

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定是用來設定上索引標籤欄的半透明度模式 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 它會在 XAML 中使用，方法是將可系結 `TabbedPage.TranslucencyMode` 屬性設定為 `TranslucencyMode` 列舉值：

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

`TabbedPage.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `TabbedPage.SetTranslucencyMode`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是透過 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 指定下列其中一個列舉值，用來設定上索引標籤列的半透明度模式 `TranslucencyMode` ：

- `Default`，這會將索引標籤列設定為其預設半透明度模式。 此為 `TabbedPage.TranslucencyMode` 屬性的預設值。
- `Translucent`，這會將索引標籤列設定為半透明。
- `Opaque`，這會將索引標籤列設定為不透明。

此外， `GetTranslucencyMode` 方法可以用來抓取套用至之列舉的目前值 `TranslucencyMode` [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。

結果是可以設定上索引標籤列的半透明度模式 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ：

![IOS 上半透明和不透明索引標籤欄的螢幕擷取畫面](tabbedpage-translucent-tabbar-images/translucencymodes.png "半透明和不透明的索引標籤列")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

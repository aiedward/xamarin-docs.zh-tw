---
title: VisualElement iOS 上的第一個回應程式
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，讓 VisualElement 物件成為觸控事件的第一個回應者。
ms.prod: xamarin
ms.assetid: 3A77BA02-B87A-44EC-AC51-9D3130EF314C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f5089a60d331433b8a007c1ec027746227e7a62f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368657"
---
# <a name="visualelement-first-responder-on-ios"></a>VisualElement iOS 上的第一個回應程式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定可讓 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件成為觸控事件的第一個回應程式，而不是包含元素的頁面。 它是在 XAML 中使用，方法是將可系結 `VisualElement.CanBecomeFirstResponder` 屬性設定為 `true` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry Placeholder="Enter text" />
        <Button ios:VisualElement.CanBecomeFirstResponder="True"
                Text="OK" />
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

`VisualElement.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 在 `VisualElement.SetCanBecomeFirstResponder` 命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來設定， `VisualElement` 成為觸控事件的第一個回應程式。 此外， `VisualElement.CanBecomeFirstResponder` 方法可以用來傳回是否 `VisualElement` 為觸控事件的第一個回應程式。

結果是， [`VisualElement`](xref:Xamarin.Forms.VisualElement) 可以成為觸控事件的第一個回應程式，而不是包含元素的頁面。 這可讓聊天應用程式等案例不會在按下時關閉鍵盤 [`Button`](xref:Xamarin.Forms.Button) 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
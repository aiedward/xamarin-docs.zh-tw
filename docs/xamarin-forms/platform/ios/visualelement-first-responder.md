---
title: 在 iOS 上 VisualElement 第一個回應程式
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，讓 VisualElement 物件成為觸控事件的第一個回應者。
ms.prod: xamarin
ms.assetid: 3A77BA02-B87A-44EC-AC51-9D3130EF314C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: be6c233b63d172d2fcacb1cea7f5e9aeeb7faed1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292967"
---
# <a name="visualelement-first-responder-on-ios"></a>在 iOS 上 VisualElement 第一個回應程式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定可讓[`VisualElement`](xref:Xamarin.Forms.VisualElement)物件成為觸控事件的第一個回應程式，而不是包含專案的頁面。 它會在 XAML 中使用，方法是將 `VisualElement.CanBecomeFirstResponder` 可系結屬性設定為 `true`：

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

或者，也可以C#使用 Fluent API 來取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

`VisualElement.On<iOS>` 方法指定此平臺特定的只會在 iOS 上執行。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間中的 `VisualElement.SetCanBecomeFirstResponder` 方法是用來設定 `VisualElement` 成為觸控事件的第一個回應程式。 此外，`VisualElement.CanBecomeFirstResponder` 方法可以用來傳回 `VisualElement` 是否為觸控事件的第一個回應者。

結果是[`VisualElement`](xref:Xamarin.Forms.VisualElement)可以成為觸控事件的第一個回應程式，而不是包含元素的頁面。 這可讓聊天應用程式之類的情況在按下[`Button`](xref:Xamarin.Forms.Button)時，不會關閉鍵盤。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

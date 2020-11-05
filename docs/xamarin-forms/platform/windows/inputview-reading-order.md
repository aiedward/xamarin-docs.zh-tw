---
title: 在 Windows 上 InputView 讀取順序
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用可動態偵測雙向文字讀取順序的 Windows 平臺特定。
ms.prod: xamarin
ms.assetid: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 55907b5878a112d0ad640cc95049183dd68d89f3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374689"
---
# <a name="inputview-reading-order-on-windows"></a>在 Windows 上 InputView 讀取順序

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這通用 Windows 平臺平臺特定的，可讓您 [`Entry`](xref:Xamarin.Forms.Entry) 以動態方式偵測、和實例中的雙向文字) 的讀取順序 (由左至右或由右至左 [`Editor`](xref:Xamarin.Forms.Editor) [`Label`](xref:Xamarin.Forms.Label) 。 它是在 XAML 中使用，方法是將 [`InputView.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) `Entry` 和 `Editor` 實例) 或 [`Label.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) 附加屬性的 (設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

`Editor.On<Windows>`方法指定此平臺特定只會在通用 Windows 平臺上執行。 [ `InputView.SetDetectReadingOrderFromContent` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. InputView. SetDetectReadingOrderFromContent (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。InputView} 是命名空間中的}、system.string) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 可用來控制是否從中的內容偵測到讀取順序 [`InputView`](xref:Xamarin.Forms.InputView) 。 此外，您 `InputView.SetDetectReadingOrderFromContent` 也可以使用方法，藉由呼叫 [ `InputView.GetDetectReadingOrderFromContent` ] (x：，來切換是否偵測到內容中的讀取順序 Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. InputView. GetDetectReadingOrderFromContent (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。InputView} ) # A3 方法來傳回目前的值：

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

結果是 [`Entry`](xref:Xamarin.Forms.Entry) ， [`Editor`](xref:Xamarin.Forms.Editor) 和 [`Label`](xref:Xamarin.Forms.Label) 實例可以動態偵測其內容的讀取順序：

[![InputView 偵測內容平臺特定的讀取順序](inputview-reading-order-images/editor-readingorder.png "InputView 偵測內容平臺特定的讀取順序")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView 偵測內容平臺特定的讀取順序")

> [!NOTE]
> 不同于設定 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性，從文字內容偵測讀取順序之視圖的邏輯，將不會影響視圖內的文字對齊方式。 相反地，它會調整雙向文字區塊的配置順序。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
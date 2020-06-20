---
title: InputView Windows 上的讀取順序
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，以動態方式偵測雙向文字的讀取順序。
ms.prod: xamarin
ms.assetid: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f5f0bcdc2d2c8eb1b51ad8dcd1014c649af80c90
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137757"
---
# <a name="inputview-reading-order-on-windows"></a>InputView Windows 上的讀取順序

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此通用 Windows 平臺平臺特定的會啟用、和實例中雙向文字的讀取順序（由左至右或由右至左）， [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) [`Label`](xref:Xamarin.Forms.Label) 以動態方式偵測。 將 [`InputView.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) （適用于 `Entry` 和 `Editor` 實例）或 [`Label.DetectReadingOrderFromContent`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) 附加屬性設定為 `boolean` 值，即可在 XAML 中使用它：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

`Editor.On<Windows>`方法會指定此平臺特定只會在通用 Windows 平臺上執行。 [ `InputView.SetDetectReadingOrderFromContent` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. InputView. SetDetectReadingOrderFromContent （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。InputView}，system.string）方法（在命名空間中） [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 是用來控制是否從中的內容偵測到讀取順序 [`InputView`](xref:Xamarin.Forms.InputView) 。 此外， `InputView.SetDetectReadingOrderFromContent` 方法可以藉由呼叫 [ `InputView.GetDetectReadingOrderFromContent` ] （x：，用來切換是否從內容偵測到讀取順序 Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. InputView. GetDetectReadingOrderFromContent （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。InputView}））方法來傳回目前的值：

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

結果是，、 [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) 和 [`Label`](xref:Xamarin.Forms.Label) 實例可以動態偵測到其內容的讀取順序：

[![InputView 偵測來自內容平臺特定的讀取順序](inputview-reading-order-images/editor-readingorder.png "InputView 偵測來自內容平臺特定的讀取順序")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView 偵測來自內容平臺特定的讀取順序")

> [!NOTE]
> 不同于設定 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 屬性，從文字內容偵測讀取順序的視圖邏輯，不會影響視圖中文字的對齊方式。 相反地，它會調整雙向文字區塊的排列順序。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)

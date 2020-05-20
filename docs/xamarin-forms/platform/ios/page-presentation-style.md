---
title: IOS 上的強制回應頁面呈現樣式
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來設定強制回應頁面的呈現樣式。
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
ms.openlocfilehash: 19175a6d97afb9d2d985d8a1bf8e1ce4c0179242
ms.sourcegitcommit: 87035b654434c22ca1b0d70ee8d2496dcb63b365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2020
ms.locfileid: "83546502"
---
# <a name="modal-page-presentation-style-on-ios"></a>IOS 上的強制回應頁面呈現樣式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定是用來設定強制回應頁面的呈現樣式，此外還可以用來顯示具有透明背景的模式頁面。 它會在 XAML 中使用，方法是將可系結 `Page.ModalPresentationStyle` 屬性設定為 `UIModalPresentationStyle` 列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="OverFullScreen">
    ...
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.OverFullScreen);
        ...
    }
}
```

`Page.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `Page.SetModalPresentationStyle`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是藉 [`Page`](xref:Xamarin.Forms.Page) 由指定下列其中一個列舉值，用來設定上的強制回應呈現樣式 `UIModalPresentationStyle` ：

- `FullScreen`，這會將強制回應呈現樣式設定為包含整個螢幕。 根據預設，會使用此呈現樣式來顯示強制回應頁面。
- `FormSheet`，這會將強制回應展示樣式設定為置中和小於螢幕。
- `Automatic`，這會將強制回應呈現樣式設定為系統所選擇的預設值。 對於大部分的視圖控制器而言， `UIKit` 會將此對應至 `UIModalPresentationStyle.PageSheet` ，但某些系統檢視控制器可能會將其對應至不同的樣式。
- `OverFullScreen`，這會設定強制回應呈現樣式以涵蓋螢幕。
- `PageSheet`，它會設定強制回應呈現樣式以涵蓋基礎內容。

此外， `GetModalPresentationStyle` 方法可以用來抓取套用至之列舉的目前值 `UIModalPresentationStyle` [`Page`](xref:Xamarin.Forms.Page) 。

結果是可以設定上的強制回應呈現樣式 [`Page`](xref:Xamarin.Forms.Page) ：

[![](page-presentation-style-images/modal-presentation-style-small.png "Modal Presentation Styles")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Modal Presentation Styles")

> [!NOTE]
> 使用此平臺特定設定強制回應呈現樣式的頁面，必須使用強制回應導覽。 如需詳細資訊，請參閱[Xamarin。表單](~/xamarin-forms/app-fundamentals/navigation/modal.md)強制回應頁面。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

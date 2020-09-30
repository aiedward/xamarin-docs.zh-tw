---
title: IOS 上的強制回應頁面呈現樣式
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用可設定強制回應頁面呈現樣式的 iOS 平臺特定。
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9ed0c2b9b0916349b11f64e83c57f2737c302e92
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557461"
---
# <a name="modal-page-presentation-style-on-ios"></a>IOS 上的強制回應頁面呈現樣式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定用來設定強制回應頁面的呈現樣式，此外還可以用來顯示具有透明背景的強制回應頁面。 它是在 XAML 中使用，方法是將可系結 `Page.ModalPresentationStyle` 屬性設定為 `UIModalPresentationStyle` 列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="OverFullScreen">
    ...
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

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

`Page.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `Page.SetModalPresentationStyle`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來 [`Page`](xref:Xamarin.Forms.Page) 指定下列其中一個列舉值，藉以設定的強制回應呈現樣式 `UIModalPresentationStyle` ：

- `FullScreen`，將強制回應呈現樣式設定為包含整個畫面。 根據預設，強制回應頁面會使用這個呈現樣式來顯示。
- `FormSheet`，可將強制回應呈現樣式設定為置中且小於螢幕。
- `Automatic`，會將強制回應呈現樣式設定為系統所選擇的預設值。 大部分的視圖控制器 `UIKit` 都會將此對應 `UIModalPresentationStyle.PageSheet` 至，但某些系統檢視控制器可能會將其對應至不同的樣式。
- `OverFullScreen`，可將強制回應呈現樣式設定為涵蓋螢幕。
- `PageSheet`，可將強制回應呈現樣式設定為涵蓋基礎內容。

此外， `GetModalPresentationStyle` 方法可以用來取得套用至之列舉的目前值 `UIModalPresentationStyle` [`Page`](xref:Xamarin.Forms.Page) 。

結果是可以設定的強制回應呈現樣式 [`Page`](xref:Xamarin.Forms.Page) ：

[![強制回應呈現樣式](page-presentation-style-images/modal-presentation-style-small.png)](page-presentation-style-images/modal-presentation-style-large.png#lightbox "強制回應呈現樣式")

> [!NOTE]
> 使用這個平臺特定的頁面來設定強制回應展示樣式必須使用強制回應的導覽。 如需詳細資訊，請參閱強制回應[ Xamarin.Forms 頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
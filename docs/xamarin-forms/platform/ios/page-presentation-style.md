---
title: IOS 上的強制回應頁面呈現樣式
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定設定模式頁面的呈現樣式。
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
ms.openlocfilehash: 5078b280499929e0e2e3691539cf1927b4c79fe7
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517531"
---
# <a name="modal-page-presentation-style-on-ios"></a>IOS 上的強制回應頁面呈現樣式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定是用來設定強制回應頁面的呈現樣式。 它會在 XAML 中使用，方法`Page.ModalPresentationStyle`是將可系`UIModalPresentationStyle`結屬性設定為列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
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
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

`Page.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 命名空間中的`Page.SetModalPresentationStyle`方法是藉[`Page`](xref:Xamarin.Forms.Page)由指定下列`UIModalPresentationStyle`其中一個列舉值，用來設定上的強制回應呈現樣式： [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

- `FullScreen`，這會將強制回應呈現樣式設定為包含整個螢幕。 根據預設，會使用此呈現樣式來顯示強制回應頁面。
- `FormSheet`，這會將強制回應展示樣式設定為置中和小於螢幕。
- `Automatic`，這會將強制回應呈現樣式設定為系統所選擇的預設值。 對於大部分的視圖控制器`UIKit`而言，會`UIModalPresentationStyle.PageSheet`將此對應至，但某些系統檢視控制器可能會將其對應至不同的樣式。
- `OverFullScreen`，這會設定強制回應呈現樣式以涵蓋螢幕。
- `PageSheet`，它會設定強制回應呈現樣式以涵蓋基礎內容。

此外， `GetModalPresentationStyle`方法可以用來抓取套用至之`UIModalPresentationStyle`列舉的目前值。 [`Page`](xref:Xamarin.Forms.Page)

結果是[`Page`](xref:Xamarin.Forms.Page)可以設定上的強制回應呈現樣式：

[![](page-presentation-style-images/modal-presentation-style-small.png "Modal Presentation Styles")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Modal Presentation Styles")

> [!NOTE]
> 使用此平臺特定設定強制回應呈現樣式的頁面，必須使用強制回應導覽。 如需詳細資訊，請參閱[Xamarin。表單](~/xamarin-forms/app-fundamentals/navigation/modal.md)強制回應頁面。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

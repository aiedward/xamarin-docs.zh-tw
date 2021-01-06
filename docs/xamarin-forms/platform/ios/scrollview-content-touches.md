---
title: IOS 上的 ScrollView 內容觸控
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，以控制 ScrollView 是否處理觸控手勢，或將其傳遞至其內容。
ms.prod: xamarin
ms.assetid: 99F823DB-B379-40F0-A343-A9783C341120
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 072f5db9115069fad547bb363865a609e5167ce8
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939936"
---
# <a name="scrollview-content-touches-on-ios"></a>IOS 上的 ScrollView 內容觸控

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當觸控手勢于 iOS 上開始時，會觸發隱含計時器， [`ScrollView`](xref:Xamarin.Forms.ScrollView) 並 `ScrollView` 根據計時器範圍內的使用者動作，決定是否應該處理手勢或將其傳遞至其內容。 根據預設，iOS 會 `ScrollView` 延遲內容的內容，但在某些情況下可能會造成問題，但在某些情況下，內容不會在應該的情況下 `ScrollView` 贏得手勢。 因此，此平臺特定會控制是否 `ScrollView` 處理觸控手勢，或將其傳遞至其內容。 它是在 XAML 中使用，方法是將 `ScrollView.ShouldDelayContentTouches` 附加屬性設定為 `boolean` 值：

```xaml
<FlyoutPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <FlyoutPage.Flyout>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </FlyoutPage.Flyout>
    <FlyoutPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </FlyoutPage.Detail>
</FlyoutPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

`ScrollView.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `ScrollView.SetShouldDelayContentTouches`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制是否 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 處理觸控手勢，或將其傳遞至其內容。 此外， `SetShouldDelayContentTouches` 方法可以藉由呼叫 `ShouldDelayContentTouches` 方法來傳回是否延遲內容，來切換延遲的內容觸控：

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

結果是， [`ScrollView`](xref:Xamarin.Forms.ScrollView) 可以停用延遲接收內容的觸控，因此在此案例中，會 [`Slider`](xref:Xamarin.Forms.Slider) 接收手勢，而不是的 [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) 頁面 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) ：

[![ScrollView 延遲內容觸及平臺特定](scrollview-content-touches-images/scrollview-delay-content-touches.png)](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView 延遲內容觸控 Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
---
title: IOS 上的 SwipeView 滑動轉換模式
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來控制開啟 SwipeView 時所使用的轉換。
ms.prod: xamarin
ms.assetid: C667F24C-BAD8-47E0-9285-D3546BEF703B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: d5ba92d008cf3431bce2c197aca45c894eb3d5c7
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490322"
---
# <a name="swipeview-swipe-transition-mode-on-ios"></a>IOS 上的 SwipeView 滑動轉換模式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定控制開啟 `SwipeView`時所使用的轉換。 它會在 XAML 中使用，方法是將 `SwipeView.SwipeTransitionMode` 可系結屬性設定為 `SwipeTransitionMode` 列舉的值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SwipeView ios:SwipeView.SwipeTransitionMode="Drag">
            <SwipeView.LeftItems>
                <SwipeItems>
                    <SwipeItem Text="Delete"
                               IconImageSource="delete.png"
                               BackgroundColor="LightPink"
                               Invoked="OnDeleteSwipeItemInvoked" />
                </SwipeItems>
            </SwipeView.LeftItems>
            <!-- Content -->
        </SwipeView>
    </StackLayout>
</ContentPage>
```

或者，也可以C#使用 Fluent API 來取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<iOS>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

`SwipeView.On<iOS>` 方法指定此平臺特定的只會在 iOS 上執行。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間中的 `SwipeView.SetSwipeTransitionMode` 方法是用來控制開啟 `SwipeView`時所使用的轉換。 `SwipeTransitionMode` 列舉提供兩個可能的值：

- `Reveal` 表示當 `SwipeView` 內容為撥動時，會顯示滑動專案，而則是 `SwipeView.SwipeTransitionMode` 屬性的預設值。
- `Drag` 表示當 `SwipeView` 內容為撥動時，會將滑動專案拖曳到 view。

此外，`SwipeView.GetSwipeTransitionMode` 方法可以用來傳回套用至 `SwipeView`的 `SwipeTransitionMode`。

結果是指定的 `SwipeTransitionMode` 值會套用至 `SwipeView`，以控制開啟 `SwipeView`時所使用的轉換：

[![IOS 上的 SwipeView SwipeTransitionModes 螢幕擷取畫面](swipeview-swipetransitionmode-images/swipetransitionmode.png "IOS 上的 SwipeTransitionModes")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "IOS 上的 SwipeTransitionModes")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)

---
title: SwipeView 在 Android 上滑動切換模式
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的來控制開啟 SwipeView 時所使用的轉換。
ms.prod: xamarin
ms.assetid: 6B1F8213-9D62-4C40-9F04-881F1371B5AA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c420fe65b020067169230dd06dbcd5ce65c036ab
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128618"
---
# <a name="swipeview-swipe-transition-mode-on-android"></a>SwipeView 在 Android 上滑動切換模式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定控制開啟時所使用的轉換 `SwipeView` 。 它會在 XAML 中使用，方法是將可系結 `SwipeView.SwipeTransitionMode` 屬性設定為列舉的值 `SwipeTransitionMode` ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core" >
    <StackLayout>
        <SwipeView android:SwipeView.SwipeTransitionMode="Drag">
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

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<Android>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

`SwipeView.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 `SwipeView.SetSwipeTransitionMode`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制開啟時所使用的轉換 `SwipeView` 。 `SwipeTransitionMode`列舉提供兩個可能的值：

- `Reveal`表示當內容為撥動時，會顯示滑動專案 `SwipeView` ，而則是屬性的預設值 `SwipeView.SwipeTransitionMode` 。
- `Drag`表示在撥動內容時，會將滑動專案拖曳到 view `SwipeView` 。

此外， `SwipeView.GetSwipeTransitionMode` 方法可以用來傳回套用 `SwipeTransitionMode` 至的 `SwipeView` 。

結果是指定的 `SwipeTransitionMode` 值會套用至 `SwipeView` ，以控制開啟時所使用的轉換 `SwipeView` ：

[![Android 上 SwipeView SwipeTransitionModes 的螢幕擷取畫面](swipeview-swipetransitionmode-images/swipetransitionmode.png "Android 上的 SwipeTransitionModes")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "Android 上的 SwipeTransitionModes")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)

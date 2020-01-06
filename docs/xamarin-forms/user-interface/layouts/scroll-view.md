---
title: Xamarin. Forms ScrollView
description: 本文說明如何使用 ScrollView 類別來呈現無法僅放在一個畫面上的配置，以及有內容可讓鍵盤使用的版面配置。
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2019
ms.openlocfilehash: bb10cda7c9899f176861ceee712cc876984c56ef
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488266"
---
# <a name="xamarinforms-scrollview"></a>Xamarin. Forms ScrollView

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView)包含版面配置，並可讓它們在外滾動。 `ScrollView` 也可以在顯示鍵盤時，用來允許視圖自動移至螢幕的可見部分。

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>用途

[`ScrollView`](xref:Xamarin.Forms.ScrollView)可以用來確保較大的視圖在較小的手機上顯示良好。 例如，可以在 iPhone 4s 上裁剪適用于 iPhone 6s 的版面配置。 使用 `ScrollView` 可以讓版面配置的裁剪部分顯示在較小的螢幕上。

## <a name="usage"></a>使用

> [!NOTE]
> 不應將[`ScrollView`](xref:Xamarin.Forms.ScrollView)物件加以嵌套。 此外，`ScrollView`s 不應該與提供滾動的其他控制項（例如 `ListView` 和 `WebView`）加以嵌套。

[`ScrollView`](xref:Xamarin.Forms.ScrollView)會公開 `Content` 屬性，可以設定為單一視圖或版面配置。 請以非常大的 boxView （後面接著 `Entry`）做為版面配置的範例：

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

在 C# 中：

```csharp
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

在使用者向下滾動之前，只會顯示 `BoxView`：

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

請注意，當使用者開始在 `Entry`中輸入文字時，視圖會滾動，讓它在畫面上顯示：

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>屬性

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 會定義下列屬性：

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty)取得代表內容大小的[`Size`](xref:Xamarin.Forms.Size)值。
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)取得或設定[`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation)列舉值，表示 `ScrollView`的滾動方向。
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty)取得代表目前 X 捲軸位置的 `double`。
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty)取得代表目前 Y 捲軸位置的 `double`。
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty)取得或設定代表水準捲軸可見時間的[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)值。
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty)取得或設定代表垂直捲動條可見時間的[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)值。

> [!NOTE]
> 將 [ [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) ] 屬性設定為 [`Neither`]，即可停用滾動。

## <a name="methods"></a>方法

[`ScrollView`](xref:Xamarin.Forms.ScrollView)提供 `ScrollToAsync` 方法，可以用來使用座標或指定應該顯示的特定視圖來滾動視圖。

使用座標時，請指定 `x` 和 `y` 座標，以及布林值，指出是否應將滾動動畫：

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> 當[`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)屬性設定為 `Neither`時，`ScrollToAsync` 方法不會產生捲軸。

當滾動到特定專案時，`ScrollToPosition` 列舉會指定要在視圖中顯示元素的位置：

- **置**中 &ndash; 將元素滾動到視圖可見部分的中央。
- **結束**&ndash; 將元素滾動到視圖可見部分的結尾。
- **MakeVisible** &ndash; 會滾動專案，使其可在視圖中顯示。
- **開始**&ndash; 將元素滾動到視圖可見部分的開頭。

[`IsAnimated`] 屬性會指定如何滾動視圖。 設定為 [`true`] 時，將會使用平滑動畫，而不是立即將內容移至 [視圖]。

## <a name="events"></a>「事件」

[`ScrollView`](xref:Xamarin.Forms.ScrollView)只會定義一個事件，`Scrolled`。 當視圖完成滾動時，就會引發 `Scrolled`。 `Scrolled` 的事件處理常式會接受 `ScrolledEventArgs`，其具有 `ScrollX` 和 `ScrollY` 屬性。 以下示範如何以 `ScrollView`的目前滾動位置來更新標籤：

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

請注意，捲軸位置可能是負面的，因為在清單的結尾處滾動時，會產生跳動效果。

## <a name="related-links"></a>相關連結

- [版面配置（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 範例（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)

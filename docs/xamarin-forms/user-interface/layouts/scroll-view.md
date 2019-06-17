---
title: Xamarin.Forms ScrollView
description: 這篇文章說明如何使用 Xamarin.Forms ScrollView 類別呈現，無法容納在一個畫面上，而且具有內容挪出空間的鍵盤配置。
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 34339b9ca3a15c7f7f24edee5401c542fd09ba74
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61370521"
---
# <a name="xamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 包含版面配置，並讓使用者能夠在幕後的捲軸。 `ScrollView` 也用來允許檢視，以顯示鍵盤時，會自動移至螢幕的可見部分。

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms 版面配置")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms 版面配置")

本文涵蓋：

- **[用途](#purpose)** &ndash;的目的`ScrollView`和搭配使用時。
- **[使用方式](#usage)** &ndash;如何使用`ScrollView`實務。
- **[屬性](#properties)** &ndash;可以讀取和修改的公用屬性。
- **[方法](#methods)** &ndash;可以捲動檢視呼叫的公用方法。
- **[事件](#events)** &ndash;可以用來接聽檢視狀態變更的事件。

## <a name="purpose"></a>用途

`ScrollView` 可用來確保也在較小的手機上，顯示較大的檢視。 比方說，在 iPhone 6s 的運作方式的版面配置可能會裁剪 iphone 4 秒。 使用`ScrollView`可讓版面配置，以較小螢幕上所顯示的裁剪的部分。

## <a name="usage"></a>使用量

> [!NOTE]
> `ScrollView`s 不是巢狀。 颾魤 ㄛ `ScrollView`s 應該與其他控制項，例如提供向下捲動，巢狀`ListView`和`WebView`。

`ScrollView` 會公開`Content`屬性可以設定的單一檢視或配置。 這個非常大型的 boxView，後面接著版面配置的範例，請考慮`Entry`:

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
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,    HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

使用者向下捲動，只有之前`BoxView`會顯示：

![](scroll-view-images/scroll-start.png "在 ScrollView BoxView")

請注意，當使用者開始輸入文字中的`Entry`，捲動以讓它在螢幕顯示的檢視：

![](scroll-view-images/scroll-end.png "ScrollView 中的項目")

## <a name="properties"></a>屬性

`ScrollView` 定義下列屬性：

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) 取得[ `Size` ](xref:Xamarin.Forms.Size)值，表示內容的大小。
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) 取得或設定[ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation)列舉值，表示捲動的方向`ScrollView`。
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) 取得`double`，表示目前捲動位置的 X。
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) 取得`double`，代表目前的 Y 捲軸位置。
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) 取得或設定[ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility)值，表示水平捲軸為可見時。
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) 取得或設定[ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility)值，表示顯示垂直捲軸時。

## <a name="methods"></a>方法

`ScrollView` 提供`ScrollToAsync`方法，可用來捲動檢視使用的座標，或指定特定的檢視應顯示出來。

當使用座標，指定`x`和`y`座標，以及指出是否捲動應該顯示動畫的布林值：

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

向下捲動到特定的項目中，當`ScrollToPosition`列舉型別指定檢視中的項目出現的位置：

- **Center** &ndash;捲動至檢視的可見部分的中心元素。
- **結束**&ndash;捲動至檢視的可見部分結尾的項目。
- **MakeVisible** &ndash;捲動項目，讓您可在檢視內為可見。
- **開始**&ndash;捲動至檢視的可見部分開頭的項目。

`IsAnimated`屬性會指定如何將捲動檢視。 時設定為 true，動畫更為順暢，將使用，而不是立即將內容移到檢視。

## <a name="events"></a>事件

`ScrollView` 定義一個事件， `Scrolled`。 `Scrolled` 檢視已完成捲動時引發。 事件處理常式`Scrolled`會採用`ScrolledEventArgs`，其中包含`ScrollX`和`ScrollY`屬性。 以下示範如何更新目前捲動位置的標籤`ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

請注意，捲動位置可能是負數的因為時捲動清單結尾處的彈跳效果。


## <a name="related-links"></a>相關連結

- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)

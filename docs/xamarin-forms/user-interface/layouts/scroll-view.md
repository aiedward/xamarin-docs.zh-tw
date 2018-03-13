---
title: ScrollView
description: "您可以使用 ScrollView 呈現不能只有一個螢幕上，而且其內容騰出空間給鍵盤配置。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2016
ms.openlocfilehash: 648125ca8bd2c7c8a015b4c29195dc75c0bbf0a0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="scrollview"></a>ScrollView

[`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) 包含配置，並可讓他們幕後的捲軸。 `ScrollView` 也用來允許檢視來顯示鍵盤時，會自動移至螢幕的可見部分。

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms 配置")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms 版面配置")

本文涵蓋：

- **[目的](#Purpose)** &ndash;的目的`ScrollView`和使用時。
- **[使用量](#Usage)** &ndash;如何使用`ScrollView`實務。
- **[屬性](#Properties)** &ndash;可以讀取及修改的公用屬性。
- **[方法](#Methods)** &ndash;可以捲動檢視呼叫的公用方法。
- **[事件](#Events)** &ndash;可用來接聽檢視狀態變更的事件。

## <a name="purpose"></a>用途

`ScrollView` 可以用於確保較大的檢視會顯示在較小的手機上。 例如，適用於 iPhone 6s 版面配置可能會裁剪 4s 在 iPhone 上。 使用`ScrollView`會允許要在較小螢幕上顯示配置的裁剪的部分。

## <a name="usage"></a>使用量

> [!NOTE]
> `ScrollView`s 不是巢狀。 此外，`ScrollView`應該不使用其他控制項，可提供向下捲動，例如巢狀 s`ListView`和`WebView`。

`ScrollView` 公開`Content`內容可以在單一檢視或版面配置設定。 請考量以下範例使用非常大的 boxView，後面加上配置的`Entry`:

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
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,   HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

使用者向下捲動，只有之前`BoxView`會顯示：

![](scroll-view-images/scroll-start.png "在 ScrollView BoxView")

請注意，當使用者開始輸入文字中的`Entry`，捲動，讓它在螢幕顯示的檢視：

![](scroll-view-images/scroll-end.png "ScrollView 中的項目")

## <a name="properties"></a>屬性

ScrollView 具有下列屬性：

- **內容**&ndash;取得或設定要顯示在檢視`ScrollView`。
- **[ContentSize](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/)**  &ndash;唯讀狀態，取得內容，其寬度和高度元件的大小。 這是可繫結屬性
- **[方向](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/)** &ndash;這是[ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/)，這是列舉，可設為`Horizontal`， `Vertical`，或`Both`。
- **ScrollX** &ndash;唯讀狀態，取得目前捲動位置的 X 維度。
- **ScrollY** &ndash;唯讀狀態，取得目前捲動位置的 Y 維度。

## <a name="methods"></a>方法

`ScrollView` 提供`ScrollToAsync`方法，後者可以捲動檢視使用座標，或藉由指定特定的檢視應顯示出來。

當使用座標，指定`x`和`y`座標，以及表示是否捲動應該繪製的布林值：

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

捲動至特定的項目，當`ScrollToPosition`列舉指定檢視中的項目出現的位置：

- **Center** &ndash;捲動至可見的部分檢視的中心項目。
- **結束**&ndash;捲動到檢視的可見部分結尾的項目。
- **MakeVisible** &ndash;捲動項目，它會顯示在檢視中。
- **啟動**&ndash;捲動到檢視的可見部分開頭的項目。

`IsAnimated`屬性會指定如何將捲動檢視。 當設為 true，smooth 動畫會使用，而不是立即將內容移到檢視。

## <a name="events"></a>事件

`ScrollView` 會公開事件， `Scrolled`。 `Scrolled` 檢視已完成將捲動時引發。 事件處理常式`Scrolled`採用`ScrolledEventArgs`，其具有`ScrollX`和`ScrollY`屬性。 以下示範如何使用目前的捲動位置更新標籤`ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

請注意，捲動位置可能是負數，因為在清單結尾捲動時的彈回效果。


## <a name="related-links"></a>相關連結

- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)

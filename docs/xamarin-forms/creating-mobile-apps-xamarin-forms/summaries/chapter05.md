---
title: 第 5 章的摘要。 因應大小
description: 使用 Xamarin.Forms 建立行動應用程式： 第 5 章的摘要。 因應大小
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 0e99d15bbecf6640b714b499997144eb18268183
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51562701"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>第 5 章的摘要。 因應大小

> [!NOTE] 
> 在此頁面上的附註表示其中 Xamarin.Forms 有分歧活頁簿中所呈現的題材的區域。

到目前為止已發生在 Xamarin.Forms 中的數種大小：

- IOS 的 [狀態] 列的高度為 20
- `BoxView`預設寬度和高度為 40
- 預設值`Padding`在`Frame`為 20
- 預設值`Spacing`上`StackLayout`為 6
- `Device.GetNamedSize`方法會傳回數值的字型大小

這些大小不是像素為單位。 相反地，它們是裝置獨立單位，每個平台獨立辨識。

## <a name="pixels-points-dps-dips-and-dius"></a>像素為單位 」、 「 點 」、 「 dps 」、 「 Dip 和 「 DIUs

Apple Mac 和 Microsoft Windows 的歷程記錄，在早期程式設計師曾以像素為單位。 不過，更高解析度顯示器的問世所需更多虛擬化及抽象的方法，以螢幕座標。 在 Mac 的世界中，程式設計人員處理單位*點*、 過去 1/72 英吋，而使用的 Windows 開發人員*裝置獨立單位*(DIUs) 會根據 1/96 英吋。

行動裝置，不過，通常會保留更接近所面臨的而且有較高的解析度，比桌面螢幕，這表示，能容許更高的像素密度。

以 Apple iPhone 和 iPad 裝置為目標的程式設計人員繼續運作的單位*點*，但有 160 個點英吋。 根據裝置，可能有 1、 2 或 3 個像素的點。

Android 很類似。 程式設計人員使用的單位*密度無關的像素*(dps)，且 dps 和像素為單位之間的關聯性根據 160 dp 英吋。

Windows 手機和行動裝置也建立了表示幾近 160 英吋的裝置獨立單位的縮放比例。

> [!NOTE]
> Xamarin.Forms 不再支援任何以 Windows 為基礎的電話或行動裝置。

在 [摘要] 以電話和平板電腦為目標的 Xamarin.Forms 程式設計師可以假設所有度量單位都根據下列準則：

- 160 單位為英吋，相當於
- 以公分 64 單位

唯讀[ `Width` ](xref:Xamarin.Forms.VisualElement.Width)並[ `Height` ](xref:Xamarin.Forms.VisualElement.Height)屬性所定義`VisualElement`有 「 模擬 」 值的預設&ndash;1。 已調整大小並容納在版面配置項目時，才將這些屬性會反映實際的裝置獨立單位中的項目大小。 此大小可包含任何`Padding`項目上設定，但不是`Margin`。

視覺化項目，就會引發[ `SizeChanged` ](xref:Xamarin.Forms.VisualElement.SizeChanged)事件時其`Width`或`Height`已變更。 [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize)範例會使用這個事件來顯示程式的螢幕大小。

## <a name="metrical-sizes"></a>Metrical 大小

[ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView)會使用[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)並[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)以顯示`BoxView`一英吋高，另一個公分寬。

## <a name="estimated-font-sizes"></a>估計的字型大小

[**浮水印文字**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes)範例示範如何使用 160 單位-至-吋規則，以指定的字型大小以點為單位。 使用這項技術的平台之間的視覺一致性是優於`Device.GetNamedSize`。

## <a name="fitting-text-to-available-size"></a>若要使用的大小調整文字

可以藉由計算符合特定的矩形內的文字區塊`FontSize`的`Label`使用下列準則：

- 行距是字型大小的 120%（在 Windows 平台上的 130%)。
- 平均字元寬度是字型大小的 50%。

[ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize)範例會示範這項技術。 此程式之前所撰寫[ `Margin` ](xref:Xamarin.Forms.View.Margin)屬性是可用的因此它會使用[ `ContentView` ](xref:Xamarin.Forms.ContentView)具有[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)模擬設定邊界。

[![估計的字型大小的三個螢幕擷取畫面](images/ch05fg07-small.png "文字放到可用的大小，使得")](images/ch05fg07-large.png#lightbox "文字符合可用的大小")

## <a name="a-fit-to-size-clock"></a>符合大小-大小時鐘

[ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock)範例示範如何使用[ `Device.StartTimer` ](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean}))啟動計時器，定期在更新時鐘的時候通知應用程式。 字型大小設六分之一的頁面寬度，讓顯示器一樣大。

## <a name="accessibility-issues"></a>協助工具問題

**EstimatedFontSize**計劃和**FitToSizeClock**程式，這兩個包含不易察覺的問題： 如果使用者變更手機上的協助工具設定，在 Android 或 Windows 10 行動裝置，不會再執行程式可以估計大小呈現文字的字型大小為基礎。 [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest)範例會示範此問題。

## <a name="empirically-fitting-text"></a>廣為人知調整文字

另一種方式，以容納文字的矩形是廣為人知計算呈現的文字大小，並向上或向下調整。 中的活頁簿呼叫的程式[ `GetSizeRequest` ](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))上視覺化的項目，若要取得的項目所需的大小。 方法已被取代，，和程式應該改為呼叫[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))。

針對`Label`第一個引數都必須是 （如果您要允許換行） 之容器的寬度，第二個引數應該設定到`Double.PositiveInfinity`讓未受限制的高度。 [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize)範例會示範這項技術。



## <a name="related-links"></a>相關連結

- [第 5 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [第 5 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [第 5 章：F#範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)

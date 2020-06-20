---
title: 第5章的摘要。 因應大小
description: 建立 Mobile Apps Xamarin.Forms ：第5章的摘要。 因應大小
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 37403cfe9f37972c20fb074db5e30cc54b60fea9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136873"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>第5章的摘要。 因應大小

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

Xamarin.Forms目前為止已遇到數種大小：

- IOS 狀態列的高度為20
- 的 `BoxView` 預設寬度和高度為40
- 中的預設值 `Padding` `Frame` 為20
- 的預設值 `Spacing` `StackLayout` 為6
- 方法會傳回 `Device.GetNamedSize` 數值字型大小

這些大小不是圖元。 相反地，它們是由每個平臺獨立識別的裝置獨立單位。

## <a name="pixels-points-dps-dips-and-dius"></a>圖元、點、dps、Dip 和 Diu

在 Apple Mac 和 Microsoft Windows 的歷程記錄中，程式設計人員是以圖元為單位來處理。 不過，較高解析度的出現會顯示為螢幕座標所需的更虛擬化和抽象方法。 在 Mac 世界中，程式設計人員是以時間*點*（傳統1/72 英寸）來運作，而 Windows 開發人員則使用以1/96 英寸*為基礎的裝置獨立單位*（diu）。

不過，行動裝置通常會更接近臉部，而且解析度比桌面畫面更高，這表示可以容許較大的圖元密度。

以 Apple iPhone 和 iPad 裝置為目標的程式設計人員會繼續以*點*為單位來工作，但這些點的160為英寸。 視裝置而定，點可能會有1、2或3個圖元。

Android 很類似。 程式設計人員會以*與密度無關的圖元*（dps）為單位工作，而 dps 和圖元之間的關聯性是以 160 dps 到英寸為基礎。

Windows phone 和行動裝置也已建立調整因素，表示接近160裝置獨立單位到英寸的某個部分。

> [!NOTE]
> Xamarin.Forms不再支援任何以 Windows 為基礎的電話或行動裝置。

總而言之，以 Xamarin.Forms 手機和平板電腦為目標的程式設計人員可以假設所有測量單位都是以下列準則為基礎：

- 160單位到英寸，相當於
- 64單位至釐米

所定義的唯讀 [`Width`](xref:Xamarin.Forms.VisualElement.Width) 和 [`Height`](xref:Xamarin.Forms.VisualElement.Height) 屬性 `VisualElement` 具有預設的 "mock" 值 &ndash; 1。 只有當專案已調整大小並容納于版面配置時，這些屬性才會反映專案的實際大小（以與裝置無關的單位）。 這個大小包含專案 `Padding` 上的任何設定，而不 `Margin` 是。

視覺專案已變更時，會引發 [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) 事件 `Width` `Height` 。 [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize)範例會使用此事件來顯示程式畫面的大小。

## <a name="metrical-sizes"></a>度量大小

此[**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView)會使用 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 來顯示一 `BoxView` 英寸的高和一釐米寬。

## <a name="estimated-font-sizes"></a>估計的字型大小

[**FontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes)範例示範如何使用 160-單位到英寸的規則來指定以點為單位的字型大小。 使用這項技術的平臺之間的視覺一致性，優於 `Device.GetNamedSize` 。

## <a name="fitting-text-to-available-size"></a>將文字調整成可用大小

`FontSize`使用下列準則來計算的，可以配合特定矩形內的一段文字 `Label` ：

- 行距是120% 的字型大小（在 Windows 平臺上為130%）。
- 平均字元寬度是字型大小的50%。

[**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize)範例會示範這項技術。 此程式是在 [`Margin`](xref:Xamarin.Forms.View.Margin) 屬性可供使用之前寫入，因此它會使用 [`ContentView`](xref:Xamarin.Forms.ContentView) 具有 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 設定的來模擬邊界。

[![估計字型大小的三向螢幕擷取畫面](images/ch05fg07-small.png "文字元合可用大小")](images/ch05fg07-large.png#lightbox "文字元合可用大小")

## <a name="a-fit-to-size-clock"></a>大小調整時鐘

[**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock)範例 [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) 會示範如何使用來啟動計時器，以便在更新時鐘時定期通知應用程式。 字型大小設定為頁面寬度的六分之一，讓顯示器越大越好。

## <a name="accessibility-issues"></a>協助工具問題

**EstimatedFontSize**程式和**FitToSizeClock**程式都包含一個微妙的缺陷：如果使用者在 Android 或 Windows 10 行動裝置版上變更電話的協助工具設定，程式就不再可以根據字型大小來估計文字的大小。 [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest)範例會示範此問題。

## <a name="empirically-fitting-text"></a>廣為人知且實證調整文字

將文字放入矩形的另一種方式是廣為人知且實證計算轉譯的文字大小，並向上或向下調整。 本書中的程式會呼叫 [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) 視覺元素上的，以取得元素的所需大小。 該方法已被取代，而程式應改為呼叫 [ `Measure` ] （x： Xamarin.Forms 。VisualElement. Measure （system.string，double，， Xamarin.Forms 。MeasureFlags)).

若為 `Label` ，第一個引數應該是容器的寬度（以允許換行），而第二個引數應該設定為， `Double.PositiveInfinity` 使高度不受限制。 [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize)範例會示範這項技術。

## <a name="related-links"></a>相關連結

- [第5章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [第5章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [第5章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)

---
title: 第5章的摘要。 處理大小
description: 使用 Xamarin 建立 Mobile Apps：第5章的摘要。 處理大小
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771146"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>第5章的摘要。 處理大小

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> 此頁面上的附注指出 Xamarin 從書籍中呈現的材料中分歧的區域。

Xamarin 中有數種大小。目前為止已遇到表單：

- IOS 狀態列的高度為20
- `BoxView` 的預設寬度和高度為40
- `Frame` 中的預設 `Padding` 是20
- `StackLayout` 上的預設 `Spacing` 為6
- `Device.GetNamedSize` 方法會傳回數值字型大小

這些大小不是圖元。 相反地，它們是由每個平臺獨立識別的裝置獨立單位。

## <a name="pixels-points-dps-dips-and-dius"></a>圖元、點、dps、Dip 和 Diu

在 Apple Mac 和 Microsoft Windows 的歷程記錄中，程式設計人員是以圖元為單位來處理。 不過，較高解析度的出現會顯示為螢幕座標所需的更虛擬化和抽象方法。 在 Mac 世界中，程式設計人員是以時間*點*（傳統1/72 英寸）來運作，而 Windows 開發人員則使用以1/96 英寸*為基礎的裝置獨立單位*（diu）。

不過，行動裝置通常會更接近臉部，而且解析度比桌面畫面更高，這表示可以容許較大的圖元密度。

以 Apple iPhone 和 iPad 裝置為目標的程式設計人員會繼續以*點*為單位來工作，但這些點的160為英寸。 視裝置而定，點可能會有1、2或3個圖元。

Android 很類似。 程式設計人員會以*與密度無關的圖元*（dps）為單位工作，而 dps 和圖元之間的關聯性是以 160 dps 到英寸為基礎。

Windows phone 和行動裝置也已建立調整因素，表示接近160裝置獨立單位到英寸的某個部分。

> [!NOTE]
> Xamarin 已不再支援任何以 Windows 為基礎的電話或行動裝置。

總而言之，以手機和平板電腦為目標的 Xamarin. Forms 程式設計人員可以假設所有測量單位都是根據下列準則：

- 160單位到英寸，相當於
- 64單位至釐米

`VisualElement` 所定義的唯讀[`Width`](xref:Xamarin.Forms.VisualElement.Width)和[`Height`](xref:Xamarin.Forms.VisualElement.Height)屬性都有 &ndash;1 的預設 "mock" 值。 只有當專案已調整大小並容納于版面配置時，這些屬性才會反映專案的實際大小（以與裝置無關的單位）。 此大小包括在元素上設定的任何 `Padding`，但不包含 `Margin`的。

視覺元素 `Width` 或 `Height` 變更時，會引發[`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)事件。 [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize)範例會使用此事件來顯示程式畫面的大小。

## <a name="metrical-sizes"></a>度量大小

此[**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView)會使用[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)和[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) ，以顯示 `BoxView` 一英寸高和1個寬度。

## <a name="estimated-font-sizes"></a>估計的字型大小

[**FontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes)範例示範如何使用 160-單位到英寸的規則來指定以點為單位的字型大小。 使用這項技術的平臺之間的視覺一致性，優於 `Device.GetNamedSize`。

## <a name="fitting-text-to-available-size"></a>將文字調整成可用大小

您可以使用下列準則來計算 `Label` 的 `FontSize`，以符合特定矩形內的一段文字：

- 行距是120% 的字型大小（在 Windows 平臺上為130%）。
- 平均字元寬度是字型大小的50%。

[**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize)範例會示範這項技術。 此程式是在[`Margin`](xref:Xamarin.Forms.View.Margin)屬性可用之前寫入的，因此它會使用具有[`Padding`](xref:Xamarin.Forms.Layout.Padding)設定的[`ContentView`](xref:Xamarin.Forms.ContentView)來模擬邊界。

[![估計字型大小的三向螢幕擷取畫面](images/ch05fg07-small.png "文字元合可用大小")](images/ch05fg07-large.png#lightbox "文字元合可用大小")

## <a name="a-fit-to-size-clock"></a>大小調整時鐘

[**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock)範例會示範如何使用[`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean}))來啟動計時器，以便在更新時鐘時定期通知應用程式。 字型大小設定為頁面寬度的六分之一，讓顯示器越大越好。

## <a name="accessibility-issues"></a>協助工具問題

**EstimatedFontSize**程式和**FitToSizeClock**程式都包含一個微妙的缺陷：如果使用者在 Android 或 Windows 10 行動裝置版上變更電話的協助工具設定，程式就不再可以根據字型大小來估計文字的大小。 [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest)範例會示範此問題。

## <a name="empirically-fitting-text"></a>廣為人知且實證調整文字

將文字放入矩形的另一種方式是廣為人知且實證計算轉譯的文字大小，並向上或向下調整。 本書中的程式會呼叫視覺元素上的[`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) ，以取得專案的所需大小。 該方法已被取代，而程式應改為呼叫[`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))。

若為 `Label`，第一個引數應該是容器的寬度（以允許換行），而第二個引數應設定為 `Double.PositiveInfinity`，使高度不受限制。 [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize)範例會示範這項技術。

## <a name="related-links"></a>相關連結

- [第5章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [第5章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [第 5 F#章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)

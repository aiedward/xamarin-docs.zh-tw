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
ms.openlocfilehash: 8cd68fdc3d7e94b6ae12ce6296dc4d698bc0ebd2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370256"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>第5章的摘要。 因應大小

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

目前為止已有數種大小 Xamarin.Forms ：

- IOS 狀態列的高度為20
- 的 `BoxView` 預設寬度和高度為40
- 的預設值 `Padding` `Frame` 為20
- 的預設值 `Spacing` `StackLayout` 為6
- 方法會傳回 `Device.GetNamedSize` 數值字型大小

這些大小不是圖元。 相反地，它們是每個平臺獨立辨識的裝置獨立單位。

## <a name="pixels-points-dps-dips-and-dius"></a>圖元、點、dps、Dip 和 Diu

在 Apple Mac 和 Microsoft Windows 的歷程記錄中，程式設計人員會以圖元為單位來工作。 不過，較高解析度的出現會顯示需要更多虛擬化和抽象的螢幕座標方法。 在 Mac 世界中，程式設計人員會以時間單位（傳統1/72 英寸）來 *運作，而* Windows 開發人員則使用 *與裝置無關的單位* (根據1/96 英寸來 diu) 。

不過，行動裝置通常會更接近臉部，並具有比桌面畫面更高的解析度，這表示可以容許較大的圖元密度。

以 Apple iPhone 和 iPad 裝置為目標的程式設計人員會繼續以 *點* 為單位工作，但這些點的160會以英寸為單位。 視裝置而定，點可能會有1、2或3個圖元。

Android 很類似。 程式設計人員會以 *密度無關的圖元* (dps) 的單位來運作，而 dps 與圖元之間的關聯性則是以 160 dps 到英寸為基礎。

Windows 手機和行動裝置也建立了調整因素，表示接近160裝置獨立單位至英寸的部分。

> [!NOTE]
> Xamarin.Forms 不再支援任何以 Windows 為基礎的電話或行動裝置。

總而言之，以 Xamarin.Forms 手機和平板電腦為目標的程式設計人員可以假設所有測量單位都是根據下列準則：

- 160單位至英寸，相當於
- 64單位至釐米

由定義的唯讀 [`Width`](xref:Xamarin.Forms.VisualElement.Width) 和 [`Height`](xref:Xamarin.Forms.VisualElement.Height) 屬性 `VisualElement` 具有預設的 "mock" 值 &ndash; 1。 只有當元素已調整大小且可在版面配置中容納時，這些屬性才會反映元素在裝置獨立單位中的實際大小。 此大小包含 `Padding` 元素上的任何集合，但不包含 `Margin` 。

視覺效果元素會 [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) 在其 `Width` 或已變更時引發事件 `Height` 。 [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize)範例會使用此事件來顯示程式畫面的大小。

## <a name="metrical-sizes"></a>度量大小

[**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView)會使用 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 來顯示一 `BoxView` 英寸的高度和一釐米寬。

## <a name="estimated-font-sizes"></a>估計的字型大小

[**FontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes)範例示範如何使用160單位到英寸的規則，以點為單位來指定字型大小。 使用這項技術的平臺之間的視覺化一致性優於 `Device.GetNamedSize` 。

## <a name="fitting-text-to-available-size"></a>將文字調整為可用大小

您可以 `FontSize` 使用下列準則來計算的，以符合特定矩形內的一段文字 `Label` ：

- 行距為 Windows 平臺) 上 (130% 的字型大小120%。
- 平均字元寬度是字型大小的50%。

[**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize)範例會示範這項技巧。 此程式是在屬性可用之前寫入的 [`Margin`](xref:Xamarin.Forms.View.Margin) ，因此它會使用 [`ContentView`](xref:Xamarin.Forms.ContentView) 具有 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 設定的來模擬邊界。

[![估計字型大小的三重螢幕擷取畫面](images/ch05fg07-small.png "符合可用大小的文字")](images/ch05fg07-large.png#lightbox "符合可用大小的文字")

## <a name="a-fit-to-size-clock"></a>大小調整時鐘

[**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock)範例 [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) 會示範如何使用來啟動計時器，以在更新時鐘時定期通知應用程式。 字型大小會設定為頁面寬度的一到六個，讓顯示器越大越好。

## <a name="accessibility-issues"></a>協助工具問題

**EstimatedFontSize** 程式和 **FitToSizeClock** 程式都包含微妙的瑕疵：如果使用者變更 Android 或 Windows 10 行動裝置版上的手機協助工具設定，程式就不再可以根據字型大小來預估文字的轉譯大小。 [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest)範例會示範這個問題。

## <a name="empirically-fitting-text"></a>廣為人知且實證調整文字

將文字放在矩形中的另一種方式是廣為人知且實證計算轉譯的文字大小，並向上或向下調整。 書籍中的程式會 [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) 在視覺元素上呼叫，以取得元素所需的大小。 該方法已被取代，而程式應該改為呼叫 [ `Measure` ] (x： Xamarin.Forms 。VisualElement Measure (system.string，Double， Xamarin.Forms 。MeasureFlags) # A3。

若為 `Label` ，第一個引數應該是容器 (的寬度，以允許包裝) ，而第二個引數應該設定為， `Double.PositiveInfinity` 讓高度不受限制。 [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize)範例會示範這項技巧。

## <a name="related-links"></a>相關連結

- [第5章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [第5章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [第5章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)

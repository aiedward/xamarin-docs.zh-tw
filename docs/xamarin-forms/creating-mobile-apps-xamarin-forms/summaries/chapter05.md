---
title: 第5章摘要。 因應大小
description: 使用 Xamarin.表單創建行動應用程式:第 5 章摘要。 因應大小
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771146"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>第5章摘要。 因應大小

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

到目前為止,在 Xamarin.窗體中遇到了多個尺寸:

- iOS 狀態列的高度為 20
- 預設`BoxView`寬度和高度為 40
- 中的預設值`Padding``Frame`為 20
- 預設值`Spacing``StackLayout`為 6
- 此方法`Device.GetNamedSize`傳回數字字型大小

這些大小不是圖元。 相反,它們是每個平台獨立識別的設備級單位。

## <a name="pixels-points-dps-dips-and-dius"></a>像素、點、dp、DIP 和 DIA

早在蘋果Mac和微軟視窗的歷史,程式師工作單位圖元。 但是,高解析度顯示的出現需要一種更加虛擬化和抽象的螢幕座標方法。 在 Mac 領域,程式師以*單位*為單位工作,傳統上為 1/72 英寸,而 Windows 開發人員則使用基於 1/96 英寸*的設備獨立單元*(DIUs)。

但是,行動裝置通常比桌面螢幕更靠近面部,解析度更高,這意味著可以容忍更大的圖元密度。

面向蘋果iPhone和iPad設備的程式員繼續以*單位為單位*工作,但有160個點英寸。 根據設備的不同,點可能有 1、2 或 3 圖元。

安卓是相似的。 程式師以*與密度無關的圖元*(dps) 單位工作,dps 和圖元之間的關係基於 160 dps 到英寸。

Windows 手機和行動裝置還建立了縮放因素,這意味著接近160台設備的單元英寸。

> [!NOTE]
> Xamarin.Forms 不再支援任何基於 Windows 的手機或行動裝置。

總之,針對手機和平板電腦的 Xamarin.Forms 程式師可以假定所有測量單位都基於以下標準:

- 160 單位到英寸,相當於
- 64 個單位到公分

定義的[`Width`](xref:Xamarin.Forms.VisualElement.Width)`VisualElement`唯讀&ndash;屬性[`Height`](xref:Xamarin.Forms.VisualElement.Height)和屬性的預設"模擬"值為1。 僅當元素的大小並在佈局中容納時,這些屬性才會反映與設備無關的單位中元素的實際大小。 此大小包括元素`Padding`上的任何集,`Margin`但是不包括 。

可視元素在其[`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)`Width``Height`或 已更改時觸發該事件。 [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize)範例使用此事件來顯示程式螢幕的大小。

## <a name="metrical-sizes"></a>公製大小

[**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView)[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)使用[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)並`BoxView`顯示一 英寸高和一釐米寬。

## <a name="estimated-font-sizes"></a>估計字型大小

[**FontSize 範例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes)展示如何使用 160 個單位到英吋規則以點為單位指定字體大小。 使用這種技術的平台之間的視覺一致性優於`Device.GetNamedSize`。

## <a name="fitting-text-to-available-size"></a>將文字擬合到可用大小

使用以下條件計算的`FontSize``Label`a,可以在特定矩形中容納文字塊:

- 行間距為字體大小的 120%(Windows 平臺上為 130%)。
- 平均字元寬度為字體大小的 50%。

[**"估計字型大小"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize)範例範例了此技術。 此程式是在屬性可用之前[`Margin`](xref:Xamarin.Forms.View.Margin)編寫的,因此它[`ContentView`](xref:Xamarin.Forms.ContentView)使用 帶有[`Padding`](xref:Xamarin.Forms.Layout.Padding)一個 設置來類比邊距。

[![估計字型大小的三重螢幕截圖](images/ch05fg07-small.png "文字適合可用大小")](images/ch05fg07-large.png#lightbox "文字適合可用大小")

## <a name="a-fit-to-size-clock"></a>調整大小的時鐘

[**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock)範例示範如何[`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean}))啟動計時器,該計時器在更新時鐘時定期通知應用程式。 字體大小設置為頁面寬度的六分之一,以使顯示盡可能大。

## <a name="accessibility-issues"></a>輔助功能問題

**估計字體大小**程式和**FitToSizeClock**程式都包含一個細微的缺陷:如果使用者更改了 Android 或 Windows 10 Mobile 上的手機輔助功能設定,則該程式不再能夠根據字型大小估計文本的呈現大小。 [**輔助功能測試**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest)示例演示了此問題。

## <a name="empirically-fitting-text"></a>經驗擬合文字

將文本與矩形擬合的另一種方法是經驗計算呈現的文本大小並向上或向下調整文本大小。 書中的程式呼叫[`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))視覺元素以獲取元素所需的大小。 該方法已被棄用,程式應改為呼叫[`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))。

對於`Label`,第一個參數應是容器的寬度(以允許換行),而第二個參數應設置`Double.PositiveInfinity`為使高度不受限制。 [**經驗字體大小**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize)示例演示了此技術。

## <a name="related-links"></a>相關連結

- [第五章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [第五章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [第五章 F# 樣品](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)

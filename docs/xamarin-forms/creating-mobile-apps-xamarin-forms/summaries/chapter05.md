---
title: 第 5 章的摘要。 處理大小
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 71d40136d7c3fc780815471cb822e94a4fa704c5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>第 5 章的摘要。 處理大小

到目前為止已發生 Xamarin.Forms 中的數個大小：

- IOS 的 [狀態] 列的高度為 20
- `BoxView`預設寬度和高度為 40
- 預設值`Padding`中`Frame`為 20
- 預設值`Spacing`上`StackLayout`為 6。
- `Device.GetNamedSize`方法會傳回數值的字型大小

這些大小不是像素。 相反地，它們是每個平台獨立辨識裝置獨立單位。

## <a name="pixels-points-dps-dips-and-dius"></a>像素為單位，點、 dp、 Dip，以及 DIUs

早期的 Apple Mac 和 Microsoft Windows 的記錄，程式設計人員工作以像素為單位。 不過，較高解析度顯示問世需要螢幕座標的多虛擬和抽象方法。 在 Mac 世界中，程式設計人員使用過的單位*點*、 傳統 1/72 英吋，而 Windows 開發人員使用*裝置獨立單位*(DIUs) 根據 1/96 英吋。

行動裝置，不過，通常會保留更接近的圖示，而且有較高的解析度桌面比表示可容許更大的像素密度的螢幕。

以 Apple iPhone 和 iPad 裝置為目標的程式設計人員繼續工作的單位*點*，但有 160 英吋這些點。 根據裝置，可能有 1、 2 或 3 的像素的點。

Android 很類似。 程式設計人員使用的單位*密度無關的像素*(dp)，且 dp 像素為單位之間的關聯性根據 160 dp 每一英吋。

Windows 執行階段也已經建立隱含東西接近 160 英吋的裝置獨立單位的縮放比例。

在 [摘要] 目標手機和平板電腦 Xamarin.Forms 程式設計人員就可以假設所有的度量單位根據下列準則：

- 160 英吋，相當於單位
- 以公分 64 單位

唯讀[ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/)和[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/)所定義的屬性`VisualElement`有 「 模擬 」 值的預設&ndash;1。 已調整和配置中容納項目時，才將這些屬性會反映裝置無關的單位中的項目中的實際大小。 此大小可包含任何`Padding`在元素上設定，但不是`Margin`。

視覺項目就會引發[ `SizeChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/)事件時其`Width`或`Height`已變更。 [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize)範例會使用這個事件來顯示程式的螢幕大小。

## <a name="metrical-sizes"></a>Metrical 大小

[ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView)使用[ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)和[ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/)顯示`BoxView`一英吋高，另一個公分寬。

## <a name="estimated-font-sizes"></a>估計的字型大小

[**浮水印文字**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes)範例示範如何使用 160 單位-至-吋規則來指定字型的大小，以點為單位。 使用這項技術的平台 visual 的一致性是優於`Device.GetNamedSize`。

## <a name="fitting-text-to-available-size"></a>可用的大小調整文字

可以藉由計算符合特定的矩形內的文字區塊`FontSize`的`Label`使用下列準則：

- 行距是 120%字型大小 （130 %windows 平台上)。
- 平均字元寬度是字型大小的 50%。

[ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize)範例會示範這項技術。 此程式已寫入之前[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)屬性是可用的因此它會使用[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)與[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)模擬設定邊界。

[![估計的字型大小的三個螢幕擷取畫面](images/ch05fg07-small.png "可用的大小來容納文字")](images/ch05fg07-large.png#lightbox "文字調整成可用的大小")

## <a name="a-fit-to-size-clock"></a>調整的大小時鐘

[ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock)範例將示範如何使用[ `Device.StartTimer` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.StartTimer/p/System.TimeSpan/System.Func%7BSystem.Boolean%7D/)啟動定期通知應用程式時若要更新的時鐘時間的計時器。 字型大小設頁面寬度的六分之一，讓顯示器一樣大。

## <a name="accessibility-issues"></a>協助工具問題

**EstimatedFontSize**程式和**FitToSizeClock**程式同時包含不易察覺的問題： 如果使用者變更手機上的協助工具設定，在 Android 或 Windows 10 行動裝置，不會再執行程式可以估計大小呈現文字的字型大小為基礎。 [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest)範例將說明此問題。

## <a name="empirically-fitting-text"></a>實證調整文字

以配合文字矩形的另一個方法是實證計算呈現的文字大小，然後向上或向下調整。 中的活頁簿呼叫的程式[ `GetSizeRequest` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/)上視覺化的項目，若要取得的項目所需的大小。 方法已被取代，，和程式應該改為呼叫[ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)。

如`Label`第一個引數應該是 （可允許換行） 容器的寬度，第二個引數應該設定到`Double.PositiveInfinity`讓未受限的高度。 [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize)範例會示範這項技術。



## <a name="related-links"></a>相關連結

- [第 5 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [第 5 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [第 5 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)

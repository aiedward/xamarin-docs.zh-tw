---
title： " Xamarin.Forms 效果" 描述： "效果可讓您自訂每個平臺上的原生控制項，而不需要使用自訂轉譯器的執行。
assetid： 8AF168A7-4CD9-4603-B961-15B8B1543784 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：03/01/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-effects"></a>Xamarin.Forms效應

_Xamarin：表單使用者介面是使用目標平臺的原生控制項來轉譯，讓 Xamarin.Forms 應用程式可以為每個平臺保留適當的外觀與風格。效果可讓您自訂每個平臺上的原生控制項，而不需要使用自訂轉譯器的執行。_

## <a name="introduction-to-effects"></a>[效果簡介](introduction.md)

效果可讓您自訂每個平台上的原生控制項，通常用於小型的樣式變更。 本文提供效果簡介、概述效果與自訂轉譯器之間的界限，並描述 `PlatformEffect` 類別。

## <a name="creating-an-effect"></a>[建立效果](creating.md)

效果會簡化控制項的自訂。 本文示範如何 [`Entry`](xref:Xamarin.Forms.Entry) 在控制項取得焦點時，建立變更控制項背景色彩的效果。

## <a name="passing-parameters-to-an-effect"></a>[傳遞參數給效果](passing-parameters/index.md)

建立可透過參數設定的效果，可讓效果能重複使用。 下列文章會示範如何使用屬性將參數傳遞給效果，並在執行階段變更參數。

## <a name="invoking-events-from-an-effect"></a>[從效果叫用事件](touch-tracking.md)

效果可以叫用事件。 本文章介紹如何建立可實作低層級多點觸控手指追蹤，並對應用程式傳送觸控、移動和釋放訊號的事件。

## <a name="reusable-roundeffect"></a>[可重複使用的 RoundEffect](reusable-roundeffect.md)

RoundEffect 是可重複使用的效果，可以套用至衍生自 VisualElement 的任何控制項，以將控制項轉譯為圓形。 這種效果可以用來建立圓形影像、圓形按鈕或其他迴圈控制項。

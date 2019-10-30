---
title: Xamarin.Forms 效果
description: 效果可讓每個平台的原生控制項不需使用自訂轉譯器實作，即可進行自訂。
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 2de1d1dd065a01bb457ebf03acdc0c01529abf7b
ms.sourcegitcommit: 1242d32b7f072c837005cdee174abe6c0d1d0c68
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73083828"
---
# <a name="xamarinforms-effects"></a>Xamarin.Forms 效果

_Xamarin：表單使用者介面會使用目標平臺的原生控制項來轉譯，讓 Xamarin 應用程式能夠針對每個平臺保留適當的外觀與風格。效果可讓您自訂每個平臺上的原生控制項，而不需要使用自訂轉譯器的執行。_

## <a name="introduction-to-effectsintroductionmd"></a>[效果簡介](introduction.md)

效果可讓您自訂每個平台上的原生控制項，通常用於小型的樣式變更。 本文提供效果簡介、概述效果與自訂轉譯器之間的界限，並描述 `PlatformEffect` 類別。

## <a name="creating-an-effectcreatingmd"></a>[建立效果](creating.md)

效果會簡化控制項的自訂。 本文示範如何在控制項取得焦點時，建立 [`Entry`](xref:Xamarin.Forms.Entry) 控制項中變更背景色彩的效果。

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[傳遞參數給效果](passing-parameters/index.md)

建立可透過參數設定的效果，可讓效果能重複使用。 下列文章會示範如何使用屬性將參數傳遞給效果，並在執行階段變更參數。

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[從效果叫用事件](touch-tracking.md)

效果可以叫用事件。 本文章介紹如何建立可實作低層級多點觸控手指追蹤，並對應用程式傳送觸控、移動和釋放訊號的事件。

## <a name="reusable-roundeffectreusable-roundeffectmd"></a>[可重複使用的 RoundEffect](reusable-roundeffect.md)

RoundEffect 是可重複使用的效果，可以套用至衍生自 VisualElement 的任何控制項，以將控制項轉譯為圓形。 這種效果可以用來建立圓形影像、圓形按鈕或其他迴圈控制項。

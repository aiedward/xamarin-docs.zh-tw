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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73083828"
---
# <a name="xamarinforms-effects"></a>Xamarin.Forms 效果

_Xamarin.Forms 使用者介面使用目標平臺的本機控制件呈現,允許 Xamarin.Forms 應用程式為每個平臺保留適當的外觀。效果允許自定義每個平臺上的本機控件,而無需採用自定義呈現器實現。_

## <a name="introduction-to-effects"></a>[效果簡介](introduction.md)

效果可讓您自訂每個平台上的原生控制項，通常用於小型的樣式變更。 本文提供效果簡介、概述效果與自訂轉譯器之間的界限，並描述 `PlatformEffect` 類別。

## <a name="creating-an-effect"></a>[建立效果](creating.md)

效果會簡化控制項的自訂。 本文演示如何創建效果,當控件獲得焦點時更改[`Entry`](xref:Xamarin.Forms.Entry)控件的背景顏色。

## <a name="passing-parameters-to-an-effect"></a>[傳遞參數給效果](passing-parameters/index.md)

建立可透過參數設定的效果，可讓效果能重複使用。 下列文章會示範如何使用屬性將參數傳遞給效果，並在執行階段變更參數。

## <a name="invoking-events-from-an-effect"></a>[從效果叫用事件](touch-tracking.md)

效果可以叫用事件。 本文章介紹如何建立可實作低層級多點觸控手指追蹤，並對應用程式傳送觸控、移動和釋放訊號的事件。

## <a name="reusable-roundeffect"></a>[可重複使用的 RoundEffect](reusable-roundeffect.md)

RoundEffect 是一種可重用的效果,可應用於從 VisualElement 派生的任何控制項,以將控制件呈現為圓。 此效果可用於創建圓形圖像、圓形按鈕或其他圓形控制件。

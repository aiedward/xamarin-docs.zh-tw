---
title: Xamarin.Forms 效果
description: 效果可讓自訂而不必訴諸於自訂轉譯器實作的每個平台的原生控制項。
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 9d859377c40c6fca07e140c50da46d8f30aaae04
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994454"
---
# <a name="xamarinforms-effects"></a>Xamarin.Forms 效果

_Xamarin.Forms 的使用者介面會使用目標平台，讓 Xamarin.Forms 應用程式保留適當的外觀及操作，每個平台的原生控制項呈現的。效果可讓自訂而不必訴諸於自訂轉譯器實作的每個平台的原生控制項。_

## <a name="introduction-to-effectsintroductionmd"></a>[效果簡介](introduction.md)

效果可讓自訂，每個平台的原生控制項，通常用於小型的樣式變更。 本文簡介如何影響、 概述效果與自訂轉譯器之間的界限，並說明`PlatformEffect`類別。

## <a name="creating-an-effectcreatingmd"></a>[建立的效果](creating.md)

效果簡化自訂控制項。 這篇文章示範如何建立變更的背景色彩的效果[ `Entry` ](xref:Xamarin.Forms.Entry)控制當控制項取得焦點。

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[將參數傳遞的效果](passing-parameters/index.md)

建立透過參數設定的效果，可讓要重複使用的效果。 這些文章會示範如何將參數傳遞給效果，請使用屬性和變更在執行階段參數。

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[叫用事件的影響](touch-tracking.md)

效果可以叫用事件。 這篇文章會示範如何建立實作低層級的多點觸控手指追蹤並傳送信號觸控按下、 移動和版本的應用程式的事件。

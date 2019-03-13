---
title: Xamarin.Forms 效果
description: 效果可讓每個平台的原生控制項不需使用自訂轉譯器實作，即可進行自訂。
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 9d859377c40c6fca07e140c50da46d8f30aaae04
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994454"
---
# <a name="xamarinforms-effects"></a>Xamarin.Forms 效果

_Xamarin.Forms 使用者介面使用目標平台的原生控制項來轉譯，讓 Xamarin.Forms 應用程式保留每個平台適當的外觀及操作。效果可讓每個平台的原生控制項不需使用自訂轉譯器實作，即可進行自訂。_

## <a name="introduction-to-effectsintroductionmd"></a>[效果簡介](introduction.md)

效果可讓您自訂每個平台上的原生控制項，通常用於小型的樣式變更。 本文提供效果簡介、概述效果與自訂轉譯器之間的界限，並描述 `PlatformEffect` 類別。

## <a name="creating-an-effectcreatingmd"></a>[建立效果](creating.md)

效果會簡化控制項的自訂。 本文示範如何在控制項取得焦點時，建立 [`Entry`](xref:Xamarin.Forms.Entry) 控制項中變更背景色彩的效果。

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[傳遞參數給效果](passing-parameters/index.md)

建立可透過參數設定的效果，可讓效果能重複使用。 下列文章會示範如何使用屬性將參數傳遞給效果，並在執行階段變更參數。

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[從效果叫用事件](touch-tracking.md)

效果可以叫用事件。 本文章介紹如何建立可實作低層級多點觸控手指追蹤，並對應用程式傳送觸控、移動和釋放訊號的事件。

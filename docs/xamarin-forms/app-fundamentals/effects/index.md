---
title: "效果"
description: "使用允許保留每個平台適當的外觀及操作 Xamarin.Forms 應用程式的目標平台的原生控制項轉譯 Xamarin.Forms 使用者介面。 效果可讓自訂而不必訴諸於自訂轉譯器實作的每個平台的原生控制項。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: dd8b98982052e15744bf67ece6a25cd940a9875a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="effects"></a>效果

_使用允許保留每個平台適當的外觀及操作 Xamarin.Forms 應用程式的目標平台的原生控制項轉譯 Xamarin.Forms 使用者介面。效果可讓自訂而不必訴諸於自訂轉譯器實作的每個平台的原生控制項。_

## <a name="introduction-to-effectsintroductionmd"></a>[效果簡介](introduction.md)

效果可讓自訂，每個平台的原生控制項，通常用於小型的樣式變更。 本文介紹效果、 概述效果和自訂轉譯器之間的界限，並說明`PlatformEffect`類別。

## <a name="creating-an-effectcreatingmd"></a>[建立效果](creating.md)

效果簡化自訂控制項。 本文示範如何建立變更的背景色彩的效果[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制當控制項取得焦點。

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[將參數傳遞至效果](passing-parameters/index.md)

建立透過參數設定的效果，可讓要重複使用的效果。 這些文章將示範如何將參數傳遞給效果，請使用屬性和變更在執行階段參數。

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[叫用的作用中的事件](touch-tracking.md)

效果可以叫用事件。 本文示範如何建立實作低層級的多點觸控手指追蹤，並向發出信號觸控按、 移動和版本的應用程式的事件。
---
title: Xamarin.Forms 手勢
description: 本指南說明如何使用 Xamarin.Forms 手勢辨識器，來偵測 Xamarin.Forms 應用程式中使用者與檢視的互動。
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61184567"
---
# <a name="xamarinforms-gestures"></a>Xamarin.Forms 手勢

_Xamarin.Forms 手勢辨識器可用於偵測 Xamarin.Forms 應用程式中使用者與檢視的互動。_

Xamarin.Forms[`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer)類支援在實例[`View`](xref:Xamarin.Forms.View)上 點擊、捏合、平移和輕掃手勢。

## <a name="adding-a-tap-gesture-recognizer"></a>[新增點選手勢辨識器](tap.md)

點擊手勢用於點擊檢測,並且通過[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)類識別。

## <a name="adding-a-pinch-gesture-recognizer"></a>[新增捏合手勢辨識器](pinch.md)

捏合手勢用於執行互動式縮放,並且通過[`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer)類識別。

## <a name="adding-a-pan-gesture-recognizer"></a>[新增平移手勢辨識器](pan.md)

平移手勢用於檢測手指在螢幕上的移動並將該移動應用於內容,並且通過[`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer)類進行識別。

## <a name="adding-a-swipe-gesture-recognizer"></a>[新增撥動手勢辨識器](swipe.md)

撥動手勢會在手指以水平或垂直方向橫跨畫面移動時發生，且通常用來起始內容的瀏覽。 使用[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer)類識別滑動手勢。

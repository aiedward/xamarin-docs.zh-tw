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
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106178"
---
# <a name="xamarinforms-gestures"></a>Xamarin.Forms 手勢

_Xamarin.Forms 手勢辨識器可用於偵測 Xamarin.Forms 應用程式中使用者與檢視的互動。_

Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) 類別支援 [`View`](xref:Xamarin.Forms.View) 執行個體上的點選、捏合、平移和撥動等手勢。

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[新增點選手勢辨識器](tap.md)

點選手勢用於點選偵測，由 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 類別辨識。

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[新增捏合手勢辨識器](pinch.md)

捏合手勢用於執行互動式縮放，由 [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) 類別辨識。

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[新增平移手勢辨識器](pan.md)

平移手勢用於偵測手指在螢幕上的移動，並會將該動作套用至內容，由 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 類別辨識。

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[新增撥動手勢辨識器](swipe.md)

撥動手勢會在手指以水平或垂直方向橫跨畫面移動時發生，且通常用來起始內容的瀏覽。 撥動手勢由 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 類別辨識。

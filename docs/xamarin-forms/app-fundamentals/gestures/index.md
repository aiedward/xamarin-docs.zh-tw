---
title: Xamarin.Forms動作
description: 本指南說明手勢辨識器如何 Xamarin.Forms 用來偵測應用程式中與 views 的使用者互動 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e1e93f74ab8ef6d63213a8fbdc7ec45a794cf55
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137873"
---
# <a name="xamarinforms-gestures"></a>Xamarin.Forms動作

_筆勢辨識器可以用來偵測應用程式中與 views 的使用者互動 Xamarin.Forms 。_

Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) 類別支援實例上的點按、縮小、平移和滑動手勢 [`View`](xref:Xamarin.Forms.View) 。

## <a name="adding-a-tap-gesture-recognizer"></a>[新增點選手勢辨識器](tap.md)

點按手勢用於點一下偵測，並可透過類別辨識 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 。

## <a name="adding-a-pinch-gesture-recognizer"></a>[新增捏合手勢辨識器](pinch.md)

縮小手勢是用來執行互動式縮放，並可使用類別來辨識 [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) 。

## <a name="adding-a-pan-gesture-recognizer"></a>[新增平移手勢辨識器](pan.md)

移動流覽手勢是用來偵測螢幕上的手指移動，並將該動作套用至內容，並使用類別來辨識 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 。

## <a name="adding-a-swipe-gesture-recognizer"></a>[新增撥動手勢辨識器](swipe.md)

撥動手勢會在手指以水平或垂直方向橫跨畫面移動時發生，且通常用來起始內容的瀏覽。 您可以使用類別來辨識滑動手勢 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 。

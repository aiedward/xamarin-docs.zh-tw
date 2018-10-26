---
title: Xamarin.Forms 筆勢
description: 本指南說明如何使用 Xamarin.Forms 筆勢辨識器，來偵測在 Xamarin.Forms 應用程式中的使用者與檢視互動。
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106178"
---
# <a name="xamarinforms-gestures"></a>Xamarin.Forms 筆勢

_筆勢辨識器可用來偵測在 Xamarin.Forms 應用程式中的使用者與檢視互動。_

Xamarin.Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer)類別支援上的點選、 捏合、 取景位置調整及撥動手勢[ `View` ](xref:Xamarin.Forms.View)執行個體。

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[新增 tap 的筆勢辨識器](tap.md)

點選手勢來點選偵測和辨識的[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)類別。

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[新增捏合筆勢辨識器](pinch.md)

捏合手勢用來執行互動式縮放並與辨識[ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer)類別。

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[新增取景位置調整的筆勢辨識器](pan.md)

移動瀏覽軌跡用於偵測的手指在螢幕上移動，並將該動作套用至內容，和與辨識[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)類別。

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[新增揮擊筆勢辨識器](swipe.md)

撥動手勢發生於手指移動以水平或垂直方向，畫面上，且通常用來起始瀏覽內容。 與辨識撥動手勢[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)類別。

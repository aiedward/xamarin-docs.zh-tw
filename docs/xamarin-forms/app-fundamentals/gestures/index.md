---
title: Xamarin.Forms動作
description: 本指南說明手勢辨識器如何 Xamarin.Forms 用來偵測應用程式中與 views 的使用者互動 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/04/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7528afd0971cf06eb69df4ed7c08c3fd6dcc9e22
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917827"
---
# <a name="no-locxamarinforms-gestures"></a>Xamarin.Forms動作

_筆勢辨識器可以用來偵測應用程式中與 views 的使用者互動 Xamarin.Forms 。_

Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) 類別支援在實例上進行點按、縮小、平移、滑動和拖放手勢 [`View`](xref:Xamarin.Forms.View) 。

## <a name="add-a-tap-gesture-recognizer"></a>[新增攻絲手勢辨識器](tap.md)

點按手勢用於點一下偵測，並可透過類別辨識 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 。

## <a name="add-a-pinch-gesture-recognizer"></a>[新增縮小的手勢辨識器](pinch.md)

縮小手勢是用來執行互動式縮放，並可使用類別來辨識 [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) 。

## <a name="add-a-pan-gesture-recognizer"></a>[新增 pan 手勢辨識器](pan.md)

移動流覽手勢是用來偵測螢幕上的手指移動，並將該動作套用至內容，並使用類別來辨識 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 。

## <a name="add-a-swipe-gesture-recognizer"></a>[新增滑動手勢辨識器](swipe.md)

撥動手勢會在手指以水平或垂直方向橫跨畫面移動時發生，且通常用來起始內容的瀏覽。 您可以使用類別來辨識滑動手勢 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 。

## <a name="add-a-drag-and-drop-gesture-recognizer"></a>[新增拖放手勢辨識器](drag-and-drop.md)

拖放手勢可讓您使用連續手勢，將專案及其相關聯的資料封裝從一個螢幕位置拖曳至另一個位置。 拖曳手勢可使用類別來辨識 `DragGestureRecognizer` ，而卸載手勢則可使用類別來辨識 `DropGestureRecognizer` 。

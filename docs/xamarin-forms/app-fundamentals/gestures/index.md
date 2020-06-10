---
title： " Xamarin.Forms 手勢" 描述： "本指南說明筆勢辨識器如何 Xamarin.Forms 用來偵測應用程式中與 views 的使用者互動 Xamarin.Forms 。
assetid： 0E197A51-2304-4C09-A710-C7FF24A89F15 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：07/25/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
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

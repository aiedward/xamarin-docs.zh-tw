---
title: 第14章摘要。 絕對版面配置
description: 使用 Xamarin.表單創建行動應用程式:第 14 章摘要。 絕對版面配置
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771136"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>第14章摘要。 絕對版面配置

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

喜歡`StackLayout`[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout),派生`Layout<View>`和`Children`繼承 屬性。 `AbsoluteLayout`實現一個佈局系統,要求程式師指定其子級的位置,以及可以選擇其大小。 位置由子級的左上角相對於設備無關單元的`AbsoluteLayout`左上角指定。 `AbsoluteLayout`還實現了比例定位和大小調整功能。

`AbsoluteLayout`應被視為一種特殊用途的布局系統,僅當程式師可以對子級(例如`BoxView`元素)施加大小或元素的大小不會影響其他子級的位置時,才使用。 屬性`HorizontalOptions``VerticalOptions`對子項目沒有影響`AbsoluteLayout`。

本章還介紹了*附加可綁定屬性*的重要功能,這些屬性允許將一個類中定義的屬性(在`AbsoluteLayout`本例 中)附加到另一個`AbsoluteLayout`類(的 子項)。

## <a name="absolutelayout-in-code"></a>代碼中的絕對佈局

可以使用標準`Children``AbsoluteLayout`[`Add`](xref:System.Collections.Generic.ICollection`1.Add*)方法將子項新增到集合中,但也`AbsoluteLayout`提供一個擴[`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*)充方法,允許您指定[`Rectangle`](xref:Xamarin.Forms.Rectangle)。 另[`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*)一種方法[`Point`](xref:Xamarin.Forms.Point)只需要 一個,在這種情況下,子級是不受約束的,並且大小本身。

可以使用[建構函數](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double))創建`Rectangle`一個值,該值需要&mdash;前兩個值,前兩個值指示子項相對於其父級的左上角的位置,后兩個值指示子級的大小。 或您可以使用需要與`Point`值的[`Size`](xref:Xamarin.Forms.Size)[建構函數](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size))。

這些方法`Add`在[**絕對Demo(**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo)使用`BoxView``Rectangle`值定位元素)`Label``Point`和僅使用 值的元素中演示。

[**棋盤固定**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed)示例使用`BoxView`32 個元素創建棋盤模式。 該程式為`BoxView`元素提供 35 個單位平方的硬編碼大小。 `AbsoluteLayout`有`HorizontalOptions``VerticalOptions`其`LayoutOptions.Center`和設置為 ,`AbsoluteLayout`這將導致 的總大小為 280 個單位的正方形。

## <a name="attached-bindable-properties"></a>附加的可結合屬性

也可以設置位置,並可以選擇,在使用靜態方法`AbsoluteLayout``Children`[`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle))將 子級添加到集合后的大小。 第一個論點是孩子;第二個是`Rectangle`物件。 通過將寬度和高度值設置為[`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize)常量,可以指定子級水準調整和/或垂直調整自身大小。

[**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic)範例`AbsoluteLayout``ContentView``SizeChanged``AbsoluteLayout.SetLayoutBounds`將  

`AbsoluteLayout`定義的附加可綁定屬性是名為`BindableProperty`[`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)類型的靜態唯讀欄位。 靜態`AbsoluteLayout.SetLayoutBounds`方法是通過調`SetValue`用 帶`AbsoluteLayout.LayoutBoundsProperty`的子 級 實現的。 子項包含一個字典,其中存儲附加的可綁定屬性及其值。 在佈局期間,`AbsoluteLayout`可以通過[`AbsoluteLayout.GetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject))調用 獲得該值,該`GetValue`值是使用 調用實現的。

## <a name="proportional-sizing-and-positioning"></a>大小調整與定位

`AbsoluteLayout`實現比例大小調整和定位功能。 類使用相關的靜態方法和[`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)[`AbsoluteLayout.SetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags))定義第二個附加的可綁定屬性[`AbsoluteLayout.GetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject))。

的`AbsoluteLayout.SetLayoutFlags`參數`AbsoluteLayout.GetLayoutFlags`與的傳回[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)值是類型的值,具有以下成員的枚舉:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None)(等於 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional)(1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional)(2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional)(3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional)(4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional)(8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional)(12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All)(\xFFFFFFFFFF)

您可以將它們與 C# 位元或運算符組合。

設置這些標誌后,用於定位子級`Rectangle`和大小的布局邊界結構的某些屬性將按比例解釋。

設定`WidthProportional`標誌時`Width`, 值為 1 表示子級的寬`AbsoluteLayout`度與 相同。 對於高度,也使用了類似的方法。

比例定位會考慮大小。 設置`XProportional`標誌時`X``Rectangle`, 布局邊界的屬性成比例。 值 0 表示孩子的左邊緣`AbsoluteLayout`位於 的 左邊緣,但位置為 1`AbsoluteLayout`表示孩子的右邊緣`AbsoluteLayout`位於的 右邊緣,而不是超出的右邊緣。如您所料。 屬性`X`0.5 將子級水準居`AbsoluteLayout`中。

[**棋盤比例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional)樣本演示了比例大小調整和定位的使用。

## <a name="working-with-proportional-coordinates"></a>使用比例座標

有時,與 在中實現比例定位的方式不同,更容易考慮比例`AbsoluteLayout`定位 。 您可能更喜歡使用比例座標,`X`其中 1 的屬性將孩子的左邊緣(而不是右邊緣)`AbsoluteLayout`定位在 的 右邊緣。

這種替代定位方案可以稱為"分數子座標"。 您可以將從小數子座標轉換為使用以下公式所需的`AbsoluteLayout`佈局邊界:

佈局繫結.X = (分數兒童座標.X / (1 - 佈局繫結.Width))

佈局繫結.Y = (分數兒童座標.Y / (1 - 佈局繫結.高度))

[**比例座標Calc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc)示例演示了這一點。

## <a name="absolutelayout-and-xaml"></a>絕對佈局和XAML

可以在`AbsoluteLayout`XAML 中使用`AbsoluteLayout`,並在`AbsoluteLayout.LayoutBounds`和的子 級上設置附加的可`AbsoluteLayout.LayoutFlags`綁定屬性。 這體現在[**絕對XamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo)和[**棋盤Xaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml)樣品中。 後一程式包含 32`BoxView`個元素`Style`,但`AbsoluteLayout.LayoutFlags`使用包含屬性 的隱式元素將標記保持在最小值。

XAML 中由類名稱、點和屬性名稱組成的屬性*始終是*附加的可綁定屬性。

## <a name="overlays"></a>重疊

您可以使用`AbsoluteLayout`建構覆蓋 ,該*疊加*與其他控制項一起覆蓋頁面,可能是為了保護使用者不與頁面上的正常控制項進行互動。

[**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay)範例展示此技術,並展示[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)了顯示程式完成任務的程度。

## <a name="some-fun"></a>一些樂趣

[**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock)示例透過類比的 5x7 點矩陣顯示當前時間。 每個點的大小為`BoxView`(有 228 個)大小`AbsoluteLayout`,並位於 上。

[![點矩陣時鐘的三重螢幕截圖](images/ch14fg08-small.png "點矩陣時鐘")](images/ch14fg08-large.png#lightbox "點矩陣時鐘")

[**BounceText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText)程式為`Label`兩 個物件設置動畫,以便在螢幕上水準和垂直地反彈。

## <a name="related-links"></a>相關連結

- [第14章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [第14章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)

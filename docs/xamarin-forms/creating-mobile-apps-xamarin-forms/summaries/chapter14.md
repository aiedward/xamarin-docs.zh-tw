---
title: 第14章的摘要。 絕對版面配置
description: 建立 Mobile Apps 與 Xamarin.Forms ：第14章的摘要。 絕對版面配置
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 09011647428e2af1bdfcdb2f9def8da64ef84144
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374835"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>第14章的摘要。 絕對版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

例如 `StackLayout` ， [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 衍生自 `Layout<View>` 並繼承 `Children` 屬性。 `AbsoluteLayout` 執行需要程式設計人員指定其子系位置的版面配置系統，並選擇性地指定其大小。 位置是由相對於裝置獨立單位之左上角的子系左上角所指定 `AbsoluteLayout` 。 `AbsoluteLayout` 也會執行依比例調整的定位和調整大小功能。

`AbsoluteLayout` 應該視為特殊用途的版面配置系統，只有在程式設計人員可以在子系上強加 (例如專案 `BoxView`) 或元素的大小不會影響其他子系的定位時，才會使用。 `HorizontalOptions`和 `VerticalOptions` 屬性不會影響的子系 `AbsoluteLayout` 。

本章也介紹附加的可系結 *屬性* 的重要功能，此功能可讓一個類別中定義的屬性 (在此案例中 `AbsoluteLayout`) 附加至另一個類別 () 的子系 `AbsoluteLayout` 。

## <a name="absolutelayout-in-code"></a>程式碼中的 AbsoluteLayout

您可以使用標準方法將子系加入至的 `Children` 集合 `AbsoluteLayout` [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) ，但也會 `AbsoluteLayout` 提供可 [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) 讓您指定的擴充方法 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 。 另一個 [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) 方法只需要 [`Point`](xref:Xamarin.Forms.Point) ，在這種情況下，子系不受限制且大小本身。

您可以 `Rectangle` 使用需要四個值的函 [式來建立](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) 值，其中 &mdash; 前兩個值表示子系相對於其父系的左上角位置，而第二個則表示子系的大小。 或者，您可以使用 [函式] (x： Xamarin.Forms 。23ctor (Xamarin.Forms 。點： Xamarin.Forms 。需要和值的) # A3 大小 `Point` [`Size`](xref:Xamarin.Forms.Size) 。

這些 `Add` 方法會在 [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo)中示範，這些方法會 `BoxView` 使用值來定位元素 `Rectangle` ，以及 `Label` 只使用值的元素 `Point` 。

[**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed)範例會使用 32 `BoxView` 元素來建立 chessboard 模式。 此程式會為 `BoxView` 元素提供硬式編碼大小的35單位平方。 `AbsoluteLayout`會將其 `HorizontalOptions` 和 `VerticalOptions` 設定為 `LayoutOptions.Center` ，使 `AbsoluteLayout` 大小總計為280個單位的正方形。

## <a name="attached-bindable-properties"></a>附加的可系結屬性

您也可以 `AbsoluteLayout` `Children` 使用靜態方法 [ `AbsoluteLayout.SetLayoutBounds` ] (x：，設定位置，以及選擇性地將它加入至集合之後的子系大小 Xamarin.Forms 。AbsoluteLayout. SetLayoutBounds (Xamarin.Forms 。BindableObject， Xamarin.Forms 。矩形) # A3。 第一個引數是子系;第二個是 `Rectangle` 物件。 您可以藉由將寬度和高度值設定為常數，指定子系的水準和/或垂直大小  [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) 。

[**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic)範例會將放 `AbsoluteLayout` 在中， `ContentView` 並 `SizeChanged` 以處理常式呼叫 `AbsoluteLayout.SetLayoutBounds` 所有子系，讓它們盡可能大。  

定義的附加可系結屬性 `AbsoluteLayout` 是名為之型別的靜態唯讀 `BindableProperty` 欄位 [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 。 靜態 `AbsoluteLayout.SetLayoutBounds` 方法是藉由在子系上呼叫來執行 `SetValue` `AbsoluteLayout.LayoutBoundsProperty` 。 子系包含字典，其中附加的可系結屬性及其值會儲存在其中。 在版面配置期間， `AbsoluteLayout` 可以藉由呼叫 [ `AbsoluteLayout.GetLayoutBounds` ] (x：來取得該值 Xamarin.Forms 。AbsoluteLayout. GetLayoutBounds (Xamarin.Forms 。BindableObject) # A3，可透過呼叫來執行 `GetValue` 。

## <a name="proportional-sizing-and-positioning"></a>比例調整大小和定位

`AbsoluteLayout` 實行比例調整大小和定位功能。 類別會定義第二個附加的可系結屬性， [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 並使用相關的靜態方法 [ `AbsoluteLayout.SetLayoutFlags` ] (x： Xamarin.Forms 。AbsoluteLayout. SetLayoutFlags (Xamarin.Forms 。BindableObject， Xamarin.Forms 。AbsoluteLayoutFlags) # A3 和 [ `AbsoluteLayout.GetLayoutFlags` ] (x： Xamarin.Forms 。AbsoluteLayout. GetLayoutFlags (Xamarin.Forms 。BindableObject) # A7。

的引數 `AbsoluteLayout.SetLayoutFlags` 和傳回值 `AbsoluteLayout.GetLayoutFlags` 是類型的值 [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) ，具有下列成員的列舉：

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (等於 0) 
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1) 
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2) 
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3) 
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4) 
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8) 
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12) 
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) ( \xFFFFFFFF) 

您可以將這些與 c # 位 OR 運算子結合在一起。

設定這些旗標之後， `Rectangle` 用來定位和調整大小子系之配置界限結構的某些屬性會依比例進行轉譯。

`WidthProportional`設定旗標時，值為 `Width` 1 表示子系與的寬度相同 `AbsoluteLayout` 。 高度也會使用類似的方法。

比例的定位會將大小納入考慮。 `XProportional`設定旗標時，配置 `X` 界限的屬性 `Rectangle` 為比例。 值為0表示子系的左邊緣位於的左邊緣 `AbsoluteLayout` ，而1的位置則表示子系的右邊緣位於的右邊緣 `AbsoluteLayout` ，而不是您預期的右邊緣 `AbsoluteLayout` 。 `X`0.5 的屬性會將子系水準放在中 `AbsoluteLayout` 。

[**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional)範例會示範如何使用比例調整大小和定位。

## <a name="working-with-proportional-coordinates"></a>使用比例座標

有時候，比較簡單的方式是將非比例的定位與在中的實作為方式不同 `AbsoluteLayout` 。 您可能會想要使用比例座標，其中 `X` 1 的屬性會將子系的左邊緣 (，而不是右邊緣) 的右邊緣 `AbsoluteLayout` 。

這個替代定位配置可以稱為「分數子座標」。 您可以使用下列公式，從分數子座標轉換為所需的版面配置界限 `AbsoluteLayout` ：

layoutBounds. X = (fractionalChildCoordinate. X/ (1-layoutBounds. Width) # A3

layoutBounds. Y = (fractionalChildCoordinate. Y/ (1-layoutBounds. Height) # A3

[**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc)範例會示範這一點。

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout 和 XAML

您可以 `AbsoluteLayout` 在 XAML 中使用，並 `AbsoluteLayout` 使用和的屬性值，在的子系上設定附加的可系結屬性 `AbsoluteLayout.LayoutBounds` `AbsoluteLayout.LayoutFlags` 。 這會在 [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) 和 [**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) 範例中示範。 後者的套裝程式含32個 `BoxView` 元素，但 `Style` 會使用包含屬性的隱含， `AbsoluteLayout.LayoutFlags` 讓標記保持最小。

XAML 中包含類別名稱、點和屬性名稱的屬性 *一律* 是附加的可系結屬性。

## <a name="overlays"></a>重疊

您可以使用 `AbsoluteLayout` 來建立覆 *overlay* 迭，其中涵蓋具有其他控制項的頁面，或許是為了保護使用者與頁面上的一般控制項互動。

[**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay)範例會示範這項技術，並示範如何 [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) 顯示程式已完成工作的範圍。

## <a name="some-fun"></a>有趣的

[**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock)範例會顯示目前時間與模擬的5x7 點矩陣顯示。 每個點都是 `BoxView` (有228的) 大小並置於上 `AbsoluteLayout` 。

[![點矩陣時鐘的三重螢幕擷取畫面](images/ch14fg08-small.png "點矩陣時鐘")](images/ch14fg08-large.png#lightbox "點矩陣時鐘")

[**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText)程式會在螢幕上繪製兩個物件的動畫 `Label` ，以水準和垂直方向彈跳。

## <a name="related-links"></a>相關連結

- [第14章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [第14章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolutelayout.md)
- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)

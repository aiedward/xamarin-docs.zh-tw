---
title: 第14章的摘要。 絕對版面配置
description: 使用 Xamarin 建立 Mobile Apps：第14章的摘要。 絕對版面配置
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771136"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>第14章的摘要。 絕對版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

如同 `StackLayout`， [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)衍生自 `Layout<View>`，並繼承 `Children` 屬性。 `AbsoluteLayout` 會執行配置系統，要求程式設計人員指定其子系的位置，並選擇性地指定其大小。 位置是由子系的左上角指定，相對於裝置獨立單位中 `AbsoluteLayout` 的左上角。 `AbsoluteLayout` 也會實行比例定位和調整大小功能。

`AbsoluteLayout` 應該被視為特殊用途的版面配置系統，只有在程式設計人員可以在子系（例如，`BoxView` 元素）上施加大小，或當元素的大小不影響其他子系的位置時，才會使用這個配置系統。 `HorizontalOptions` 和 `VerticalOptions` 屬性不會影響 `AbsoluteLayout`的子系。

本章也介紹附加可系結*屬性*的重要功能，可讓您在一個類別中定義的屬性（在此案例中 `AbsoluteLayout`）附加至另一個類別（`AbsoluteLayout`的子系）。

## <a name="absolutelayout-in-code"></a>程式碼中的 AbsoluteLayout

您可以使用標準[`Add`](xref:System.Collections.Generic.ICollection`1.Add*)方法，將子系加入至 `AbsoluteLayout` 的 `Children` 集合，但 `AbsoluteLayout` 也會提供擴充的[`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*)方法，讓您指定[`Rectangle`](xref:Xamarin.Forms.Rectangle)。 另一個[`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*)方法只需要一個[`Point`](xref:Xamarin.Forms.Point)，在此情況下，子系不受限制，而且大小本身。

您可以使用需要四個值的函[式來建立](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double))`Rectangle` 值，&mdash; 前兩個表示子系相對於其父系的位置，而第二個則表示子系的大小。 或者，您可以使用需要 `Point` 和[`Size`](xref:Xamarin.Forms.Size) [值的函](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size))式。

這些 `Add` 的方法會在[**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo)中示範，這會使用 `Rectangle` 值來放置 `BoxView` 元素，以及僅使用 `Point` 值的 `Label` 元素。

[**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed)範例會使用 32 `BoxView` 元素來建立 chessboard 模式。 程式會為 `BoxView` 元素提供硬式編碼大小為35單位的正方形。 `AbsoluteLayout` 的 `HorizontalOptions` 和 `VerticalOptions` 設定為 `LayoutOptions.Center`，這會導致 `AbsoluteLayout` 的總大小為280單位的平方。

## <a name="attached-bindable-properties"></a>附加的可系結屬性

您也可以使用靜態方法[`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle))，在將 `AbsoluteLayout` 的子系加入至 `Children` 集合之後，設定位置，並選擇性地設定其子系的大小。 第一個引數是子系;第二個是 `Rectangle` 物件。 您可以藉由將 width 和 height 值設定為[`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize)常數，指定子大小本身的水準和/或垂直。

[**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic)範例會使用 `SizeChanged` 處理常式將 `AbsoluteLayout` 放在 `ContentView` 中，以在所有子系上呼叫 `AbsoluteLayout.SetLayoutBounds`，使其盡可能大。  

`AbsoluteLayout` 定義的附加可系結屬性是名為[`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)`BindableProperty` 類型的靜態唯讀欄位。 靜態 `AbsoluteLayout.SetLayoutBounds` 方法是藉由在子系上透過 `AbsoluteLayout.LayoutBoundsProperty`呼叫 `SetValue` 來執行。 子系包含字典，其中儲存了附加的可系結屬性和其值。 在版面配置期間，`AbsoluteLayout` 可以藉由呼叫[`AbsoluteLayout.GetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject))（以 `GetValue` 呼叫來執行）來取得該值。

## <a name="proportional-sizing-and-positioning"></a>比例調整大小和位置

`AbsoluteLayout` 會實行比例調整大小與定位功能。 類別會定義第二個附加的可系結屬性[`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)，並[`AbsoluteLayout.SetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags))和[`AbsoluteLayout.GetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject))相關的靜態方法。

`AbsoluteLayout.SetLayoutFlags` 的引數和 `AbsoluteLayout.GetLayoutFlags` 的傳回值是[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)類型的值，這是具有下列成員的列舉：

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) （等於0）
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) （1）
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) （2）
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) （3）
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) （4）
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) （8）
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) （12）
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) （\xFFFFFFFF）

您可以將C#這些與位 or 運算子結合。

設定這些旗標後，用來定位和調整子系的 `Rectangle` 版面配置界限結構的某些屬性會按比例轉譯。

設定 `WidthProportional` 旗標時，`Width` 值1表示子系與 `AbsoluteLayout`的寬度相同。 高度會使用類似的方法。

比例定位會將大小列入考慮。 設定 `XProportional` 旗標時，`Rectangle` 配置界限的 `X` 屬性會成比例。 值為0表示子系的左邊緣位於 `AbsoluteLayout`的左邊緣，而位置為1則表示子系的右邊緣位於 `AbsoluteLayout`的右邊緣，而不是超出您預期的右 `AbsoluteLayout` 邊緣。 0\.5 的 `X` 屬性會在 `AbsoluteLayout`中水準置中子系。

[**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional)範例示範如何使用比例調整大小和位置。

## <a name="working-with-proportional-coordinates"></a>使用比例座標

有時候，比起在 `AbsoluteLayout`中的執行方式，更容易考慮比例定位。 您可能偏好使用比例座標，其中 `X` 屬性1會將子系的左邊緣（而不是右邊緣）放置在 `AbsoluteLayout`的右邊緣。

這個替代的定位配置可以稱為「分數子座標」。 您可以使用下列公式，從小數子座標轉換成 `AbsoluteLayout` 所需的版面配置界限：

layoutBounds. X = （fractionalChildCoordinate. X/（1-layoutBounds. Width））

layoutBounds。 Y = （fractionalChildCoordinate. Y/（1-layoutBounds Height））

[**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc)範例會示範這種情況。

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout 和 XAML

您可以使用 XAML 中的 `AbsoluteLayout`，並使用 `AbsoluteLayout.LayoutBounds` 和 `AbsoluteLayout.LayoutFlags`的屬性值，在 `AbsoluteLayout` 的子系上設定附加的可系結屬性。 這會在[**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo)和[**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml)範例中示範。 後面的套裝程式含 32 `BoxView` 元素，但使用包含 `AbsoluteLayout.LayoutFlags` 屬性的隱含 `Style`，以將標記保持最小值。

XAML 中由類別名稱、點和屬性名稱所組成的屬性，*一律*是附加的可系結屬性。

## <a name="overlays"></a>重疊

您可以使用 `AbsoluteLayout` 來建立覆*迭，其中*涵蓋含有其他控制項的頁面，可能會保護使用者與頁面上的一般控制項的互動。

[**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay)範例會示範這項技術，同時也示範[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)，其中顯示程式已完成工作的範圍。

## <a name="some-fun"></a>有趣

[**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock)範例會顯示目前時間與模擬的5x7 點矩陣顯示。 每個點都是 `BoxView` （其中有228）大小，並置於 `AbsoluteLayout`上。

[![點矩陣時鐘的三重螢幕擷取畫面](images/ch14fg08-small.png "點矩陣時鐘")](images/ch14fg08-large.png#lightbox "點矩陣時鐘")

[**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText)程式會在螢幕上以動畫方式繪製兩個 `Label` 物件，以水準和垂直方式跳動。

## <a name="related-links"></a>相關連結

- [第14章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [第14章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)

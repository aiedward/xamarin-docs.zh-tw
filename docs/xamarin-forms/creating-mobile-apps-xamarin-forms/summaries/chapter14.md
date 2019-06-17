---
title: 第 14 章的摘要。 絕對版面配置
description: 使用 Xamarin.Forms 建立行動應用程式：第 14 章的摘要。 絕對版面配置
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 7371f134944d7492e51aa2d02247c0ab48345a47
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334271"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>第 14 章的摘要。 絕對版面配置

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

像是`StackLayout`， [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout)衍生自`Layout<View>`並繼承`Children`屬性。 `AbsoluteLayout` 會實作需要程式設計師指定的位置及其子系和 （選擇性） 其大小的版面配置系統。 指定相對於左上角的子系的左上角位置`AbsoluteLayout`以與裝置無關單位。 `AbsoluteLayout` 也會實作的調和間距定位和調整大小功能。

`AbsoluteLayout` 應該被視為特殊用途的版面配置系統程式設計師可以強制設定大小的子系時，才使用 (比方說，`BoxView`項目) 或當此項目的大小並不會影響其他子系的位置。 `HorizontalOptions`並`VerticalOptions`屬性不影響的子系`AbsoluteLayout`。

本章也導入了重要的功能*附加屬性可繫結*可讓您在一個類別中定義的屬性 (在此情況下`AbsoluteLayout`) 附加至另一個類別 (子系`AbsoluteLayout`)。

## <a name="absolutelayout-in-code"></a>在程式碼中的 AbsoluteLayout

您可以加入子系`Children`的集合`AbsoluteLayout`使用標準[ `Add` ](xref:System.Collections.Generic.ICollection`1.Add*)方法，但`AbsoluteLayout`也會提供擴充[ `Add` ](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*)可讓您指定的方法[ `Rectangle` ](xref:Xamarin.Forms.Rectangle)。 另一個[ `Add` ](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*)方法僅需要[ `Point` ](xref:Xamarin.Forms.Point)，子系的不受限制在此情況下，並調整大小本身。

您可以建立`Rectangle`具有值[建構函式](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double))需要四個值&mdash;前兩個指出相對於父代的子系的左上角的位置和表示兩個子系的大小。 或者您可以使用[建構函式](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size))需要`Point`並[ `Size` ](xref:Xamarin.Forms.Size)值。

這些`Add`中示範的方法[ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo)，哪些位置`BoxView`項目使用`Rectangle`的值，和`Label`使用剛項目`Point`值。

[ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed)範例會使用 32`BoxView`棋盤圖樣的項目。 計劃可以讓`BoxView`35 單位正方形的硬式編碼的項目大小。 `AbsoluteLayout`有其`HorizontalOptions`並`VerticalOptions`設定為`LayoutOptions.Center`，會造成`AbsoluteLayout`有 280 單位正方形的大小總計。

## <a name="attached-bindable-properties"></a>附加的可繫結屬性

您也可設定的位置和 （選擇性） 的子系的大小`AbsoluteLayout`新增至後`Children`使用的靜態方法的集合[ `AbsoluteLayout.SetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle))。 第一個引數是子系;第二個是`Rectangle`物件。 您可以指定的子系本身水平及/或垂直的大小將寬度和高度值設定為[ `AbsoluteLayout.AutoSize` ](xref:Xamarin.Forms.AbsoluteLayout.AutoSize)常數。

[ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic)範例放`AbsoluteLayout`中`ContentView`具有`SizeChanged`處理常式呼叫`AbsoluteLayout.SetLayoutBounds`使其變成最大的所有子系。  

附加的可繫結屬性，`AbsoluteLayout`定義是靜態的唯讀欄位的型別`BindableProperty`名為[ `AbsoluteLayout.LayoutBoundsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)。 靜態`AbsoluteLayout.SetLayoutBounds`方法藉由呼叫`SetValue`子項`AbsoluteLayout.LayoutBoundsProperty`。 子系包含儲存附加的可繫結屬性和其值的字典。 在配置期間`AbsoluteLayout`可以藉由呼叫取得該值[ `AbsoluteLayout.GetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject))，這透過實作`GetValue`呼叫。

## <a name="proportional-sizing-and-positioning"></a>依比例調整大小和位置

`AbsoluteLayout` 實作依比例調整大小及定位功能。 此類別會定義第二個的附加可繫結屬性， [ `LayoutFlagsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)，與相關的靜態方法[ `AbsoluteLayout.SetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags))並[ `AbsoluteLayout.GetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject))。

引數`AbsoluteLayout.SetLayoutFlags`和傳回值`AbsoluteLayout.GetLayoutFlags`是類型的值[ `AbsoluteLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayoutFlags)，列舉型別具有下列成員：

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) （等於 0）
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

您可以結合這些項目與C#位元 OR 運算子。

這些旗標設定後，特定屬性的`Rectangle`會按比例解譯用來定位和調整大小的子系的版面配置範圍結構。

當`WidthProportional`旗標設定時，`Width`值的子系是相同的寬度為 1 表示`AbsoluteLayout`。 類似的方法用於高度。

調和間距位置納入考量的大小。 當`XProportional`旗標設定時，`X`屬性`Rectangle`配置界限成正比。 值為 0，表示子系的左邊緣位於左邊緣`AbsoluteLayout`，但為 1，表示子系的右邊緣位於右邊緣的位置`AbsoluteLayout`，不會超出的右邊緣`AbsoluteLayout`就像 expect。 `X` 0.5 屬性中心的水平方式中的子系`AbsoluteLayout`。

[ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional)範例示範如何使用依比例調整大小和位置。

## <a name="working-with-proportional-coordinates"></a>使用依比例的座標

有時候，很容易將按比例定位與實作的方式不同`AbsoluteLayout`。 您可能想要使用依比例的座標位置`X`屬性為 1 會將子系的左邊的緣 （而不是右邊緣） 置於針對右緣`AbsoluteLayout`。

此替代的位置配置可以呼叫"小數的子系 coordinates"。 您可以將轉換來自小數的子系座標所需的版面配置邊界`AbsoluteLayout`使用以下公式：

layoutBounds.X = (fractionalChildCoordinate.X / (1-layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1-layoutBounds.Height))

[ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc)為範例。

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout 和 XAML

您可以使用`AbsoluteLayout`XAML 中並設定連接的可繫結屬性的子系`AbsoluteLayout`使用的屬性值`AbsoluteLayout.LayoutBounds`和`AbsoluteLayout.LayoutFlags`。 這示範於[ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo)並[ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml)範例。 後者的程式包含 32`BoxView`項目，但使用隱含`Style`包含`AbsoluteLayout.LayoutFlags`保留，最小的標記屬性。

包含類別名稱、 一個點和屬性名稱的 XAML 中的屬性是*一律*附加可繫結屬性。

## <a name="overlays"></a>覆疊

您可以使用`AbsoluteLayout`來建構*重疊*，其中涵蓋了頁面與其他控制項，或許是為了保護使用者與頁面上的一般控制項互動。

[ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay)範例示範這項技巧，同時也示範[ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar)，其中顯示的程式已完成的範圍工作。

## <a name="some-fun"></a>一些有趣

[ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock)範例會顯示目前的時間以模擬的 5 x 7 點矩陣顯示。 每個點`BoxView`（有 228 個） 的大小和位置上`AbsoluteLayout`。

[![點陣式時鐘的三個螢幕擷取畫面](images/ch14fg08-small.png "點陣式時鐘")](images/ch14fg08-large.png#lightbox "點陣式時鐘")

[ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText)計劃以動畫顯示兩個`Label`彈水平和垂直螢幕上的物件。



## <a name="related-links"></a>相關連結

- [第 14 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [第 14 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)

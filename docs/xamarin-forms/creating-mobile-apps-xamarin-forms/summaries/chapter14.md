---
title: "第 14 的摘要。 絕對版面配置"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: ac4c41ebd70b58e95a3fa4fa7a391a473361b1db
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-14-absolute-layout"></a>第 14 的摘要。 絕對版面配置

像`StackLayout`， [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)衍生自`Layout<View>`和繼承`Children`屬性。 `AbsoluteLayout` 實作的版面配置系統需要程式設計人員指定的位置及其子系以及 （選擇性） 其大小。 指定相對於左上角的子系的左上角位置`AbsoluteLayout`以與裝置無關的單位。 `AbsoluteLayout` 也會實作比例定位和調整大小功能。

`AbsoluteLayout` 應該被視為一個特殊用途的版面配置系統，以程式設計人員可以強制大小的子系時，才使用 (例如，`BoxView`項目) 或當元素的大小並不會影響其他子系的位置。 `HorizontalOptions`和`VerticalOptions`屬性有不會影響的子系`AbsoluteLayout`。

本章節也導入了重要的功能*附加可繫結屬性*，讓某一個類別中定義的屬性 (在此情況下`AbsoluteLayout`) 附加至另一個類別 (子系`AbsoluteLayout`)。

## <a name="absolutelayout-in-code"></a>在程式碼中的 AbsoluteLayout

您可以新增的子系`Children`集合`AbsoluteLayout`使用標準[ `Add` ](https://developer.xamarin.com/api/member/System.Collections.Generic.ICollection%3CT%3E.Add/p/T/)方法，但`AbsoluteLayout`也會提供擴充[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Rectangle/Xamarin.Forms.AbsoluteLayoutFlags/)可讓您指定的方法[ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/)。 另一個[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Point/)方法只需要[ `Point` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Point/)，在此情況下不受限制子系，並調整大小。

您可以建立`Rectangle`值與[建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/System.Double/System.Double/System.Double/System.Double/)需要四個值 & #x 2014; 指出相對於其父代，子系的左上角的位置與前兩個和第二個兩個指出子系的大小。 您可以使用或[建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/Xamarin.Forms.Point/Xamarin.Forms.Size/)需要`Point`和[ `Size` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/)值。

這些`Add`中示範的方法[ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo)，哪些位置`BoxView`項目使用`Rectangle`值，和`Label`使用剛才項目`Point`值。

[ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed)範例會使用 32`BoxView`建立 chessboard 模式的項目。 程式可讓`BoxView`35 單位正方形的硬式編碼的項目大小。 `AbsoluteLayout`具有其`HorizontalOptions`和`VerticalOptions`設`LayoutOptions.Center`，這會導致`AbsoluteLayout`有 280 單位正方形的總大小。

## <a name="attached-bindable-properties"></a>附加的繫結屬性

您也可設定的位置和 （選擇性） 的子系的大小`AbsoluteLayout`加入至之後`Children`使用靜態方法的集合[ `AbsoluteLayout.SetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutBounds/p/Xamarin.Forms.BindableObject/Xamarin.Forms.Rectangle/)。 第一個引數是子系。第二個是`Rectangle`物件。 您可以指定的子系調整大小水平及/或垂直寬度和高度值設定為[ `AbsoluteLayout.AutoSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.AbsoluteLayout.AutoSize/)常數。

[ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic)範例置於`AbsoluteLayout`中`ContentView`與`SizeChanged`處理常式呼叫`AbsoluteLayout.SetLayoutBounds`，使其變成最大的所有子系。  

附加的繫結屬性的`AbsoluteLayout`定義為靜態唯讀欄位的型別`BindableProperty`名為[ `AbsoluteLayout.LayoutBoundsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/)。 靜態`AbsoluteLayout.SetLayoutBounds`方法藉由呼叫`SetValue`子系上`AbsoluteLayout.LayoutBoundsProperty`。 子系包含儲存附加的繫結屬性和其值的字典。 在配置期間`AbsoluteLayout`可以藉由呼叫取得該值[ `AbsoluteLayout.GetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutBounds/p/Xamarin.Forms.BindableObject/)，與實作`GetValue`呼叫。

## <a name="proportional-sizing-and-positioning"></a>調和間距大小及位置

`AbsoluteLayout` 實作比例調整大小和位置的功能。 類別會定義第二個連結可繫結屬性[ `LayoutFlagsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/)，與相關的靜態方法[ `AbsoluteLayout.SetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutFlags/p/Xamarin.Forms.BindableObject/Xamarin.Forms.AbsoluteLayoutFlags/)和[ `AbsoluteLayout.GetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutFlags/p/Xamarin.Forms.BindableObject/)。

引數`AbsoluteLayout.SetLayoutFlags`和傳回值`AbsoluteLayout.GetLayoutFlags`是型別值[ `AbsoluteLayoutFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/)，具有下列成員的列舉：

- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.None/) （等於 0）
- [`XProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.XProportional/) (1)
- [`YProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.YProportional/) (2)
- [`PositionProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional/) (3)
- [`WidthProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional/) (4)
- [`HeightProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional/) (8)
- [`SizeProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional/) (12)
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.All/) (\xFFFFFFFF)

您可以結合這些使用 C# 位元 OR 運算子。

這些旗標設定後，特定屬性的`Rectangle`會按比例解譯用來定位和調整大小的子配置範圍結構。

當`WidthProportional`設定旗標，`Width`值為 1 表示子系是寬度相同`AbsoluteLayout`。 用於高度類似的方法。

調和間距定位列入考量的大小。 當`XProportional`設定旗標，`X`屬性`Rectangle`配置界限是成比例。 值為 0，表示子系的左邊緣會定位於左邊緣`AbsoluteLayout`，但是 1 表示子系的右邊緣位於右邊緣的位置`AbsoluteLayout`，不會超出的右邊緣`AbsoluteLayout`如同您 expect。 `X` 0.5 屬性中心水平的子系`AbsoluteLayout`。

[ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional)範例將示範如何使用比例調整大小和位置。

## <a name="working-with-proportional-coordinates"></a>使用比例座標

有時候，很容易想到比例定位方式不同於實作的方式`AbsoluteLayout`。 您可能會偏好使用成比例的座標位置`X`1 屬性放置子項目的左邊的緣 （而不是右邊緣） 對的右邊緣`AbsoluteLayout`。

這個替代的位置配置可以呼叫"小數子 coordinates"。 您可以將轉換從小數子座標所需的配置界限`AbsoluteLayout`使用下列公式：

layoutBounds.X = (fractionalChildCoordinate.X / (1-layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1-layoutBounds.Height))

[ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc)範例為其示範。

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout 和 XAML

您可以使用`AbsoluteLayout`在 XAML 中的子系上設定附加的繫結屬性和`AbsoluteLayout`上使用的屬性值`AbsoluteLayout.LayoutBounds`和`AbsoluteLayout.LayoutFlags`。 這示範於[ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo)和[ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml)範例。 第二個程式包含 32`BoxView`項目但使用隱含`Style`包含`AbsoluteLayout.LayoutFlags`保留，最小的標記屬性。

包含類別名稱、 一個點和屬性名稱的 XAML 中的屬性是*一律*附加的繫結屬性。

## <a name="overlays"></a>覆疊

您可以使用`AbsoluteLayout`建構*重疊*，其中涵蓋了網頁與其他控制項，可能是以避免使用者互動的頁面上的標準控制項。 

[ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay)範例示範這項技巧，同時也會示範[ `ProgressBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/)，其中顯示程式已完成的範圍工作。

## <a name="some-fun"></a>有趣

[ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock)範例會顯示目前的時間以模擬的 5 x 7 點矩陣顯示。 每個點`BoxView`（有 228 個） 的大小和位置上`AbsoluteLayout`。

[![點陣式時鐘的三個螢幕擷取畫面](images/ch14fg08-small.png "點陣式時鐘")](images/ch14fg08-large.png "點陣式時鐘")

[ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText)程式繪製兩個`Label`可以水平及垂直在畫面上的物件。



## <a name="related-links"></a>相關連結

- [第 14 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [第 14 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)

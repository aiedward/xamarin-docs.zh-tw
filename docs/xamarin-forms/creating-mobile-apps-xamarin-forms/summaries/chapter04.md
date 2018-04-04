---
title: 第 4 章的摘要。 捲動堆疊
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 7A39FD4F-15AD-4F94-960E-9FEEB63FFD44
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 09a9d21b7979e0eb3f12d3b1207f2185e059f65c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-4-scrolling-the-stack"></a>第 4 章的摘要。 捲動堆疊

本章主要專用於介紹的概念*配置*，這是類別和技術 Xamarin.Forms 用來組織頁面上的多個檢視的視覺顯示整體的詞彙。

版面配置包含數個類別衍生自[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)和[ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)。 本章著重[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。

在本章節也導入了是[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)， [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)，和[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)類別。

## <a name="stacks-of-views"></a>檢視的堆疊

[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 衍生自`Layout<View>`和繼承[ `Children` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)型別的屬性`IList<View>`。 您將多個檢視項目加入至這個集合，以及`StackLayout`它們顯示在水平或垂直堆疊。

設定[ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/)屬性`StackLayout`成員[ `StackOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackOrientation/)列舉型別，其中一個[ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Vertical/)或[`Horizontal`](https://developer.xamarin.com/api/field/Xamarin.Forms.StackOrientation.Horizontal/). 預設值為 `Vertical`。

設定[ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/)屬性`StackLayout`至`double`指定子系之間的間距的值。 預設值為 6。

程式碼中，您可以將項目加入`Children`集合`StackLayout`中`for`或`foreach`迴圈中所示[ **ColorLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorLoop)範例中，或者您可以初始化`Children`做為示範在個別的檢視清單集合[ **ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorList)。 子系必須衍生自`View`但可以包含其他`StackLayout`物件。

## <a name="scrolling-content"></a>捲動內容

如果`StackLayout`包含太多的子系，以顯示在頁面上，您可以放入`StackLayout`中[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)允許捲動。

設定[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/)屬性`ScrollView`至您想要捲動的檢視。 這通常是`StackLayout`，但它可以是任何檢視。

設定[ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/)屬性`ScrollView`成員[ `ScrollOrientation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollOrientation/)屬性， [ `Vertical` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Vertical/)， [ `Horizontal` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Horizontal/)，或[ `Both` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ScrollOrientation.Both/)。 預設值為 `Vertical`。 如果內容`ScrollView`是`StackLayout`，兩個方向應該一致。

[ **ReflectedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ReflectedColors)範例將示範如何使用`ScrollView`和`StackLayout`顯示可用的色彩。 此範例也示範如何使用.NET 反映來取得所有的公用靜態屬性和欄位的`Color`結構，而不需要明確列出它們。

## <a name="the-expands-option"></a>展開選項

當`StackLayout`堆疊及其子系，每個子系佔據的總高度內特定位置`StackLayout`相依於子系的大小，以及設定其`HorizontalOptions`和`VerticalOptions`屬性。 類型的值指派給這些屬性[ `LayoutOptions` ](http://developer.xamstage.com/api/type/Xamarin.Forms.LayoutOptions/)。

`LayoutOptions`結構會定義兩個屬性：

- [`Alignment`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/) 列舉型別的[ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/)與四個成員， [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/)， [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/)， [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/)，和 [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/)
- [`Expands`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) 型別 `bool`

為了方便起見，`LayoutOptions`結構也會定義八個靜態唯讀欄位的型別`LayoutOptions`，包含所有組合的兩個執行個體屬性：

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

下列討論牽涉到`StackLayout`與預設的垂直方向。 水平`StackLayout`類似。

針對垂直`StackLayout`、`HorizontalOptions`設定會決定子系如何水平定位的寬度`StackLayout`。 `Alignment`設定`Start`， `Center`，或`End`會導致要水平受條件約束的子系。 子判斷它自己的寬度，並位於 left、 center 或右邊的`StackLayout`。 `Fill`選項會導致要水平受到條件約束的子系，並填滿的寬度`StackLayout`。

針對垂直`StackLayout`每個子系、 垂直受條件約束，以及取得垂直位置根據子系的高度，在此情況下`VerticalOptions`設定無關。

如果垂直`StackLayout`本身不受限制&mdash;也就是如果其`VerticalOptions`設定`Start`， `Center`，或`End`的高度`StackLayout`是總其子系的高度。

不過，如果垂直`StackLayout`垂直限制&mdash;如果其`VerticalOptions`設定`Fill`&mdash;的高度`StackLayout`將為其容器，可能會大於總高度及其子系的高度。 如果這種情況下，而且，如果至少一個子系`VerticalOptions`設定`Expands`旗標`true`，再額外的空間`StackLayout`那些與所有子系之間平均配置`Expands`旗標`true`。 子系的總高度會接著等於的高度`StackLayout`，而`Alignment`屬於`VerticalOptions`設定會決定如何在子系垂直放置在其位置。

這示範於[ **VerticalOptionsDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/VerticalOptionsDemo)範例。

## <a name="frame-and-boxview"></a>框架和 BoxView

這兩個矩形檢視通常會用於顯示用途。

[ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/)檢視會顯示另一個檢視，例如可以是版面配置周圍矩形框架`StackLayout`。 `Frame` 繼承[ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/)屬性從[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) ，來檢視中顯示`Frame`。 `Frame`預設是透明的。 設定下列三個屬性，並自訂畫面格的外觀：

- [ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/)要顯示的屬性。 通常會設定`OutlineColor`至`Color.Accent`時您不知道基礎的色彩配置。
- [ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/)屬性可以設定為`true`黑色陰影顯示 iOS 裝置上。
- 設定[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)屬性`Thickness`框架和框架之間留下一個空格的值的內容。 預設值是 20 所有側邊的單位。

`Frame`具有預設`HorizontalOptions`和`VerticalOptions`值`LayoutOptions.Fill`，這表示`Frame`會填滿其容器。 搭配其他設定，大小`Frame`根據其內容的大小。

`Frame`示範[ **FramedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FramedText)範例。

[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)色彩所指定的一個矩形區域會顯示其[ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/)屬性。

如果`BoxView`受到 (其`HorizontalOptions`和`VerticalOptions`屬性有其預設設定`LayoutOptions.Fill`)、`BoxView`填滿可用空間。 如果`BoxView`不受限制 (與`HorizontalOptions`和`LayoutOptions`設定`Start`， `Center`，或`End`)，它有預設的維度 40 單位正方形。 A`BoxView`可以限制一個維度中，並在其他未受限。

通常，您將設定[ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)和[ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/)屬性`BoxView`，給予該特定的大小。 說明此用法[ **SizedBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/SizedBoxView)範例。

您可以使用數個執行個體`StackLayout`結合`BoxView`和數個`Label`中執行個體`Frame`來顯示特定的色彩，，然後放置每個檢視中`StackLayout`中`ScrollView`建立具吸引力清單中顯示的色彩[ **ColorBlocks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/ColorBlocks)範例：

[![三個螢幕擷取畫面的色彩區塊](images/ch04fg11-small.png "的色彩清單")](images/ch04fg11-large.png#lightbox "色彩的清單")

## <a name="a-scrollview-in-a-stacklayout"></a>在 StackLayout ScrollView 嗎？

放置`StackLayout`中`ScrollView`不常見，但將`ScrollView`中`StackLayout`有時候很方便。 不應該在理論上，這是可能因為垂直的子系`StackLayout`會以垂直方式不受限制。 但`ScrollView`必須以垂直方式限制。 必須為它提供特定的高度，以便它然後捲動決定它的子系的大小。

訣竅是要讓`ScrollView`的子系`StackLayout``VerticalOptions`設定`FillAndExpand`。 這示範於[ **BlackCat** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCat)範例。

**BlackCat**範例還示範如何定義及存取程式的資源內嵌在可攜式類別程式庫 (PCL)。 這可以達成共用資產專案 (Sap) 但有一點難度，做為處理程序[ **BlackCatSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/BlackCatSap)範例將示範如何。



## <a name="related-links"></a>相關連結

- [第 4 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch04-Apr2016.pdf)
- [第 4 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04)
- [第 4 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter04/FS)
- [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)
- [ScrollView](~/xamarin-forms/user-interface/layouts/scroll-view.md)

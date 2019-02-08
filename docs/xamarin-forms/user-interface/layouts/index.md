---
title: 在 Xamarin.Forms 中的版面配置
description: Xamarin.Forms 數個版面配置和功能來組織內容，在畫面上，而且這篇文章說明它們。
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 5bd232293c979566faed2856de7287903da94054
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831764"
---
# <a name="layouts-in-xamarinforms"></a>在 Xamarin.Forms 中的版面配置

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)

Xamarin.Forms 有數個版面配置和功能的組織在畫面上的內容。

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Xamarin.Forms 版面配置、 藉由[Xamarin University](https://university.xamarin.com/)**

每個版面配置控制項是如下所述，以及如何處理螢幕方向的變更的詳細資料：

* **[StackLayout](stack-layout.md)**  ： 用來排列檢視，以線性方式，水平或垂直。 中心，左邊或右邊的版面配置可對齊 StackLayout 中的檢視。
* **[AbsoluteLayout](absolute-layout.md)**  ： 用來排列檢視，藉由設定座標 & 絕對值或比例調整大小。 AbsoluteLayout 可以用來層檢視，以及它們的錨點向左、 右或置中。
* **[RelativeLayout](relative-layout.md)**  ： 用來排列檢視，藉由設定相對於其父代的維度及位置條件約束。
* **[方格](grid.md)** ： 用來排列方格中的檢視。 資料列和資料行，都可以指定絕對值或比例。
* **[FlexLayout](flex-layout.md)**  ： 用來水平或垂直排列檢視進行換行。
* **[ScrollView](scroll-view.md)**  ： 用來提供捲動時檢視完全無法符合螢幕的界限。
* **[LayoutOptions](layout-options.md)**  – 定義對齊和展開的檢視，相對於其父代。
* **[輸入透明度](#input_transparency)** – 指定項目是否收到輸入。
* **[邊界和邊框距離](margin-and-padding.md)** -示範如何在使用者介面中呈現的項目時，控制版面配置行為。
* **[裝置方向](device-orientation.md)** – 說明如何處理裝置方向變更。
* **[在平板電腦及桌面裝置上的版面配置](tablet.md)** -示範如何針對每個平台的較大螢幕最佳化。
* **[可繫結的版面配置](bindable-layouts.md)** – 啟用版面配置的類別，以產生其內容繫結至項目的集合。
* **[建立自訂的版面配置](custom.md)** – 說明如何建立自訂的版面配置的類別。
* **[版面配置壓縮](layout-compression.md)** – 移除指定從視覺化樹狀結構的版面配置的試圖改善頁面轉譯效能。

平台控制項也可用在以 Xamarin.Forms 版面配置中直接[**原生內嵌**](~/xamarin-forms/platform/native-views/index.md) （新的 Xamarin.Forms 2.2 中），而且可以[**建立自訂配置**](custom.md)以符合特定需求。

下圖會視覺化版面配置控制項：

[![](images/layouts-sml.png "Xamarin.Forms 版面配置")](images/layouts.png#lightbox "Xamarin.Forms 版面配置")

## <a name="choosing-the-right-layout"></a>選擇正確的版面配置

您選擇在您的應用程式中的配置可以幫助，或當您建立吸引人且可使用 Xamarin.Forms 應用程式會降低。 花費一些時間考慮每個配置的運作方式如何協助您撰寫更簡潔且更有彈性的 UI 程式碼。 畫面上可以有不同的版面配置，以達到特定的設計的組合。

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

`StackLayout`用於顯示檢視表，沿著水平或垂直線條。 位置和配置內的大小取決於檢視`HeightRequest`， `WidthRequest`，`HorizontalOptions`和`VerticalOptions`。 `StackLayout` 通常當作基底的版面配置，排列在畫面上的其他配置使用。

如需範例，當`StackLayout`會是不錯的選擇，請考慮需要顯示按鈕和標籤，標籤為靠左對齊] 和 [靠右對齊按鈕的應用程式。

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="flexlayoutflex-layoutmd"></a>[FlexLayout](flex-layout.md)

`FlexLayout`類似於`StackLayout`，因為它會顯示子檢視，以水平或垂直方式：

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">

    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

不過，如果有太多的子系，無法納入單一資料列或資料行，`FlexLayout`能夠包裝這些檢視表。 `FlexLayout` 會根據 CSS 彈性方塊版面配置模組，並有許多相同的內建選項，用來定位和調整及其子系。

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

`AbsoluteLayout`用於顯示檢視，以做為明確的值或值相對於配置大小的大小和所指定的位置。 不同於`StackLayout`並`Grid`，`AbsoluteLayout`允許子重疊的檢視。 不同於`RelativeLayout`，`AbsoluteLayout`不會讓您放入螢幕以外的元素。

如需範例，當`AbsoluteLayout`會是不錯的選擇，請考慮應用程式必須顯示為堆疊物件的集合。 這通常會看到呈現的相片或歌曲的專輯時。 下列程式碼會看起來像是一疊中，使用旋轉至暗示有一堆內容的項目：

在 XAML 中：

```xaml
<AbsoluteLayout Padding="15">
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="30"
    Source="bottom.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="60"
    Source="middle.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
    Source="cover.png" />
</AbsoluteLayout>
```

請注意上述程式碼中的下列層面：

- 每個`Image`會顯示在相同的位置 （中間的水平空間）
- `Padding`視為`AbsoluteLayout`不同的是`RelativeLayout`，會忽略它。
- `AbsoluteLayout.LayoutFlags` 指定的版面配置邊界解譯的方式。 在此情況下`PositionProportional`，表示座標將會配置大小的比率，雖然大小會被解譯為明確的大小。
- `AbsoluteLayout.Layoutbounds` 依此順序中指定的水平位置、 垂直位置、 寬度和高度。

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

`RelativeLayout`用於顯示檢視中，使用大小和位置指定為相對於版面配置或另一個檢視的值的值。 相對的值不需要符合他對應相關的檢視上的值。 例如，就可以設定的檢視`Width`屬性到另一個檢視依比例`X`屬性。

RelativeLayout 可用來建立跨裝置的大小，按比例的 Ui。 下列 XAML 會實作在最上層的角落的方塊是設計，含有 flagpole 中心的旗標：

```xaml
<RelativeLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
  <BoxView Color="Blue" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = 0}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Red" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .9}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Gray" WidthRequest="15" x:Name="pole"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .45}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = .25}" />
  <BoxView Color="Green"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
    RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.2, Constant=20}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

請注意上述程式碼中的下列層面：

- 位置和大小指定為條件約束。
- 名為 flagpole 以便旗標的 （綠色方塊的） 可以相對於 flagpole 組位置。
- 條件約束運算式有`Factor`和`Constant`屬性，可用來定義位置和大小，為多個 （或片段） 之屬性的其他物件，再加上一個常數。 常數可以是負數。

### <a name="gridgridmd"></a>[格線](grid.md)

`Grid`用於顯示在 資料列和資料行中的項目。 請注意，此方格沒有資料表，因此它並沒有儲存格、 頁首和頁尾資料列或資料列和資料行之間的框線的概念。 一般情況下，不適合顯示表格式資料方格。 該用途，請考慮[ListView](~/xamarin-forms/user-interface/listview/index.md)或是[TableView](~/xamarin-forms/user-interface/tableview.md)。

如需範例，當`Grid`是正確的版面配置使用，請輸入數字時應考量的計算機。 計算機的數字輸入可能會包含四個資料列和三個資料行，各有一個按鈕。 下列程式碼會實作這項設計：

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>

  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Button Text="1" Grid.Row="0" Grid.Column="0" />
  <Button Text="2" Grid.Row="0" Grid.Column="1" />
  <Button Text="3" Grid.Row="0" Grid.Column="2" />
  <Button Text="4" Grid.Row="1" Grid.Column="0" />
  <Button Text="5" Grid.Row="1" Grid.Column="1" />
  <Button Text="6" Grid.Row="1" Grid.Column="2" />
  <Button Text="7" Grid.Row="2" Grid.Column="0" />
  <Button Text="8" Grid.Row="2" Grid.Column="1" />
  <Button Text="9" Grid.Row="2" Grid.Column="2" />
  <Button Text="0" Grid.Row="3" Grid.Column="1" />
  <Button Text="&lt;-" Grid.Row="3" Grid.Column="2" />
</Grid>
```

請注意上述程式碼中的下列層面：

- 方格和資料行明確指定，不會推斷出內容。
- `Height` 和`Width`可以將值設為星號，表示格線會設定這些值，以填滿可用空間。
- 每個按鈕的位置由指定`Grid.Row`  &  `Grid.Column`屬性。

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)結構可以用來定義對齊和展開的檢視，相對於其父代。

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[邊界和邊框距離](margin-and-padding.md)

[ `Margin` ](xref:Xamarin.Forms.View.Margin)並[ `Padding` ](xref:Xamarin.Forms.Layout.Padding)屬性控制版面配置時的行為項目會呈現使用者介面中。

<a name="input_transparency" />

### <a name="input-transparency"></a>輸入的透明度

每個元素都[ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent)用來定義項目是否收到輸入的屬性。 其預設值是`false`，確保項目收到輸入。

當設定這個屬性上的容器類別，例如版面配置類別至子元素，其值傳輸。 因此，設定[ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent)屬性設`true`版面配置，類別會導致無法接收輸入的版面配置中的項目。

### <a name="device-orientationdevice-orientationmd"></a>[裝置方向](device-orientation.md)

Xamarin.Forms 和內建的版面配置是能夠處理變更裝置方向。 您的應用程式將支援，以及如何為您要進行的方向，請考慮使用橫向和直向模式中提供的空間。

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[平板電腦和傳統型應用程式的版面配置](tablet.md)

iOS、 Android 和通用 Windows 平台所有支援較大的螢幕大小上 tablet 裝置 （以及膝上型電腦和 Windows 的桌上型電腦）。 Xamarin.Forms 可讓您藉由偵測裝置類型，以及調整頁面配置，或完全使用完全不同的頁面，針對較大螢幕最佳化您的應用程式，針對較大的螢幕。

### <a name="bindable-layoutsbindable-layoutsmd"></a>[可繫結的版面配置](bindable-layouts.md)

`BindableLayout`類別可讓任何配置類別衍生自[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)類別來產生其內容繫結至項目的集合，可設定的每個項目外觀的選項來[ `DataTemplate`](xref:Xamarin.Forms.DataTemplate).

### <a name="creating-a-custom-layoutcustommd"></a>[建立自訂版面配置](custom.md)

Xamarin.Forms 可定義四種版面配置類別- [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)， [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout)， [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)，以及[ `Grid` ](xref:Xamarin.Forms.Grid)，和每個以不同方式來排列其子系。 不過，有時候其組織未使用的版面配置的頁面內容的必要提供 Xamarin.Forms。 這篇文章說明如何撰寫自訂的版面配置的類別，並示範方向區分`WrapLayout`類別在頁面上，水平排列其子系，然後將包裝的後續的子系，額外的資料列顯示。

### <a name="layout-compressionlayout-compressionmd"></a>[版面配置壓縮](layout-compression.md)

版面配置壓縮從視覺化樹狀結構移除指定的版面配置，以試圖改善頁面轉譯效能。 這所提供的效能優勢，會根據頁面的複雜性、所使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。

## <a name="making-your-choice"></a>您所選擇

請注意在大部分情況下，一個以上的版面配置選項可以用來實作您所需的設計。 當有多個有效的選項時，請考慮哪種方法會是最簡單的方法為您的情況。
大部分的設計無法實現具有只在一個配置，以便建立更複雜的設計所需的做為巢狀版面配置。

## <a name="related-links"></a>相關連結

- [Apple 人性化介面指導方針](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Android 設計網站](https://developer.android.com/design/index.html)
- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)

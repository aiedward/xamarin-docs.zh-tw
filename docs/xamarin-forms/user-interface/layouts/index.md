---
title: 版面配置
description: 在畫面上檢視版面配置設定。
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 2f57ac5b5b54b2606618c5e59fb544cae7c77e88
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="layouts"></a>版面配置

Xamarin.Forms 會有數個配置和功能來組織螢幕上的內容。 

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Xamarin.Forms 版面配置、 藉由[Xamarin 大學](https://university.xamarin.com/)**

如下所述的每個版面配置控制項，以及如何處理螢幕方向變更的詳細資訊。

* **[StackLayout](stack-layout.md)**  &ndash;用來排列檢視，以線性方式，水平或垂直。 StackLayout 中的檢視可以對齊中央左或向右的版面配置。
* **[AbsoluteLayout](absolute-layout.md)**  &ndash;用來排列檢視，藉由設定座標 （& s） 方面絕對值或比例調整大小。 AbsoluteLayout 可以用於圖層的檢視，以及錨定在其左、 右邊或中間。
* **[RelativeLayout](relative-layout.md)**  &ndash;用來排列檢視，藉由設定相對於其父代的維度及位置條件約束。
* **[方格](grid.md)** &ndash;用來排列在方格中的檢視。 資料列和資料行可以指定絕對值或比例。
* **[FlexLayout](flex-layout.md)**  &ndash;用來水平或垂直排列檢視進行換行。
* **[ScrollView](scroll-view.md)**  &ndash;用來提供捲動時檢視完全無法容納螢幕的界限。
* **[LayoutOptions](layout-options.md)**  &ndash;對齊和擴充的檢視，相對於其父定義。
* **[輸入透明度](#input_transparency)** &ndash;指定項目是否會收到輸入。
* **[邊界和邊框距離](margin-and-padding.md)** &ndash;示範如何在使用者介面中呈現的項目時，控制配置行為。
* **[裝置方向](device-orientation.md)** &ndash;說明如何處理裝置方向變更。
* **[平板電腦和桌上型電腦裝置上的配置](tablet.md)** &ndash;示範如何針對每個平台上較大螢幕最佳化。
* **[建立自訂的版面配置](custom.md)** &ndash;說明如何建立自訂版面配置的類別。
* **[版面配置壓縮](layout-compression.md)** &ndash;移除指定的從視覺化樹狀結構的配置以改善網頁呈現效能。

也可以使用的 Xamarin.Forms 版面配置中直接使用平台控制項[**原生內嵌**](~/xamarin-forms/platform/native-views/index.md) （Xamarin.Forms 2.2 中新增），您可以[**建立自訂的版面配置**](custom.md)以符合特定需求。

下圖中視覺化版面配置控制項：

[![](images/layouts-sml.png "Xamarin.Forms 配置")](images/layouts.png#lightbox "Xamarin.Forms 版面配置")

## <a name="choosing-the-right-layout"></a>選擇右邊的版面配置

您選擇在您的應用程式中的配置可以協助，或為您要建立吸引人也可以使用 Xamarin.Forms 應用程式會降低。 花一些時間來考量每個配置運作可以幫助您撰寫較為簡潔且更具擴充性的 UI 程式碼。 畫面上可以有不同的版面配置，以達到特定的設計的組合。

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

`StackLayout`用於顯示檢視會水平或垂直線條。 位置和配置內的大小取決於檢視的`HeightRequest`， `WidthRequest`，`HorizontalOptions`和`VerticalOptions`。 `StackLayout` 通常當作基底的版面配置，排列在螢幕上的其他配置使用。

如需何時`StackLayout`會是很不錯的選擇，請考慮需要顯示按鈕和標籤，是靠左對齊的標籤和靠右對齊按鈕的應用程式。

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="flexlayoutflex-layoutmd"></a>[FlexLayout](flex-layout.md)

`FlexLayout`類似於`StackLayout`均以水平或垂直方式顯示子檢視：

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">
        
    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

不過，如果有太多的子系，以配合單一資料列或資料，`FlexLayout`能夠包裝這些檢視表。 `FlexLayout` 會根據 CSS 彈性方塊配置模組，並有許多的內建的相同選項，用來定位和對齊其子系。

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

`AbsoluteLayout`用於顯示檢視中，使用大小和位置可能是被指定做為明確的值或值相對於版面配置的大小。 不同於`StackLayout`和`Grid`，`AbsoluteLayout`允許子重疊的檢視。 不同於`RelativeLayout`，`AbsoluteLayout`不會讓您放置螢幕以外的項目。

如需何時`AbsoluteLayout`會是很不錯的選擇，假設應用程式必須出示堆疊為物件的集合。 這通常會看到呈現相簿的相片或歌曲時。 下列程式碼提供一疊，的外觀與旋轉至在一堆內容的項目：

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

請注意上述程式碼的下列層面：

- 每個`Image`會顯示在相同的位置 （中間的水平空間）
- `Padding`視為`AbsoluteLayout`不同的是`RelativeLayout`，這會予以忽略。
- `AbsoluteLayout.LayoutFlags` 指定配置界限解譯的方式。 在此情況下`PositionProportional`，表示座標將會配置大小的比例，而大小會解譯為明確的大小。
- `AbsoluteLayout.Layoutbounds` 依該順序指定水平位置、 垂直位置、 寬度和高度。

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

`RelativeLayout`用於顯示檢視中，使用大小和位置指定為值相對於版面配置或另一個檢視的值。 相對的值不需要符合他對應相關的檢視上的值。 例如，便可設定的檢視`Width`屬性到另一個檢視依比例`X`屬性。

RelativeLayout 可以用來建立跨裝置大小按比例調整的 Ui。 下列 XAML 會實作最上方角落的方塊是設計，含有 flagpole 中央的旗標：

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

請注意上述程式碼的下列層面：

- 做為條件約束指定位置和大小。
- 名為 flagpole 以便的旗標 （綠色方塊的） 可以相對於 flagpole 組位置。
- 條件約束運算式有`Factor`和`Constant`屬性，可用來定義多個位置和大小 （或分數） 的其他物件，再加上常數的屬性。 常數可以是負數。

### <a name="gridgridmd"></a>[格線](grid.md)

`Grid`用於顯示在 資料列和資料行中的項目。 請注意，此方格沒有資料表，因此不需要儲存格、 頁首和頁尾資料列或資料列和資料行之間的概念。 一般情況下，不適合用來顯示表格式資料方格。 對於使用，請考慮[ListView](~/xamarin-forms/user-interface/listview/index.md)或[TableView](~/xamarin-forms/user-interface/tableview.md)。

如需何時`Grid`是正確使用，請考慮數字輸入 [小算盤] 的版面配置。 計算機的數字輸入可能會包含四個資料列和三個資料行，各有一個按鈕。 下列程式碼會實作此設計：

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

請注意上述程式碼的下列層面：

- 方格和資料行明確指定，不會推斷出的內容。
- `Height` 和`Width`可以將值設為星號，表示格線會設定這些值以填滿可用空間。
- 每個按鈕位置由指定`Grid.Row`  &  `Grid.Column`屬性。

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)結構可以用來定義對齊和擴充的檢視，相對於其父代。

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[邊界和邊框距離](margin-and-padding.md)

[ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)和[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)屬性控制版面配置時的行為項目會呈現使用者介面中。

<a name="input_transparency" />

### <a name="input-transparency"></a>輸入的透明度

每個項目具有[ `InputTransparent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.InputTransparent/)用來定義此項目是否會收到輸入的屬性。 預設值是`false`，確保此項目收到的輸入。

當容器類別，例如版面配置類別至子元素，其值傳輸上設定這個屬性。 因此，設定[ `InputTransparent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.InputTransparent/)屬性`true`版面配置類別將會導致不接收輸入配置中的項目。

### <a name="device-orientationdevice-orientationmd"></a>[裝置方向](device-orientation.md)

Xamarin.Forms 和內建的版面配置是能夠處理變更裝置方向。 您的應用程式將支援，以及如何為您要進行的方向，請考慮使用橫向和縱向模式中所提供的空間。

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[平板電腦和桌面應用程式的版面配置](tablet.md)

iOS、 Android 和通用 Windows 平台所有支援較大的螢幕大小上平板裝置 （以及膝上型電腦和 Windows 的桌上型電腦）。 Xamarin.Forms 可讓您藉由偵測裝置類型和調整頁面配置，或完全使用完全不同的頁面，針對較大的螢幕最佳化您的應用程式，針對較大的螢幕。

### <a name="creating-a-custom-layoutcustommd"></a>[建立自訂版面配置](custom.md)

Xamarin.Forms 會定義四個配置類別- [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)， [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)， [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)，和[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)，和每個不同的方式排列子系。 不過，有時候其組織未使用的版面配置的頁面內容的必要提供 Xamarin.Forms。 本文說明如何撰寫自訂版面配置的類別，並示範方向區分`WrapLayout`類別，在頁面上，水平排列子系，並繞著額外的資料列的後續子系的顯示。

### <a name="layout-compressionlayout-compressionmd"></a>[版面配置壓縮](layout-compression.md)

版面配置壓縮從視覺化樹狀結構移除指定的配置以改善網頁呈現效能。 這所提供的效能優勢，會根據頁面的複雜性、所使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。

## <a name="making-your-choice"></a>您所選擇

請注意在大部分情況下，多個配置選項可以用來實作您所需的設計。 當有多個有效的選擇時，請考慮哪種方法將會是最簡單的情況。
大部分的設計無法實現具有一個配置，以便建立更複雜的設計所需的做為巢狀版面配置。


## <a name="related-links"></a>相關連結

- [Apple 人性化介面指導方針](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Android 設計網站](https://developer.android.com/design/index.html)
- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)

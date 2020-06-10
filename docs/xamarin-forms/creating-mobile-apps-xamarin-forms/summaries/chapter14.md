---
title： "第14章的摘要。 絕對配置的「描述：」使用下列方式建立 Mobile Apps Xamarin.Forms ：第14章的摘要。 絕對版面配置 "ms. 生產： xamarin ms. 技術： xamarin-assetid： 88882A48-3226-42D1-96ED-241250B64A84 author： davidbritch ms-chap： dabritch ms. date： 07/19/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-14-absolute-layout"></a>第14章的摘要。 絕對版面配置

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Like `StackLayout` ， [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 衍生自 `Layout<View>` ，並繼承 `Children` 屬性。 `AbsoluteLayout`會執行配置系統，要求程式設計人員指定其子系的位置，並選擇性地指定其大小。 位置是由子系的左上角指定，相對於 `AbsoluteLayout` 裝置獨立單位中的左上角。 `AbsoluteLayout`也會執行比例的定位和調整大小功能。

`AbsoluteLayout`應該被視為特殊用途的版面配置系統，只有在程式設計人員可以在子系（例如專案）上施加大小， `BoxView` 或當元素的大小不影響其他子系的位置時，才會使用這個配置系統。 `HorizontalOptions`和 `VerticalOptions` 屬性不會影響的子系 `AbsoluteLayout` 。

本章也介紹*附加*可系結屬性的重要功能，讓一個類別中定義的屬性（在此案例中為） `AbsoluteLayout` 附加至另一個類別（的子系 `AbsoluteLayout` ）。

## <a name="absolutelayout-in-code"></a>程式碼中的 AbsoluteLayout

您可以使用標準方法將子系新增至的 `Children` 集合 `AbsoluteLayout` [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) ，但也會 `AbsoluteLayout` 提供可 [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) 讓您指定的擴充方法 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 。 另一個 [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) 方法只需要 [`Point`](xref:Xamarin.Forms.Point) ，在此情況下，子系是不受限制的，而且大小本身。

您可以 `Rectangle` 使用需要四個值的函式來建立值[，這](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double))兩個值 &mdash; 表示子系相對於其父代的左上角位置，而第二個則表示子系的大小。 或者，您可以使用 [構造函式] （x： Xamarin.Forms 。矩形。% 23ctor （ Xamarin.Forms 。Point、 Xamarin.Forms 。大小）），它需要 `Point` 和 [`Size`](xref:Xamarin.Forms.Size) 值。

這些 `Add` 方法會在[**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo)中示範，其會 `BoxView` 使用值來放置元素 `Rectangle` ，而 `Label` 元素只會使用 `Point` 值。

[**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed)範例會使用 32 `BoxView` 元素來建立 chessboard 模式。 程式會提供 `BoxView` 元素硬式編碼的大小為35單位的正方形。 `AbsoluteLayout`已將其 `HorizontalOptions` 和 `VerticalOptions` 設定為 `LayoutOptions.Center` ，這會導致的 `AbsoluteLayout` 總大小為280單位的平方。

## <a name="attached-bindable-properties"></a>附加的可系結屬性

您也可以 `AbsoluteLayout` `Children` 使用靜態方法 [ `AbsoluteLayout.SetLayoutBounds` ] （x：，設定位置，並選擇性地將的子系的大小加入至集合之後 Xamarin.Forms 。AbsoluteLayout. SetLayoutBounds （ Xamarin.Forms 。BindableObject， Xamarin.Forms 。矩形））。 第一個引數是子系;第二個是 `Rectangle` 物件。 您可以藉由將 width 和 height 值設定為常數，來指定子大小本身的水準和/或垂直 [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) 。

[**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic)範例會將放 `AbsoluteLayout` 在中， `ContentView` 並使用 `SizeChanged` 處理常式呼叫 `AbsoluteLayout.SetLayoutBounds` 所有子系，使其盡可能大。  

所定義的附加可系結屬性 `AbsoluteLayout` 是名為之類型的靜態唯讀欄位 `BindableProperty` [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 。 使用在 `AbsoluteLayout.SetLayoutBounds` 子系上呼叫，即可實作為靜態方法 `SetValue` `AbsoluteLayout.LayoutBoundsProperty` 。 子系包含字典，其中儲存了附加的可系結屬性和其值。 在版面配置期間， `AbsoluteLayout` 可以藉由呼叫 [ `AbsoluteLayout.GetLayoutBounds` ] （x：）來取得該值 Xamarin.Forms 。AbsoluteLayout. GetLayoutBounds （ Xamarin.Forms 。BindableObject）），它是透過呼叫來執行 `GetValue` 。

## <a name="proportional-sizing-and-positioning"></a>比例調整大小和位置

`AbsoluteLayout`實行比例調整大小和定位功能。 類別會 [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 使用相關的靜態方法 [ `AbsoluteLayout.SetLayoutFlags` ] （x：，來定義第二個附加的可系結屬性 Xamarin.Forms 。AbsoluteLayout. SetLayoutFlags （ Xamarin.Forms 。BindableObject， Xamarin.Forms 。AbsoluteLayoutFlags））和 [ `AbsoluteLayout.GetLayoutFlags` ] （x： Xamarin.Forms 。AbsoluteLayout. GetLayoutFlags （ Xamarin.Forms 。BindableObject））。

的引數與 `AbsoluteLayout.SetLayoutFlags` 的傳回值 `AbsoluteLayout.GetLayoutFlags` 是類型的值 [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) ，列舉型別具有下列成員：

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None)（等於0）
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional)sha-1
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional)2
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional)第
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional)4gb
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional)8
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional)12
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All)(\xFFFFFFFF)

您可以將這些與 c # 位 OR 運算子結合。

設定這些旗標 `Rectangle` 後，用來定位和調整子系之配置界限結構的某些屬性會按比例轉譯。

`WidthProportional`設定旗標時，值為 `Width` 1 表示子系與的寬度相同 `AbsoluteLayout` 。 高度會使用類似的方法。

比例定位會將大小列入考慮。 `XProportional`設定旗標時，配置 `X` 界限的屬性 `Rectangle` 會成比例。 值為0表示子系的左邊緣位於的左邊緣 `AbsoluteLayout` ，但位置為1表示子系的右邊緣位於的右邊緣 `AbsoluteLayout` ，而不是超出您預期的右邊緣 `AbsoluteLayout` 。 `X`0.5 的屬性會在中水準置中子系 `AbsoluteLayout` 。

[**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional)範例示範如何使用比例調整大小和位置。

## <a name="working-with-proportional-coordinates"></a>使用比例座標

有時候，比較容易將比例定位視為不同于在中的執行方式 `AbsoluteLayout` 。 您可能偏好使用比例座標，其中1的 `X` 屬性會將子系的左邊緣（而不是右邊緣）放置在的右邊緣 `AbsoluteLayout` 。

這個替代的定位配置可以稱為「分數子座標」。 您可以從小數子座標轉換成使用下列公式所需的版面配置界限 `AbsoluteLayout` ：

layoutBounds. X = （fractionalChildCoordinate. X/（1-layoutBounds. Width））

layoutBounds。 Y = （fractionalChildCoordinate. Y/（1-layoutBounds Height））

[**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc)範例會示範這種情況。

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout 和 XAML

您可以 `AbsoluteLayout` 在 XAML 中使用，並 `AbsoluteLayout` 使用和的屬性值，在的子系上設定附加的可系結屬性 `AbsoluteLayout.LayoutBounds` `AbsoluteLayout.LayoutFlags` 。 這會在[**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo)和[**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml)範例中示範。 後面的套裝程式含 32 `BoxView` 元素，但使用 `Style` 包含屬性的隱含，將 `AbsoluteLayout.LayoutFlags` 標記保持最小值。

XAML 中由類別名稱、點和屬性名稱所組成的屬性，*一律*是附加的可系結屬性。

## <a name="overlays"></a>重疊

您可以使用 `AbsoluteLayout` 來建立覆*overlay*迭，其中涵蓋含有其他控制項的頁面，可能會保護使用者與頁面上的一般控制項的互動。

[**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay)範例會示範這項技術，而且也會示範 [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) ，它會顯示程式已完成工作的範圍。

## <a name="some-fun"></a>有趣

[**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock)範例會顯示目前時間與模擬的5x7 點矩陣顯示。 每個點都是 `BoxView` （其中有228）大小，並置於上 `AbsoluteLayout` 。

[![點矩陣時鐘的三重螢幕擷取畫面](images/ch14fg08-small.png "點矩陣時鐘")](images/ch14fg08-large.png#lightbox "點矩陣時鐘")

[**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText)程式會在螢幕上以動畫方式繪製兩個 `Label` 物件，以水準和垂直方式跳動。

## <a name="related-links"></a>相關連結

- [第14章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [第14章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)

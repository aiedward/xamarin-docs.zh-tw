---
title: Xamarin.FormsAbsoluteLayout
description: Xamarin.FormsAbsoluteLayout 是用來以明確的值或與配置大小成正比的值來定位和調整元素的位置和大小。
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a6efa4615f0061c83243f2d00d2d141a51607301
ms.sourcegitcommit: 808ff109928a1eea16e17e23ea81f8c903a239e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88181494"
---
# <a name="no-locxamarinforms-absolutelayout"></a>Xamarin.FormsAbsoluteLayout

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)

[![：：：無-loc (Xamarin. Forms) ：：： AbsoluteLayout](absolutelayout-images/layouts.png)](absolutelayout-images/layouts-large.png#lightbox)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)是用來定位和調整使用明確值的子系。 位置是由相對於的左上角 `AbsoluteLayout` （裝置獨立單位）之子系的左上角所指定。 `AbsoluteLayout`也會執行比例的定位和調整大小功能。 此外，不同于其他的版面配置類別， `AbsoluteLayout` 可以定位子系，使其重迭。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)只有當您可以在子系上施加大小，或當元素的大小不會影響其他子系的位置時，才應該將視為特殊用途的版面配置。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)類別會定義下列屬性：

- [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)，屬於類型 [`Rectangle`](xref:Xamarin.Forms.Rectangle) ，這是代表子系位置和大小的附加屬性。 這個屬性的預設值為 (0，0，AutoSize，AutoSize) 。
- [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)，屬於類型 [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) ，這是附加屬性，指出用來定位和調整子系的配置界限屬性是否按比例轉譯。 此屬性的預設值為 `AbsoluteLayoutFlags.None`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結和樣式的目標。 如需附加屬性的詳細資訊，請參閱[ Xamarin.Forms 附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)類別衍生自 `Layout<T>` 類別，其定義 `Children` 類型的屬性 `IList<T>` 。 `Children`屬性是類別的 `ContentProperty` `Layout<T>` ，因此不需要從 XAML 明確設定。

> [!TIP]
> 若要取得最佳的版面配置效能，請遵循[優化版面配置效能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)中的指導方針。

## <a name="position-and-size-children"></a>位置和大小子系

中的子系位置和大小 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) ，是藉由 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 使用絕對值或比例值來設定每個子系的附加屬性來定義。 當位置應該調整時，可以混合子系的絕對和比例值，但大小應維持固定，或反之亦然。 如需絕對值的詳細資訊，請參閱[絕對位置和調整大小](#absolute-positioning-and-sizing)。 如需比例值的相關資訊，請參閱[比例定位和調整大小](#proportional-positioning-and-sizing)。

您 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 可以使用兩種格式來設定附加屬性，而不論是否使用絕對值或比例值：

- `x, y`. 使用此格式時， `x` 和 `y` 值會指出子系相對於其父代的左上角位置。 子系不受限制，而且大小本身。
- `x, y, width, height`. 使用此格式時， `x` 和 `y` 值會指出子系相對於其父代的左上角位置，而 `width` 和 `height` 值則表示子系的大小。

若要以水準或垂直方式（或兩者）來指定子系大小，請將 `width` 和/或 `height` 值設定為 [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) 屬性。 不過，過度利用此屬性可能會傷害應用程式效能，因為它會導致配置引擎執行其他版面配置計算。

> [!IMPORTANT]
> [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性不會影響的子系 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 。

## <a name="absolute-positioning-and-sizing"></a>絕對位置和調整大小

根據預設， [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 位置和大小的子系使用絕對值（以與裝置無關的單位指定），其明確定義在配置中放置子系的位置。 將子系加入至的 `Children` 集合 `AbsoluteLayout` ，並將每個子系的 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 附加屬性設定為絕對位置和/或大小值，即可達成此目的。

> [!WARNING]
> 使用絕對值來定位和調整大小的子系可能會造成問題，因為不同的裝置有不同的螢幕大小和解析度。 因此，在一個裝置上，螢幕中心的座標可能會在其他裝置上位移。

下列 XAML [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 會顯示其子系使用絕對值定位的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <AbsoluteLayout Margin="20">
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
        <Label Text="Stylish Header"
               FontSize="24"
               AbsoluteLayout.LayoutBounds="30, 25" />
    </AbsoluteLayout>
</ContentPage>
```

在此範例中，每個物件的位置 [`BoxView`](xref:Xamarin.Forms.BoxView) 都是使用附加屬性中所指定的前兩個絕對值來定義 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 。 每個的大小 `BoxView` 都是使用第三個和每個值來定義。 物件的位置 [`Label`](xref:Xamarin.Forms.Label) 是使用附加屬性中所指定的兩個絕對值所定義 `AbsoluteLayout.LayoutBounds` 。 未指定的大小值 `Label` ，因此不受限制且大小本身。 在所有情況下，絕對值代表與裝置無關的單位。

下列螢幕擷取畫面顯示產生的版面配置：

![使用絕對值放在 AbsoluteLayout 中的子系](absolutelayout-images/absolute-values.png)

對等的 c # 程式碼如下所示：

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        AbsoluteLayout absoluteLayout = new AbsoluteLayout
        {
            Margin = new Thickness(20)
        };

        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver,
        }, new Rectangle(0, 10, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(0, 20, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(10, 0, 5, 65));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(20, 0, 5, 65));

        absoluteLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, new Point(30,25));                    

        Title = "Stylish header demo";
        Content = absoluteLayout;
    }
}
```

在此範例中，每個的位置和大小 [`BoxView`](xref:Xamarin.Forms.BoxView) 都是使用 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 物件所定義。 的位置 [`Label`](xref:Xamarin.Forms.Label) 是使用物件所定義 [`Point`](xref:Xamarin.Forms.Point) 。

在 c # 中，您也可以 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 使用方法，在將其加入至集合之後，設定其子系的位置和大小 `Children` [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) 。 這個方法的第一個引數是子系，而第二個是 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 物件。

> [!NOTE]
> [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)使用絕對值的可以定位和調整子系，使其不符合配置的範圍。

## <a name="proportional-positioning-and-sizing"></a>比例定位和調整大小

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)可以使用比例值來定位和調整子系。 將子系加入至的 `Children` 集合 `AbsoluteLayout` ，並將 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 每個子系的附加屬性設定為範圍0-1 中的比例位置和/或大小值，即可達成此目的。 位置和大小值會藉由 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 在每個子系上設定附加屬性來進行比例。

[`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)附加屬性的類型為 [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) ，可讓您設定旗標，指出子系的版面配置界限位置和大小值，與的大小成正比 `AbsoluteLayout` 。 配置子系時，會 `AbsoluteLayout` 適當地將位置和大小值調整為任何裝置大小。

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)列舉會定義下列成員：

- `None`，表示值將會解讀為絕對。 這是附加屬性的預設值 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 。
- `XProportional`，表示 `x` 值將會以比例方式解讀，而將所有其他值視為絕對值。
- `YProportional`，表示 `y` 值將會以比例方式解讀，而將所有其他值視為絕對值。
- `WidthProportional`，表示 `width` 值將會以比例方式解讀，而將所有其他值視為絕對值。
- `HeightProportional`，表示 `height` 值將會以比例方式解讀，而將所有其他值視為絕對值。
- `PositionProportional`，表示 `x` 和 `y` 值將會被視為比例，而大小值則會解讀為絕對。
- `SizeProportional`，表示 `width` 和 `height` 值將會被視為比例，而位置值則會解讀為絕對。
- `All`，表示所有值都會以比例轉譯。

> [!TIP]
> [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)列舉是 `Flags` 列舉，這表示可以合併列舉成員。 這會在 XAML 中以逗號分隔的清單來完成，並在 c # 中使用位 OR 運算子。

例如，如果您使用旗標， `SizeProportional` 並將子系的寬度設為0.25，並將高度設定為0.1，則子系會是寬度的四分之一，而高度的一季 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 。 `PositionProportional`旗標類似。  (0、0) 的位置會將子系放在左上角，而 (1，1) 會將子系放在右下角，而 (0.5，0.5) 的位置會將子系置中 `AbsoluteLayout` 。

下列 XAML [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 會顯示其子系使用比例值定位的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.ProportionalDemoPage"
             Title="Proportional demo">
    <AbsoluteLayout>
        <BoxView Color="Blue"
                 AbsoluteLayout.LayoutBounds="0.5,0,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Green"
                 AbsoluteLayout.LayoutBounds="0,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Red"
                 AbsoluteLayout.LayoutBounds="1,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Black"
                 AbsoluteLayout.LayoutBounds="0.5,1,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <Label Text="Centered text"
               AbsoluteLayout.LayoutBounds="0.5,0.5,110,25"
               AbsoluteLayout.LayoutFlags="PositionProportional" />
    </AbsoluteLayout>
</ContentPage>
```

在此範例中，每個子系都使用比例值來定位，但使用絕對值進行大小調整。 這是藉由將 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 每個子系的附加屬性設定為來完成 `PositionProportional` 。 針對每個子系，附加屬性中所指定的前兩個值，會 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 使用比例值來定義位置。 每個子系的大小都是使用與裝置無關的單位來定義的第三個和向下的絕對值。

下列螢幕擷取畫面顯示產生的版面配置：

![使用比例位置值放在 AbsoluteLayout 中的子系](absolutelayout-images/proportional-position.png)

對等的 c # 程式碼如下所示：

```csharp
public class ProportionalDemoPageCS : ContentPage
{
    public ProportionalDemoPageCS()
    {
        BoxView blue = new BoxView { Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds(blue, new Rectangle(0.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags(blue, AbsoluteLayoutFlags.PositionProportional);

        BoxView green = new BoxView { Color = Color.Green };
        AbsoluteLayout.SetLayoutBounds(green, new Rectangle(0, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(green, AbsoluteLayoutFlags.PositionProportional);

        BoxView red = new BoxView { Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds(red, new Rectangle(1, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(red, AbsoluteLayoutFlags.PositionProportional);

        BoxView black = new BoxView { Color = Color.Black };
        AbsoluteLayout.SetLayoutBounds(black, new Rectangle(0.5, 1, 100, 25));
        AbsoluteLayout.SetLayoutFlags(black, AbsoluteLayoutFlags.PositionProportional);

        Label label = new Label { Text = "Centered text" };
        AbsoluteLayout.SetLayoutBounds(label, new Rectangle(0.5, 0.5, 110, 25));
        AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.PositionProportional);

        Title = "Proportional demo";
        Content = new AbsoluteLayout
        {
            Children = { blue, green, red, black, label }
        };
    }
}
```

在此範例中，會使用方法來設定每個子系的位置和大小 [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) 。 方法的第一個引數是子系，而第二個是 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 物件。 每個子系的位置都是以比例值設定，而每個子系的大小則是使用與裝置無關的單位來設定的絕對值。

> [!NOTE]
> [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)使用比例值的可以定位和調整子系，使其無法在配置的界限內使用0-1 範圍以外的值。

## <a name="related-links"></a>相關連結

- [ (範例) 的 AbsoluteLayout 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)
- [Xamarin.Forms附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [選擇 Xamarin.Forms 版面配置](choose-layout.md)
- [改善 Xamarin.Forms 應用程式效能](~/xamarin-forms/deploy-test/performance.md)

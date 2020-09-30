---
title: Xamarin.Forms AbsoluteLayout
description: Xamarin.FormsAbsoluteLayout 可用來使用明確值來定位和調整元素大小，或將值與版面配置大小成正比。
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2020
ms.custom: contperfq1
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0468d132ee6e75ab75c0150f1f5c9e0af6dfde40
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557773"
---
# <a name="no-locxamarinforms-absolutelayout"></a>Xamarin.Forms AbsoluteLayout

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)

[![：：：非 loc (Xamarin. Forms) ：：： AbsoluteLayout](absolutelayout-images/layouts.png)](absolutelayout-images/layouts-large.png#lightbox)

使用 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 明確值來定位和調整子系的大小。 位置是以與裝置無關的單位相對於左上角之子系的左上角來指定 `AbsoluteLayout` 。 `AbsoluteLayout` 也會執行依比例調整的定位和調整大小功能。 此外，與其他某些版面配置類別不同的 `AbsoluteLayout` 是，可以定位子系以便重迭。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)應該視為特殊用途的版面配置，只有當您可以在子系上強加大小，或當元素的大小不會影響其他子系的位置時才使用。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)類別會定義下列屬性：

- [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)，屬於型別 [`Rectangle`](xref:Xamarin.Forms.Rectangle) ，其為代表子系位置和大小的附加屬性。 這個屬性的預設值是 (0、0、自動調整、自動調整) 。
- [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)型別 [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) 為的，其為附加屬性，指出用來定位和調整大小子系的配置界限屬性是否按比例進行轉譯。 此屬性的預設值為 `AbsoluteLayoutFlags.None`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結和樣式的目標。 如需附加屬性的詳細資訊，請參閱[ Xamarin.Forms 附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)類別衍生自 `Layout<T>` 類別，該類別會定義 `Children` 型別的屬性 `IList<T>` 。 `Children`屬性是類別的 `ContentProperty` `Layout<T>` ，因此不需要從 XAML 明確設定。

> [!TIP]
> 若要取得最佳的版面配置效能，請遵循將 [版面配置效能優化](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)的指導方針。

## <a name="position-and-size-children"></a>位置和大小子系

中的子系位置和大小 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 是藉由 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 使用絕對值或比例值來設定每個子系的附加屬性所定義。 當位置應該調整時，子系的絕對與比例值可以混用，但大小應該保持固定，反之亦然。 如需絕對值的詳細資訊，請參閱 [絕對位置和調整大小](#absolute-positioning-and-sizing)。 如需有關比例值的詳細資訊，請參閱 [比例定位和調整大小](#proportional-positioning-and-sizing)。

您 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 可以使用兩種格式來設定附加屬性，不論是否使用絕對值或比例值：

- `x, y`. 使用此格式時， `x` 和 `y` 值會指出子系相對於其父系之左上角的位置。 子系不受限制且本身的大小。
- `x, y, width, height`. 使用此格式時， `x` 和 `y` 值會指出子系相對於其父系之左上角的位置，而值則表示子系的 `width` `height` 大小。

若要將子系大小指定為水準或垂直，或同時指定兩者，請將 `width` 和/或 `height` 值設定為 [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) 屬性。 不過，過度利用這個屬性可能會危害應用程式效能，因為這會導致版面配置引擎執行其他的版面配置計算。

> [!IMPORTANT]
> [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性不會影響的子系 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 。

## <a name="absolute-positioning-and-sizing"></a>絕對位置和調整大小

依預設， [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 會使用絕對值（以與裝置無關的單位來指定）的位置和大小子系，以明確定義在配置中放置子系的位置。 將子系加入至的 `Children` 集合 `AbsoluteLayout` ，並將 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 每個子系上的附加屬性設定為絕對位置和/或大小值，即可達成這個目的。

> [!WARNING]
> 使用絕對值來定位和調整大小的子系可能會有問題，因為不同的裝置具有不同的螢幕大小和解析度。 因此，在某個裝置上，畫面中央的座標可能會在其他裝置上位移。

下列 XAML 顯示其子系 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 使用絕對值放置的：

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

在此範例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 會使用附加屬性中指定的前兩個絕對值來定義每個物件的位置 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 。 每個的大小 `BoxView` 都是使用第三個和第三個值來定義。 [`Label`](xref:Xamarin.Forms.Label)使用附加屬性中所指定的兩個絕對值來定義物件的位置 `AbsoluteLayout.LayoutBounds` 。 未指定的大小值 `Label` ，因此不受限制且大小本身。 在所有情況下，絕對值代表與裝置無關的單位。

下列螢幕擷取畫面顯示產生的版面配置：

![使用絕對值放置於 AbsoluteLayout 中的子系](absolutelayout-images/absolute-values.png)

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

在此範例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 會使用物件來定義每個的位置和大小 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 。 [`Label`](xref:Xamarin.Forms.Label)使用物件定義的位置 [`Point`](xref:Xamarin.Forms.Point) 。

在 c # 中，您也可以 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 使用方法，在將其加入至集合之後，設定的子系的位置和大小 `Children` [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) 。 這個方法的第一個引數是子系，而第二個引數是 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 物件。

> [!NOTE]
> [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)使用絕對值的可定位和調整子系的大小，使其不符合配置的界限。

## <a name="proportional-positioning-and-sizing"></a>比例定位和調整大小

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)可以使用比例值來定位和調整子系的大小。 將子系加入至的 `Children` 集合 `AbsoluteLayout` ，以及將 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 每個子系上的附加屬性設定為範圍0-1 中的比例位置和/或大小值，即可達成這個目的。 位置和大小值是 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 在每個子系上設定附加屬性來進行比例設定。

[`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)附加的屬性（類型為 [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) ）可讓您設定旗標，指出子系的版面配置界限位置和大小值與的大小成正比 `AbsoluteLayout` 。 配置子系時，會 `AbsoluteLayout` 適當地將位置和大小值調整為任何裝置大小。

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)列舉會定義下列成員：

- `None`，表示值會被視為絕對。 這是附加屬性的預設值 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 。
- `XProportional`，表示 `x` 值會被解讀為比例，同時將所有其他值視為絕對值。
- `YProportional`，表示 `y` 值會被解讀為比例，同時將所有其他值視為絕對值。
- `WidthProportional`，表示 `width` 值會被解讀為比例，同時將所有其他值視為絕對值。
- `HeightProportional`，表示 `height` 值會被解讀為比例，同時將所有其他值視為絕對值。
- `PositionProportional`，指出 `x` 和 `y` 值會被視為比例，而大小值則會被視為絕對。
- `SizeProportional`：表示 `width` 和 `height` 值會被視為比例，而位置值則會被視為絕對。
- `All`，表示所有值都會以比例解讀。

> [!TIP]
> [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)列舉是 `Flags` 列舉，這表示可以結合列舉成員。 這是在 XAML 中以逗號分隔的清單來完成，而在 c # 中則以位 OR 運算子來完成。

例如，如果您使用旗標， `SizeProportional` 並將子系的寬度設為0.25，而將高度設定為0.1，則子系會是寬度的四分之一， [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 以及高度的四分之一。 `PositionProportional`旗標很類似。  (0、0) 的位置會將子系放在左上角，而 (1、1) 將子系放在右下角，而 (0.5，0.5) 的位置會將子系放在中 `AbsoluteLayout` 。

下列 XAML 顯示其子系 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 使用比例值放置的子系：

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

在此範例中，每個子系都使用比例值放置，但使用絕對值來調整大小。 這是藉由將 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 每個子系的附加屬性設定為來完成 `PositionProportional` 。 針對每個子系，在附加屬性中指定的前兩個值會 [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 定義使用比例值的位置。 每個子系的大小會使用與裝置無關的單位，以第三個和第三個絕對值來定義。

下列螢幕擷取畫面顯示產生的版面配置：

![使用比例位置值放置於 AbsoluteLayout 中的子系](absolutelayout-images/proportional-position.png)

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

在此範例中，每個子系的位置和大小會使用方法來設定 [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) 。 方法的第一個引數是子系，而第二個引數是 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 物件。 每個子系的位置都是以比例值設定，而每個子系的大小則是使用與裝置無關的單位來設定的絕對值。

> [!NOTE]
> [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)使用比例值的，可以定位和調整子系，使其不符合配置的範圍，方法是使用0-1 範圍以外的值。

## <a name="related-links"></a>相關連結

- [AbsoluteLayout 示範 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)
- [Xamarin.Forms 附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [選擇 Xamarin.Forms 版面配置](choose-layout.md)
- [改善 Xamarin.Forms 應用程式效能](~/xamarin-forms/deploy-test/performance.md)
---
title: Xamarin.Forms RelativeLayout
description: Xamarin.FormsRelativeLayout 是使用位置和大小子系相對於版面配置或同級元素的屬性。
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2020
ms.custom: contperfq1
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d350ceee778c9f9ba9f25555a89a925558c6d38b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556837"
---
# <a name="no-locxamarinforms-relativelayout"></a>Xamarin.Forms RelativeLayout

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)

[![：：：非 loc (Xamarin. Forms) ：：： RelativeLayout](relativelayout-images/layouts.png)](relativelayout-images/layouts-large.png#lightbox)

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)是用來放置與配置相對於版面配置或同級元素屬性的子系。 這可讓您建立可依比例調整跨裝置大小的 Ui。 此外，與其他某些版面配置類別不同的 `RelativeLayout` 是，可以定位子系以便重迭。

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)類別會定義下列屬性：

- [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty)，屬於型別 [`Constraint`](xref:Xamarin.Forms.Constraint) ，其為代表子系 X 位置之條件約束的附加屬性。
- [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty)，屬於型別 [`Constraint`](xref:Xamarin.Forms.Constraint) ，其為代表子系 Y 位置之條件約束的附加屬性。
- [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty)，屬於型別 [`Constraint`](xref:Xamarin.Forms.Constraint) ，其為代表子系寬度之條件約束的附加屬性。
- [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty)，屬於型別 [`Constraint`](xref:Xamarin.Forms.Constraint) ，其為代表子系高度之條件約束的附加屬性。
- [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty)型別 [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) 為的，其為代表子系位置和大小條件約束的附加屬性。 這個屬性無法輕易地從 XAML 取用。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結和樣式的目標。 如需附加屬性的詳細資訊，請參閱[ Xamarin.Forms 附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

> [!NOTE]
> 在中，子系的寬度和高度 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 也可以透過子系的 `WidthRequest` 和屬性指定 `HeightRequest` ，而不是 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性。

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)類別衍生自 `Layout<T>` 類別，該類別會定義 `Children` 型別的屬性 `IList<T>` 。 `Children`屬性是類別的 `ContentProperty` `Layout<T>` ，因此不需要從 XAML 明確設定。

> [!TIP]
> 請盡可能避免使用 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 。 它會導致 CPU 必須執行更多工作。

## <a name="constraints"></a>條件約束

在中 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ，子系的位置和大小會指定為使用絕對值或相對值的條件約束。 未指定條件約束時，子系將放置於配置的左上角。

下表說明如何在 XAML 和 c # 中指定條件約束：

|     | XAML | C# |
| --- | ---- | -- |
| **絕對值** | 絕對條件約束的指定方式是將 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 附加屬性設定為 `double` 值。 | 絕對條件約束是由 [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) 方法指定，或使用 `Children.Add` 需要引數的多載來指定 `Func<Rectangle>` 。 |
| **相對值** | 相對條件約束的指定方式是將 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 附加屬性設定為 [`Constraint`](xref:Xamarin.Forms.Constraint) 標記延伸所傳回的物件 `ConstraintExpression` 。 | 相對條件約束是由 [`Constraint`](xref:Xamarin.Forms.Constraint) 類別的方法所傳回的物件所指定 `Constraint` 。 |

如需使用絕對值來指定條件約束的詳細資訊，請參閱 [絕對位置和調整大小](#absolute-positioning-and-sizing)。 如需使用相對值指定條件約束的詳細資訊，請參閱 [相對定位和調整大小](#relative-positioning-and-sizing)。

在 c # 中，子系可以 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 由三個多載加入至 `Add` 。 第一個多載需要 `Expression<Func<Rectangle>>` 指定子系的位置和大小。 第二個多載需要 `Expression<Func<double>>` `x` 、 `y` 、 `width` 和 `height` 引數的選擇性物件。 第三個多載需要 `Constraint` `x` 、 `y` 、 `width` 和 `height` 引數的選擇性物件。

您可以 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 使用 [`SetXConstraint`](xref:Xamarin.Forms.RelativeLayout.SetXConstraint*) 、 [`SetYConstraint`](xref:Xamarin.Forms.RelativeLayout.SetYConstraint*) 、 [`SetWidthConstraint`](xref:Xamarin.Forms.RelativeLayout.SetWidthConstraint*) 和 [`SetHeightConstraint`](xref:Xamarin.Forms.RelativeLayout.SetHeightConstraint*) 方法來變更中子系的位置和大小。 每個方法的第一個引數是子系，而第二個引數是 [`Constraint`](xref:Xamarin.Forms.Constraint) 物件。 此外，此 [`SetBoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.SetBoundsConstraint*) 方法也可以用來變更子系的位置和大小。 這個方法的第一個引數是子系，而第二個引數是 [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) 物件。

## <a name="absolute-positioning-and-sizing"></a>絕對位置和調整大小

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)可以使用絕對值來定位和調整子系的大小（以與裝置無關的單位來指定），以明確地定義要在版面配置中放置子系的位置。 將子系加入至的集合， `Children` `RelativeLayout` 並將 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 、 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 、 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性設定為絕對位置和/或大小值，即可達成這個目的。

> [!WARNING]
> 使用絕對值來定位和調整大小的子系可能會有問題，因為不同的裝置具有不同的螢幕大小和解析度。 因此，在某個裝置上，畫面中央的座標可能會在其他裝置上位移。

下列 XAML 顯示其子系 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 使用絕對值來定位的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <RelativeLayout Margin="20">
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="10"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="20"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="10"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="20"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <Label Text="Stylish header"
               FontSize="24"
               RelativeLayout.XConstraint="30"
               RelativeLayout.YConstraint="25" />
    </RelativeLayout>
</ContentPage>
```

在此範例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 會使用 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和附加屬性中指定的值來定義每個物件的位置 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 。 每個的大小 `BoxView` 都是使用 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和附加屬性中指定的值來定義 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 。 [`Label`](xref:Xamarin.Forms.Label)此外，也會使用 `XConstraint` 和附加屬性中指定的值來定義物件的位置 `YConstraint` 。 但是，不會指定的大小值 `Label` ，因此不受限制且本身的大小。 在所有情況下，絕對值代表與裝置無關的單位。

下列螢幕擷取畫面顯示產生的版面配置：

![使用絕對值放置於 RelativeLayout 中的子系](relativelayout-images/absolute-values.png)

對等的 c # 程式碼如下所示：

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout
        {
            Margin = new Thickness(20)
        };

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 10, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 20, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(10, 0, 5, 65));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(20, 0, 5, 65));

        relativeLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, Constraint.Constant(30), Constraint.Constant(25));

        Title = "Stylish header demo";
        Content = relativeLayout;
    }
}
```

在此範例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 會使用多載 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) `Add` （需要 `Expression<Func<Rectangle>>` 指定每個子系的位置和大小），將物件新增至。 的位置 [`Label`](xref:Xamarin.Forms.Label) 是使用需要選擇性物件的多載所定義 `Add` [`Constraint`](xref:Xamarin.Forms.Constraint) ，在此案例中是由方法所建立 [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) 。

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)使用絕對值的，可以定位和調整子系的大小，使其無法容納在配置的界限內。

## <a name="relative-positioning-and-sizing"></a>相對定位和調整大小

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)可以使用相對於配置屬性或同級專案的值，來放置和調整子系的大小。 將子系加入至的集合， `Children` `RelativeLayout` 並 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 使用物件將、、 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性設定為相對值，即可 [`Constraint`](xref:Xamarin.Forms.Constraint) 達成這個目的。

條件約束可以是常數、相對於父代或相對於同級。 條件約束的類型是由列舉所表示 [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) ，它會定義下列成員：

- `RelativeToParent`，表示相對於父系的條件約束。
- `RelativeToView`，表示相對於 view (或同級) 的條件約束。
- `Constant`，表示常數條件約束。

### <a name="constraint-markup-extension"></a>條件約束標記延伸

在 XAML 中， [`Constraint`](xref:Xamarin.Forms.Constraint) 標記延伸可以建立物件 [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) 。 這個標記延伸通常用來將中的子系的位置和大小與 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 父代或同級的位置和大小建立關聯。

[`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)類別會定義下列屬性：

- [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant)， `double` 代表條件約束常數值的型別。
- [`ElementName`](xref:Xamarin.Forms.ConstraintExpression.ElementName)， `string` 代表要針對其計算條件約束之來源元素的名稱。
- [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor)，代表用 `double` 來調整受條件約束之維度（相對於來源元素）的因數。 這個屬性的預設值為1。
- [`Property`](xref:Xamarin.Forms.ConstraintExpression.Property)型別 `string` ，代表要在條件約束計算中使用之來源元素上的屬性名稱。
- [`Type`](xref:Xamarin.Forms.ConstraintExpression.Type)， [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) 代表條件約束的類型。

如需標記延伸的詳細資訊 Xamarin.Forms ，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。

下列 XAML [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 會顯示其子系受 [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) 標記延伸條件約束的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.RelativePositioningAndSizingDemoPage"
             Title="RelativeLayout demo">
    <RelativeLayout>
        <BoxView Color="Red"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Green"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Silver"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}" />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Black"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=X}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Y}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Height, Factor=0.33}" />
    </RelativeLayout>
</ContentPage>
```

在此範例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 會藉由設定 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和附加屬性來定義每個物件的位置 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 。 第一個 `BoxView` `XConstraint` `YConstraint` 會將其和附加屬性設定為常數，也就是絕對值。 其餘的 `BoxView` 物件都會使用至少一個相對值來設定其位置。 例如，黃色物件會 `BoxView` 將 `XConstraint` 附加屬性設定為其父項的寬度 ([`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)) 減去40。 同樣地，這會 `BoxView` 將 `YConstraint` 附加屬性設定為其父系減40的高度。 這可確保黃色 `BoxView` 出現在螢幕的右下角。

> [!NOTE]
> [`BoxView`](xref:Xamarin.Forms.BoxView) 未指定大小的物件會自動調整大小以供 40x40 Xamarin.Forms 。

名為的銀級 [`BoxView`](xref:Xamarin.Forms.BoxView) `oneThird` 會集中定位（相對於其父系）。 它也會相對於其父系調整大小，這是其寬度和高度的三分之一。 這是藉由將 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 附加屬性設定為) 的父系 (寬度 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 乘以0.33 來達成。 同樣地， [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性會設定為父系的高度，乘以0.33。

黑色的 [`BoxView`](xref:Xamarin.Forms.BoxView) 位置和大小相對於 `oneThird` `BoxView` 。 這是藉由將其 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 附加屬性分別設定 `X` 為 `Y` 同一個同級元素的和值來達成。 同樣地，其大小會設定為其同級元素寬度和高度的三分之一。 這是藉由將其 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性分別設定 `Width` 為 `Height` 同級元素的和值，然後乘以0.33 來達成。

下列螢幕擷取畫面顯示產生的版面配置：

![使用相對值放置於 RelativeLayout 中的子系](relativelayout-images/relative-values.png)

### <a name="constraint-objects"></a>條件約束物件

[`Constraint`](xref:Xamarin.Forms.Constraint)類別會定義下列會傳回物件的公用靜態方法 `Constraint` ：

- [`Constant`](xref:Xamarin.Forms.Constraint.Constant*)，會將子系限制為以指定的大小 `double` 。
- [`FromExpression`](xref:Xamarin.Forms.Constraint.FromExpression*)，會使用 lambda 運算式來限制子系。
- [`RelativeToParent`](xref:Xamarin.Forms.Constraint.RelativeToParent*)，會限制相對於其父代大小的子系。
- [`RelativeToView`](xref:Xamarin.Forms.Constraint.RelativeToView*)，這會限制相對於視圖大小的子系。

此外，此 [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) 類別會定義單一方法，這個方法會傳回，以 [`FromExpression`](xref:Xamarin.Forms.BoundsConstraint.FromExpression*) `BoundsConstraint` 限制子系的位置和大小 `Expression<Func<Rectangle>>` 。 這個方法可以用來設定 [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty) 附加屬性。

下列 c # [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 程式碼會顯示其子系受 [`Constraint`](xref:Xamarin.Forms.Constraint) 物件限制的。

```csharp
public class RelativePositioningAndSizingDemoPageCS : ContentPage
{
    public RelativePositioningAndSizingDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout();

        // Four BoxView's
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Red },
            Constraint.Constant(0),
            Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Green },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }), Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Blue },
            Constraint.Constant(0),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Yellow },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        // Centered and 1/3 width and height of parent
        BoxView silverBoxView = new BoxView { Color = Color.Silver };
        relativeLayout.Children.Add(
            silverBoxView,
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }));

        // 1/3 width and height of previous
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Black },
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.X;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Y;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Width * 0.33;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Height * 0.33;
            }));

        Title = "RelativeLayout demo";
        Content = relativeLayout;
    }
}
```

在此範例中，會使用多載，將子系加入至，這些多載 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) `Add` 需要 `Constraint` 、、 `x` `y` `width` 和 `height` 引數的選擇性物件。

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)使用相對值的可定位和調整子系的大小，使其無法容納在配置的界限內。

## <a name="related-links"></a>相關連結

- [RelativeLayout 示範 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)
- [Xamarin.Forms 附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [選擇 Xamarin.Forms 版面配置](choose-layout.md)
- [改善 Xamarin.Forms 應用程式效能](~/xamarin-forms/deploy-test/performance.md)
---
title: Xamarin.FormsRelativeLayout
description: Xamarin.FormsRelativeLayout 是相對於版面配置或同級元素的屬性，使用位置和大小的子系。
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b23da34239d99faa64578bd30c5a3e969cf4b289
ms.sourcegitcommit: 808ff109928a1eea16e17e23ea81f8c903a239e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88181770"
---
# <a name="no-locxamarinforms-relativelayout"></a>Xamarin.FormsRelativeLayout

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)

[![：：：無-loc (Xamarin. Forms) ：：： RelativeLayout](relativelayout-images/layouts.png)](relativelayout-images/layouts-large.png#lightbox)

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)是用來相對於版面配置或同級元素的屬性來定位和調整子系。 如此一來，就可以在不同的裝置大小之間依比例來建立 Ui。 此外，不同于其他的版面配置類別， `RelativeLayout` 可以定位子系，使其重迭。

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)類別會定義下列屬性：

- [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty)，屬於類型 [`Constraint`](xref:Xamarin.Forms.Constraint) ，這是附加屬性，代表子系 X 位置的條件約束。
- [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty)，屬於類型 [`Constraint`](xref:Xamarin.Forms.Constraint) ，這是附加屬性，代表子系 Y 位置的條件約束。
- [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty)，屬於類型 [`Constraint`](xref:Xamarin.Forms.Constraint) ，這是代表子系寬度之條件約束的附加屬性。
- [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty)，屬於類型 [`Constraint`](xref:Xamarin.Forms.Constraint) ，這是代表子系高度之條件約束的附加屬性。
- [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty)，屬於類型 [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) ，這是附加屬性，代表子系位置和大小的條件約束。 這個屬性無法輕鬆地從 XAML 取用。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結和樣式的目標。 如需附加屬性的詳細資訊，請參閱[ Xamarin.Forms 附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

> [!NOTE]
> 在中，子系的寬度和高度 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 也可以透過子系的 `WidthRequest` 和屬性指定 `HeightRequest` ，而不是 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性。

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)類別衍生自 `Layout<T>` 類別，其定義 `Children` 類型的屬性 `IList<T>` 。 `Children`屬性是類別的 `ContentProperty` `Layout<T>` ，因此不需要從 XAML 明確設定。

> [!TIP]
> 盡可能避免使用 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 。 它會導致 CPU 必須執行更多工作。

## <a name="constraints"></a>條件約束

在中 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ，會使用絕對值或相對值，將子系的位置和大小指定為條件約束。 當未指定條件約束時，子系會放置在版面配置的左上角。

下表顯示如何在 XAML 和 c # 中指定條件約束：

|     | XAML | C# |
| --- | ---- | -- |
| **絕對值** | 絕對條件約束是藉由將 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 附加屬性設定為值來指定 `double` 。 | 絕對條件約束是由方法所指定 [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) ，或使用 `Children.Add` 需要引數的多載 `Func<Rectangle>` 。 |
| **相對值** | 藉由將 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 附加屬性設定為 [`Constraint`](xref:Xamarin.Forms.Constraint) 標記延伸所傳回的物件，來指定相對條件約束 `ConstraintExpression` 。 | 相對條件約束是由 [`Constraint`](xref:Xamarin.Forms.Constraint) 類別的方法所傳回的物件所指定 `Constraint` 。 |

如需使用絕對值來指定條件約束的詳細資訊，請參閱[絕對位置和調整大小](#absolute-positioning-and-sizing)。 如需使用相對值指定條件約束的詳細資訊，請參閱[相對定位和調整大小](#relative-positioning-and-sizing)。

在 c # 中，子系可以 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 由三個多載新增至 `Add` 。 第一個多載需要 `Expression<Func<Rectangle>>` 指定子系的位置和大小。 第二個多載需要 `Expression<Func<double>>` `x` 、 `y` 、 `width` 和 `height` 引數的選擇性物件。 第三個多載需要 `Constraint` `x` 、 `y` 、 `width` 和 `height` 引數的選擇性物件。

您可以 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 使用 [`SetXConstraint`](xref:Xamarin.Forms.RelativeLayout.SetXConstraint*) 、、 [`SetYConstraint`](xref:Xamarin.Forms.RelativeLayout.SetYConstraint*) [`SetWidthConstraint`](xref:Xamarin.Forms.RelativeLayout.SetWidthConstraint*) 和 [`SetHeightConstraint`](xref:Xamarin.Forms.RelativeLayout.SetHeightConstraint*) 方法來變更中子系的位置和大小。 每個方法的第一個引數都是子系，而第二個是 [`Constraint`](xref:Xamarin.Forms.Constraint) 物件。 此外， [`SetBoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.SetBoundsConstraint*) 方法也可以用來變更子系的位置和大小。 這個方法的第一個引數是子系，而第二個是 [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) 物件。

## <a name="absolute-positioning-and-sizing"></a>絕對位置和調整大小

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)可以使用絕對值來定位和調整子系，這會在裝置獨立單位中指定，以明確定義子系應放置在配置中的位置。 將子系加入至的集合， `Children` `RelativeLayout` 並將每個子系的 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 、 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 、 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性設定為絕對位置和/或大小值，即可達成此目的。

> [!WARNING]
> 使用絕對值來定位和調整大小的子系可能會造成問題，因為不同的裝置有不同的螢幕大小和解析度。 因此，在一個裝置上，螢幕中心的座標可能會在其他裝置上位移。

下列 XAML [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 會顯示其子系使用絕對值定位的：

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

在此範例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 會使用 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和附加屬性中指定的值來定義每個物件的位置 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 。 每個的大小 `BoxView` 都是使用 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和附加屬性中指定的值來定義 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 。 物件的位置 [`Label`](xref:Xamarin.Forms.Label) 也會使用 `XConstraint` 和附加屬性中指定的值來定義 `YConstraint` 。 不過，未指定的大小值 `Label` ，因此不受限制且大小本身。 在所有情況下，絕對值代表與裝置無關的單位。

下列螢幕擷取畫面顯示產生的版面配置：

![使用絕對值放在 RelativeLayout 中的子系](relativelayout-images/absolute-values.png)

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

在此範例中， [`BoxView`](xref:Xamarin.Forms.BoxView) 物件會使用多載（ [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) `Add` 需要 `Expression<Func<Rectangle>>` 指定每個子系的位置和大小）新增至。 的位置 [`Label`](xref:Xamarin.Forms.Label) 是使用需要選擇性物件的多載所定義 `Add` [`Constraint`](xref:Xamarin.Forms.Constraint) ，在此案例中是由方法所建立 [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) 。

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)使用絕對值的可以定位和調整子系，使其不符合配置的範圍。

## <a name="relative-positioning-and-sizing"></a>相對定位和調整大小

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)可以使用相對於版面配置屬性或同級元素的值，來定位和調整子系。 這是藉由將子系加入至的 `Children` 集合， `RelativeLayout` 並將 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 每個子系的、 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 、 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性設定為使用物件的相對值 [`Constraint`](xref:Xamarin.Forms.Constraint) 來達成。

條件約束可以是常數，相對於父系，或相對於兄弟。 條件約束的類型是由列舉表示 [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) ，它會定義下列成員：

- `RelativeToParent`，表示相對於父系的條件約束。
- `RelativeToView`，表示相對於 view (或同輩) 的條件約束。
- `Constant`，表示常數條件約束。

### <a name="constraint-markup-extension"></a>條件約束標記延伸

在 XAML 中， [`Constraint`](xref:Xamarin.Forms.Constraint) 可由標記延伸建立物件 [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) 。 這個標記延伸通常用來將中子系的位置和大小與 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 父代或同級產生關聯。

[`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)類別會定義下列屬性：

- [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant)，屬於類型 `double` ，表示條件約束常數值。
- [`ElementName`](xref:Xamarin.Forms.ConstraintExpression.ElementName)，屬於類型 `string` ，表示要針對其計算條件約束的來源專案名稱。
- [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor)，屬於類型 `double` ，表示用來調整限制維度（相對於來源元素）的因數。 這個屬性的預設值為1。
- [`Property`](xref:Xamarin.Forms.ConstraintExpression.Property)，屬於類型 `string` ，表示要在條件約束計算中使用之來源元素的屬性名稱。
- [`Type`](xref:Xamarin.Forms.ConstraintExpression.Type)，屬於類型 [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) ，表示條件約束的類型。

如需標記延伸的詳細資訊 Xamarin.Forms ，請參閱[XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。

下列 XAML [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 會顯示其子系受到 [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) 標記延伸限制的：

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

在此範例中，每個物件的位置 [`BoxView`](xref:Xamarin.Forms.BoxView) 都是藉由設定 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 附加屬性來定義。 第一個 `BoxView` `XConstraint` `YConstraint` 會將其和附加屬性設定為常數，也就是絕對值。 其餘 `BoxView` 物件全都使用至少一個相對值來設定其位置。 例如，黃色物件會 `BoxView` 將 `XConstraint` 附加屬性設為其父系的寬度 ([`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)) 減去40。 同樣地，這會 `BoxView` 將 `YConstraint` 附加屬性設為其父系減40的高度。 這可確保螢幕的 `BoxView` 右下角會出現黃色。

> [!NOTE]
> [`BoxView`](xref:Xamarin.Forms.BoxView)未指定大小的物件會自動調整大小以供 40x40 Xamarin.Forms 。

名為的銀個 [`BoxView`](xref:Xamarin.Forms.BoxView) `oneThird` 位置是集中的，相對於其父系。 它也會相對於其父系調整大小，並為其寬度和高度的三分之一。 將 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 附加屬性設定為) 的父系 (的寬度 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ，乘以0.33，即可達成此目的。 同樣地， [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性會設定為父系的高度，乘以0.33。

黑色的 [`BoxView`](xref:Xamarin.Forms.BoxView) 位置和大小相對於 `oneThird` `BoxView` 。 這是藉由將其 [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) 和 [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) 附加屬性分別設定 `X` 為 `Y` 兄弟元素的和值，來達成。 同樣地，其大小會設定為其同級元素的寬度和高度的三分之一。 將其 [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) 和 [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) 附加屬性分別設定為 `Width` 兄弟元素的和 `Height` 值，然後乘以0.33，即可達成此目的。

下列螢幕擷取畫面顯示產生的版面配置：

![使用相對值放在 RelativeLayout 中的子系](relativelayout-images/relative-values.png)

### <a name="constraint-objects"></a>條件約束物件

[`Constraint`](xref:Xamarin.Forms.Constraint)類別會定義下列會傳回物件的公用靜態方法 `Constraint` ：

- [`Constant`](xref:Xamarin.Forms.Constraint.Constant*)，其會將子系限制為使用所指定的大小 `double` 。
- [`FromExpression`](xref:Xamarin.Forms.Constraint.FromExpression*)，其使用 lambda 運算式來限制子系。
- [`RelativeToParent`](xref:Xamarin.Forms.Constraint.RelativeToParent*)，其會限制相對於其父系大小的子系。
- [`RelativeToView`](xref:Xamarin.Forms.Constraint.RelativeToView*)，限制相對於視圖大小的子系。

此外，類別會 [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) 定義單一方法， [`FromExpression`](xref:Xamarin.Forms.BoundsConstraint.FromExpression*) 它會傳回，其 `BoundsConstraint` 會使用來限制子系的位置和大小 `Expression<Func<Rectangle>>` 。 這個方法可以用來設定 [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty) 附加屬性。

下列 c # 程式碼顯示， [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 其子系受到 [`Constraint`](xref:Xamarin.Forms.Constraint) 物件的限制：

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

在此範例中，子系會 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 使用多載 `Add` `Constraint` （需要 `x` 、 `y` 、 `width` 和 `height` 引數的選擇性物件）加入至。

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)使用相對值的可以定位和調整子系，使其不符合配置的範圍。

## <a name="related-links"></a>相關連結

- [ (範例) 的 RelativeLayout 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)
- [Xamarin.Forms附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [選擇 Xamarin.Forms 版面配置](choose-layout.md)
- [改善 Xamarin.Forms 應用程式效能](~/xamarin-forms/deploy-test/performance.md)

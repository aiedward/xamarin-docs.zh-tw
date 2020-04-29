---
title: Xamarin. 表單展開器
description: '[Xamarin] 展開控制項提供可擴充的容器來裝載任何內容。 藉由點擊展開器標頭，即可顯示或隱藏內容。'
ms.prod: xamarin
ms.assetid: 381DCB55-522D-4414-B45B-E8DD70AA9985
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2020
ms.openlocfilehash: f47dc08d11a97598966ba7af620cfc81e5fe284f
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532622"
---
# <a name="xamarinforms-expander"></a>Xamarin. 表單展開器

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)

[Xamarin] `Expander`控制項提供可擴充的容器來裝載任何內容。 控制項具有標頭和內容，而且內容會藉由使用`Expander`標頭來顯示或隱藏。 只有在顯示`Expander`標*頭時，* `Expander`才會折迭。 當`Expander`內容可見時， `Expander`就會*展開*。

下列螢幕擷取畫面顯示`Expander`在其折迭和展開的狀態中，並以紅色方塊表示標頭和內容：

![在 iOS 和 Android 上折迭和展開狀態的展開器螢幕擷取畫面](expander-images/expander.png "IOS 和 Android 上的擴充器")

> [!IMPORTANT]
> `Expander`目前為實驗性，而且只能透過設定`Expander_Experimental`旗標來使用。 如需詳細資訊，請參閱[實驗性旗標](~/xamarin-forms/internals/experimental-flags.md)。

`Expander`控制項會定義下列屬性：

- `CollapseAnimationEasing`，屬於類型[`Easing`](xref:Xamarin.Forms.Easing)，表示要在折迭時套用至`Expander`內容的緩動函式。
- `CollapseAnimationLength`，屬於類型`uint`，可在折迭時`Expander`定義動畫的持續時間。 此屬性的預設值為250毫秒。
- `Command`，屬於類型`ICommand`，這是在按`Expander`下標頭時執行的。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。
- `Content`，屬於類型[`View`](xref:Xamarin.Forms.View)，定義`Expander`展開時要顯示的內容。
- `ContentTemplate`，屬於類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，這是用來動態擴充內容的範本`Expander`。
- `ExpandAnimationEasing`，屬於類型[`Easing`](xref:Xamarin.Forms.Easing)，表示要在展開期間套用至`Expander`內容的緩動函式。
- `ExpandAnimationLength`，屬於類型`uint`，可在`Expander`展開時定義動畫的持續時間。 此屬性的預設值為250毫秒。
- `ForceUpdateSizeCommand`，屬於類型`ICommand`，定義強制更新大小`Expander`時所執行的命令。 這個屬性會使用`OneWayToSource`系結模式。
- `Header`，屬於類型[`View`](xref:Xamarin.Forms.View)，定義標頭內容。
- `IsExpanded`，屬於類型`bool`，可判斷`Expander`是否已展開。 這個屬性會使用`TwoWay`系結模式，而且具有的`false`預設值。
- `Spacing`，屬於類型`double`，表示標頭和其內容之間的間距。 這個屬性的預設值為 0。
- `State`，屬於類型`ExpanderState`，表示的狀態`Expander`。 這個屬性會使用`OneWayToSource`系結模式。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標，以及樣式化的。

> [!NOTE]
> `Content`屬性是`Expander`類別的 content 屬性，因此不需要從 XAML 明確設定。

`ExpanderState` 列舉會定義下列成員：

- `Expanding`表示`Expander`正在擴充。
- `Expanded`表示`Expander`已展開。
- `Collapsing`表示`Expander`正在折迭。
- `Collapsed`表示`Expander`已折迭。

`Expander`控制項也會定義在`Tapped`按`Expander`標頭時所引發的事件。 此外， `Expander`也包含可`ForceUpdateSize`呼叫以在執行時間以程式設計方式`Expander`調整的方法。

## <a name="create-an-expander"></a>建立展開器

下列範例顯示如何`Expander`在 XAML 中具現化：

```xaml
<Expander>
    <Expander.Header>
        <Label Text="Baboon"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Grid Padding="10">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
        <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
               Aspect="AspectFill"
               HeightRequest="120"
               WidthRequest="120" />
        <Label Grid.Column="1"
               Text="Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae."
               FontAttributes="Italic" />
    </Grid>
</Expander>
```

在此範例中， `Expander`預設會折迭，並顯示[`Label`](xref:Xamarin.Forms.Label)做為其標頭。 在標頭上點擊會導致`Expander`展開以顯示其內容，也就是[`Grid`](xref:Xamarin.Forms.Grid)包含子控制項的。 `Expander`展開時，將其標頭折迭`Expander`。

> [!IMPORTANT]
> 以隱含或`Expander.Content`明確的方式設定屬性時，會`Expander`在流覽包含它的頁面時建立內容，即使`Expander`已折迭也一樣。 不過，您`Expander.ContentTemplate`可以將屬性設定為只有在第一次`Expander`展開時才會膨脹的內容。 如需詳細資訊，請參閱[視需要建立擴充器內容](#create-expander-content-on-demand)。

或者，可以`Expander`在程式碼中建立：

```csharp
Expander expander = new Expander
{
    Header = new Label
    {
        Text = "Baboon",
        FontAttributes = FontAttributes.Bold,
        FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label))
    }
};

Grid grid = new Grid
{
    Padding = new Thickness(10),
    ColumnDefinitions =
    {
        new ColumnDefinition { Width = GridLength.Auto },
        new ColumnDefinition { Width = GridLength.Auto }
    }
};

grid.Children.Add(new Image
{
    Source = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg",
    Aspect = Aspect.AspectFill,
    HeightRequest = 120,
    WidthRequest = 120
});

grid.Children.Add(new Label
{
    Text = "Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae.",
    FontAttributes = FontAttributes.Italic
}, 1, 0);

expander.Content = grid;
```

## <a name="create-expander-content-on-demand"></a>視需要建立擴充器內容

`Expander`您可以視需要建立內容，以回應展開的`Expander` 。 將`Expander.ContentTemplate`屬性設定為包含內容的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，即可完成這項作業：

```xaml
<Expander>
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

在此範例中， `Expander`只有在第一次`Expander`展開時，才會擴大內容。

這種方法的優點是當頁面包含多個`Expander`物件時，只有在使用者第`Expander`一次展開時，才會建立的內容。

## <a name="add-an-expansion-indicator"></a>新增展開指標

[`Image`](xref:Xamarin.Forms.Image)可以新增至`Expander`標頭，以提供展開狀態的視覺指示。 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)可以附加至`Image`，這會根據`Expander.IsExpanded`屬性的值`Source`來變更屬性：

```xaml
<Expander>
    <Expander.Header>
        <Grid>
            <Label Text="{Binding Name}"
                   FontAttributes="Bold"
                   FontSize="Medium" />
            <Image Source="expand.png"
                   HorizontalOptions="End"
                   VerticalOptions="Start">
                <Image.Triggers>
                    <DataTrigger TargetType="Image"
                                 Binding="{Binding Source={RelativeSource AncestorType={x:Type Expander}}, Path=IsExpanded}"
                                 Value="True">
                        <Setter Property="Source"
                                Value="collapse.png" />
                    </DataTrigger>
                </Image.Triggers>
            </Image>
        </Grid>
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

在此範例中， [`Image`](xref:Xamarin.Forms.Image)預設會`expand`顯示圖示：

![在 iOS 和 Android 上折迭狀態的展開器圖示螢幕擷取畫面](expander-images/icon-expand.png "IOS 和 Android 上的 Expandd 圖示")

當`IsExpanded`您按`true`下`Expander`標頭時，屬性會變成，這`collapse`會導致顯示圖示：

![擴充狀態（在 iOS 和 Android 上）中的展開器圖示螢幕擷取畫面](expander-images/icon-collapse.png "IOS 和 Android 上的 Expandd 圖示")

如需觸發程式的詳細資訊，請參閱[Xamarin. Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

## <a name="define-the-space-between-header-and-content"></a>定義標頭和內容之間的空格

根據預設，中的內容`Expander`會出現在其標頭的正下方。 不過，您可以藉由將`Spacing`屬性設定為代表內容和`double`其標頭之間空白空間的值，來變更此行為：

```xaml
<Expander Spacing="50"
          IsExpanded="true">
    <Expander.Header>
        <Label Text="Baboon"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Grid Padding="10">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>
        <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
               Aspect="AspectFill"
               HeightRequest="120"
               WidthRequest="120" />
        <Label Grid.Column="1"
               Text="Baboons are African and Arabian Old World monkeys belonging to the genus Papio, part of the subfamily Cercopithecinae."
               FontAttributes="Italic" />
    </Grid>
</Expander>
```

在此範例中， `Expander`內容會顯示在其標頭底下50個裝置獨立單位：

![在 iOS 和 Android 上設定間距的展開器螢幕擷取畫面](expander-images/expander-spacing.png "在 iOS 和 Android 上設定了間距的展開器")

## <a name="embed-an-expander-in-an-expander"></a>在展開器中內嵌展開器

的內容`Expander`可以設定為另一個`Expander`控制項，以啟用多個擴充層級。 下列 XAML 會顯示其`Expander`內容為另一個`Expander`物件的：

```xaml
<Expander Spacing="10">
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander Padding="10"
              Spacing="10">
        <Expander.Header>
            <Label Text="{Binding Location}"
                   FontSize="Medium" />
        </Expander.Header>
            <Expander.ContentTemplate>
                <DataTemplate>
                    <Grid>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="120"
                               WidthRequest="120" />
                        <Label Grid.Column="1"
                               Text="{Binding Details}"
                               FontAttributes="Italic" />
                    </Grid>
                </DataTemplate>
            </Expander.ContentTemplate>
    </Expander>
</Expander>
```

在此範例中，點擊根`Expander`標頭會顯示子`Expander`系的標頭：

![在 iOS 和 Android 上內嵌擴充器的螢幕擷取畫面](expander-images/embedded-expander1.png "IOS 和 Android 上的內嵌擴充器")

點擊子`Expander`標題會導致其內容膨脹並顯示：

![在 iOS 和 Android 上內嵌擴充器的螢幕擷取畫面](expander-images/embedded-expander2.png "IOS 和 Android 上的內嵌擴充器")

## <a name="define-the-expand-and-collapse-animation"></a>定義展開和折迭動畫

可以定義`Expander`展開或折迭時所發生的動畫，其方式是`ExpandAnimationEasing`將`CollapseAnimationEasing`和屬性設定為包含在 Xamarin 中的任何緩動函式，或自訂的緩時函數。 根據預設，展開和折迭動畫會出現在250毫秒上。 不過，您可以將`ExpandAnimationLength`和`CollapseAnimationLength`屬性設定為`uint`值來變更這些持續時間。

下列 XAML 顯示的範例會定義當使用者展開或折迭時`Expander`所發生的動畫：

```xaml
<Expander ExpandAnimationEasing="{x:Static Easing.CubicIn}"
          ExpandAnimationLength="500"
          CollapseAnimationEasing="{x:Static Easing.CubicOut}"
          CollapseAnimationLength="500">
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander.ContentTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="120"
                       WidthRequest="120" />
                <Label Grid.Column="1"
                       Text="{Binding Details}"
                       FontAttributes="Italic" />
            </Grid>
        </DataTemplate>
    </Expander.ContentTemplate>
</Expander>
```

在此範例中， `CubicIn`緩動函式會緩慢地加速透過500毫秒的展開`CubicOut`動畫，而緩動函式會透過500毫秒快速減速折迭動畫。

如需緩時函式的詳細資訊，請參閱[Xamarin. 表單緩時函數](~/xamarin-forms/user-interface/animation/easing.md)。

## <a name="resize-an-expander-at-runtime"></a>在執行時間調整擴充器的大小

可以`Expander`使用`ForceUpdateSize`方法，在執行時間以程式設計方式調整。

指定`Expander`名為`expander`的，其內容包含[`Label`](xref:Xamarin.Forms.Label)已附加的`TapGestureRecognizer` ，下列程式碼範例會示範如何呼叫`ForceUpdateSize`方法：

```csharp
void OnLabelTapped(object sender, EventArgs e)
{
    Label label = sender as Label;
    Expander expander = label.Parent.Parent.Parent as Expander;

    if (label.FontSize == Device.GetNamedSize(NamedSize.Default, label))
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Large, label);
    }
    else
    {
        label.FontSize = Device.GetNamedSize(NamedSize.Default, label);
    }
    expander.ForceUpdateSize();
}
```

在此範例中， `FontSize`當按[`Label`](xref:Xamarin.Forms.Label) `Label`下時，會變更。 由於字型的變更大小，因此必須藉`Expander`由呼叫其`ForceUpdateSize`方法來更新的大小。

## <a name="disable-an-expander"></a>停用展開器

應用程式可能會進入擴充不`Expander`是有效作業的狀態。 在這種情況下`Expander` ，可以藉由將其`IsEnabled`屬性設為 false 來停用。 這會讓使用者無法展開或折迭`Expander`。

## <a name="related-links"></a>相關連結

- [展開器示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)
- [Xamarin. 表單緩動函式](~/xamarin-forms/user-interface/animation/easing.md)
- [Xamarin.Forms 觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin. 表單可系結的版面配置](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)

---
title: Xamarin.Forms 膨脹
description: Xamarin.Forms擴充器控制項提供可展開的容器來裝載任何內容。 藉由點擊展開標頭來顯示或隱藏內容。
ms.prod: xamarin
ms.assetid: 381DCB55-522D-4414-B45B-E8DD70AA9985
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0d6cf880be995949553602f9925535e55a8dbb49
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369502"
---
# <a name="no-locxamarinforms-expander"></a>Xamarin.Forms 膨脹

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)

Xamarin.Forms `Expander` 控制項提供可展開的容器來裝載任何內容。 控制項有標頭和內容，並藉由點擊標頭來顯示或隱藏內容 `Expander` 。 只有在 `Expander` 顯示標頭時， `Expander` 才會 *collapsed* 折迭。 當 `Expander` 內容顯示時， `Expander` 就會 *展開* 。

下列螢幕擷取畫面顯示 `Expander` 在其折迭和展開的狀態中，並以紅色方塊指出標頭和內容：

![IOS 和 Android 上折迭和展開狀態的展開器螢幕擷取畫面](expander-images/expander.png "IOS 和 Android 上的擴充器")

> [!IMPORTANT]
> `Expander` 目前為實驗性，而且只能透過設定旗標來使用 `Expander_Experimental` 。 如需詳細資訊，請參閱 [實驗旗標](~/xamarin-forms/internals/experimental-flags.md)。
>
> 此外， `Expander` 也會在命名空間中完整地執行控制項 `Xamarin.Forms` 。 因此，它可在支援的所有平臺上使用 Xamarin.Forms 。

`Expander`控制項會定義下列屬性：

- `CollapseAnimationEasing`型別 [`Easing`](xref:Xamarin.Forms.Easing) ，代表要在折迭時套用至內容的緩 `Expander` 時函數。
- `CollapseAnimationLength`，類型為 `uint` ，其會在折迭時定義動畫的持續時間 `Expander` 。 這個屬性的預設值為250毫秒。
- `Command`，類型為 `ICommand` ，其會在按下 `Expander` 標頭時執行。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。
- `Content`，類型為 [`View`](xref:Xamarin.Forms.View) ，定義展開時要顯示的內容 `Expander` 。
- `ContentTemplate`，屬於型別 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，也就是用來動態擴充內容的範本 `Expander` 。
- `ExpandAnimationEasing`型別為的 [`Easing`](xref:Xamarin.Forms.Easing) ，代表要在展開期間套用至內容的緩 `Expander` 時函數。
- `ExpandAnimationLength`，類型為 `uint` ，可在展開時定義動畫的持續時間 `Expander` 。 這個屬性的預設值為250毫秒。
- `ForceUpdateSizeCommand`，類型為 `ICommand` ，定義強制更新的大小時所執行的命令 `Expander` 。 這個屬性會使用系結 `OneWayToSource` 模式。
- `Header`，類型為 [`View`](xref:Xamarin.Forms.View) ，定義標頭內容。
- `IsExpanded`，類型為 `bool` ，可判斷 `Expander` 是否已展開。 這個屬性會使用系結 `TwoWay` 模式，且具有的預設值 `false` 。
- `State`型別為的， `ExpanderState` 代表的狀態 `Expander` 。 這個屬性會使用系結 `OneWayToSource` 模式。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

> [!NOTE]
> `Content`屬性是類別的 content 屬性 `Expander` ，因此不需要從 XAML 明確設定。

`ExpanderState` 列舉會定義下列成員：

- `Expanding` 指出 `Expander` 正在展開。
- `Expanded` 表示 `Expander` 已展開。
- `Collapsing` 表示為折迭 `Expander` 。
- `Collapsed` 表示已折迭 `Expander` 。

`Expander`控制項也會定義在 `Tapped` 使用標頭時所引發的事件 `Expander` 。 此外，還 `Expander` 包含 `ForceUpdateSize` 可呼叫的方法，以程式設計方式 `Expander` 在執行時間調整其大小。

## <a name="create-an-expander"></a>建立擴充器

下列範例顯示如何 `Expander` 在 XAML 中具現化：

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

在此範例中， `Expander` 預設會折迭，並顯示 [`Label`](xref:Xamarin.Forms.Label) 作為其標頭。 在標頭中 `Expander` ，會導致展開以顯示其內容，也就是 [`Grid`](xref:Xamarin.Forms.Grid) 包含子控制項。 展開時 `Expander` ，將其標頭折迭 `Expander` 。

> [!IMPORTANT]
> 當您 `Expander.Content` 以隱含或明確的方式設定屬性時，即使已折迭，也 `Expander` 會在包含它的頁面被流覽時建立內容 `Expander` 。 不過，您 `Expander.ContentTemplate` 可以將屬性設定為只在 `Expander` 第一次展開時才擴大的內容。 如需詳細資訊，請參閱依 [需求建立擴充器內容](#create-expander-content-on-demand)。

或者，您 `Expander` 可以在程式碼中建立：

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

`Expander` 您可以視需要建立內容，以回應 `Expander` 擴充。 將 `Expander.ContentTemplate` 屬性設定為包含內容的，即可完成這項作業 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

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

在此範例中， `Expander` 只會在 `Expander` 第一次展開時放大內容。

這種方法的優點是，當頁面包含多個 `Expander` 物件時，的內容 `Expander` 只會在使用者第一次展開時建立。

## <a name="add-an-expansion-indicator"></a>新增展開指標

[`Image`](xref:Xamarin.Forms.Image)可以新增至 `Expander` 標頭，以提供擴充狀態的視覺指示。 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)可以附加至 `Image` ，以 `Source` 根據屬性的值變更屬性 `Expander.IsExpanded` ：

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

在此範例中， [`Image`](xref:Xamarin.Forms.Image) 預設會顯示 `expand` 圖示：

![螢幕擷取畫面： iOS 和 Android 上折迭狀態的展開器圖示](expander-images/icon-expand.png "IOS 和 Android 上的 Expandd 圖示")

`IsExpanded`當您按 `true` `Expander` 下標頭時，屬性會變成，這會導致 `collapse` 顯示圖示：

![螢幕擷取畫面：在 iOS 和 Android 上展開狀態的展開器圖示](expander-images/icon-collapse.png "IOS 和 Android 上的 Expandd 圖示")

如需觸發程式的詳細資訊，請參閱[ Xamarin.Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

## <a name="embed-an-expander-in-an-expander"></a>在展開器中內嵌擴充器

的內容 `Expander` 可以設定為另一個 `Expander` 控制項，以啟用多個層級的擴充。 下列 XAML 顯示 `Expander` 其內容為另一個 `Expander` 物件的：

```xaml
<Expander>
    <Expander.Header>
        <Label Text="{Binding Name}"
               FontAttributes="Bold"
               FontSize="Medium" />
    </Expander.Header>
    <Expander Padding="10">
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

在此範例中，點擊根 `Expander` 標題會顯示子系的標頭 `Expander` ：

![IOS 和 Android 上內嵌展開工具的螢幕擷取畫面](expander-images/embedded-expander1.png "IOS 和 Android 上的內嵌擴充器")

點擊子 `Expander` 標頭會導致其內容被放大並顯示：

![IOS 和 Android 上內嵌展開工具的螢幕擷取畫面](expander-images/embedded-expander2.png "IOS 和 Android 上的內嵌擴充器")

## <a name="define-the-expand-and-collapse-animation"></a>定義展開和折迭動畫

可以定義展開或折迭時所發生的動畫 `Expander` ，方法是將 `ExpandAnimationEasing` 和 `CollapseAnimationEasing` 屬性設定為中包含的任何簡化函式 Xamarin.Forms ，或自訂的緩縮函數。 展開和折迭動畫預設會透過250毫秒進行。 不過，您可以將 `ExpandAnimationLength` 和屬性設定為值，藉以變更這些持續時間 `CollapseAnimationLength` `uint` 。

下列 XAML 顯示定義當使用者展開或折迭時所發生之動畫的範例 `Expander` ：

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

在此範例中，緩和函式會以 `CubicIn` 緩慢的速度加速展開動畫的500毫秒，而緩時變 `CubicOut` 函數會快速減速折迭動畫的500毫秒。

如需簡化函式的詳細資訊，請參閱[ Xamarin.Forms 簡化](~/xamarin-forms/user-interface/animation/easing.md)函式。

## <a name="resize-an-expander-at-runtime"></a>在執行時間調整擴充器的大小

您 `Expander` 可以使用方法，以程式設計方式在執行時間調整大小 `ForceUpdateSize` 。

假設有一個 `Expander` 名為的 `expander` ，其內容包含 [`Label`](xref:Xamarin.Forms.Label) 已附加的，則下列程式碼範例會示範如何 `TapGestureRecognizer` 呼叫 `ForceUpdateSize` 方法：

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

在此範例中， `FontSize` [`Label`](xref:Xamarin.Forms.Label) 當按下時會變更的 `Label` 。 由於字型變更大小的緣故，因此必須藉 `Expander` 由呼叫其方法來更新的大小 `ForceUpdateSize` 。

## <a name="disable-an-expander"></a>停用展開器

應用程式可能會進入擴充不是有效作業的狀態 `Expander` 。 在這種情況下，您 `Expander` 可以將其 `IsEnabled` 屬性設定為 false，以停用。 這可防止使用者展開或折迭 `Expander` 。

## <a name="related-links"></a>相關連結

- [ (範例) 的擴充器示範 ](/samples/xamarin/xamarin-forms-samples/userinterface-expanderdemos/)
- [Xamarin.Forms 簡化函式](~/xamarin-forms/user-interface/animation/easing.md)
- [Xamarin.Forms 觸發器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 可系結版面配置](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
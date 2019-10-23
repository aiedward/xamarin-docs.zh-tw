---
title: Xamarin. 表單相對系結
description: 本文說明如何使用 RelativeSource 標記延伸，將系結來源設定為相對於系結目標的位置，以建立相對系結。
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
ms.openlocfilehash: 24879b1ffcac97acdba27c32a22e43bfb6e80459
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749779"
---
# <a name="xamarinforms-relative-bindings"></a>Xamarin. 表單相對系結

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

相對系結提供設定系結來源相對於系結目標位置的功能。 它們是使用 `RelativeSource` 標記延伸來建立，並設定為系結運算式的 `Source` 屬性。

@No__t_1 類別支援 `RelativeSource` 標記延伸，其定義下列屬性：

- `RelativeBindingSourceMode` 類型的 `Mode`，會描述系結來源相對於系結目標位置的位置。
- `AncestorLevel`，屬於 `int` 類型，這是在 `FindAncestor` `Mode` 屬性時要尋找的選擇性上階層級。
- `AncestorType`，屬於 `Type` 的類型，這是 `Mode` 屬性 `FindAncestor` 時所要尋找之上階的類型。

> [!NOTE]
> XAML 剖析器允許 `RelativeSourceExtension` 類別縮寫為 `RelativeSource`。

@No__t_0 屬性應該設定為其中一個 `RelativeBindingSourceMode` 列舉成員：

- `TemplatedParent` 表示套用繫結項目的範本所在的專案。 如需詳細資訊，請參閱系結[至樣板化父系](#bind-to-a-templated-parent)。
- `Self` 表示正在設定系結的專案，可讓您將該專案的一個屬性系結至相同元素上的另一個屬性。 如需詳細資訊，請參閱系結[至 self](#bind-to-self)。
- `FindAncestor` 表示繫結項目之視覺化樹狀結構中的上階。 此模式應該用來系結至 `AncestorType` 屬性所表示的上階控制項。 如需詳細資訊，請參閱[系結至](#bind-to-an-ancestor)上階。
- `FindAncestorBindingContext` 指出繫結項目之視覺化樹狀結構中上階的 `BindingContext`。 此模式應該用來系結至由 `AncestorType` 屬性所表示之上階的 `BindingContext`。 如需詳細資訊，請參閱[系結至](#bind-to-an-ancestor)上階。

@No__t_0 屬性是 `RelativeSourceExtension` 類別的 content 屬性。 因此，對於以大括弧表示的 XAML 標記運算式，您可以消除運算式的 `Mode=` 部分。

如需有關 Xamarin 標記延伸的詳細資訊，請參閱[XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。

## <a name="bind-to-self"></a>系結至自我

@No__t_0 的相對系結模式是用來將專案的屬性系結至相同專案上的另一個屬性：

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

在此範例中， [`BoxView`](xref:Xamarin.Forms.BoxView)會將其 `WidthRequest` 屬性設為固定大小，而 `HeightRequest` 屬性會系結至 `WidthRequest` 屬性。 因此，這兩個屬性相等，因此會繪製正方形：

[![IOS 和 Android 上的自我模式相對系結螢幕擷取畫面](relative-bindings-images/self-relative-binding.png "自我相對系結模式")](relative-bindings-images/self-relative-binding-large.png#lightbox "自我相對系結模式")

> [!IMPORTANT]
> 將元素的屬性系結至相同專案上的另一個屬性時，屬性必須是相同的類型。 或者，您可以在系結上指定轉換器來轉換值。

這個系結模式的常見用法是將物件的 `BindingContext` 設定為本身的屬性。 下列程式碼顯示這種情況的範例：

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

在此範例中，頁面的 `BindingContext` 會設定為本身的 `DefaultViewModel` 屬性。 這個屬性定義于頁面的程式碼後置檔案中，並提供 viewmodel 實例。 [@No__t_1](xref:Xamarin.Forms.ListView)系結至 viewmodel 的 `Employees` 屬性。

## <a name="bind-to-an-ancestor"></a>系結至上階

@No__t_0 和 `FindAncestorBindingContext` 相對系結模式是用來系結至視覺化樹狀結構中特定類型的父元素。 @No__t_0 模式是用來系結至從[`Element`](xref:Xamarin.Forms.Element)類型衍生的父元素。 @No__t_0 模式是用來系結至父元素的 `BindingContext`。

> [!WARNING]
> 使用 `FindAncestor` 和 `FindAncestorBindingContext` 相對系結模式時，`AncestorType` 屬性必須設定為 `Type`，否則會擲回 `XamlParseException`。

如果未明確設定 `Mode` 屬性，將 `AncestorType` 屬性設定為衍生自[`Element`](xref:Xamarin.Forms.Element)的類型，會將 `Mode` 屬性隱含地設定為 [`FindAncestor`]。 同樣地，將 `AncestorType` 屬性設定為不是衍生自 `Element` 的類型，會將 `Mode` 屬性隱含地設定為 `FindAncestorBindingContext`。

下列 XAML 會顯示一個範例，其中 `Mode` 屬性會隱含地設定為 `FindAncestorBindingContext`：

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Label Text="{Binding Fullname}"
                                   VerticalOptions="Center" />
                            <Button Text="Delete"
                                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeleteEmployeeCommand}"
                                    CommandParameter="{Binding}"
                                    HorizontalOptions="EndAndExpand" />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

在此範例中，頁面的 `BindingContext` 會設定為本身的 `DefaultViewModel` 屬性。 這個屬性定義于頁面的程式碼後置檔案中，並提供 viewmodel 實例。 [@No__t_1](xref:Xamarin.Forms.ListView)系結至 viewmodel 的 `Employees` 屬性。 [@No__t_1](xref:Xamarin.Forms.DataTemplate)，定義 `ListView` 中每個專案的外觀，其中包含[`Button`](xref:Xamarin.Forms.Button)。 按鈕的 `Command` 屬性會系結至其父系 viewmodel 中的 `DeleteEmployeeCommand`。 點擊 `Button` 會刪除員工：

[![FindAncestor 模式相對系結（在 iOS 和 Android 上）的螢幕擷取畫面](relative-bindings-images/findancestor-relative-binding.png "FindAncestor 相對系結模式")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "FindAncestor 相對系結模式")

此外，選擇性的 `AncestorLevel` 屬性可以在視覺樹狀結構中可能有多個該類型的上階時，協助區分上階查閱：

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

在此範例中，`Label.Text` 屬性會系結至在向上路徑上遇到之第二個[`Entry`](xref:Xamarin.Forms.Entry)的 `Text` 屬性，從系結的目標元素開始。

> [!NOTE]
> @No__t_0 屬性應該設定為1，以尋找最接近系結目標元素的上階。

## <a name="bind-to-a-templated-parent"></a>系結至樣板化父系

@No__t_0 的相對系結模式是用來將控制項範本內的系結至套用範本的執行時間物件實例（稱為樣板化父系）。 只有當相對系結在控制項範本內，而且類似于設定 `TemplateBinding` 時，才適用此模式。

下列 XAML 會顯示 `TemplatedParent` 相對系結模式的範例：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                   BackgroundColor="{Binding CardColor}"
                   BorderColor="{Binding BorderColor}"
                   ...>
                <Grid>
                    ...
                    <Label Text="{Binding CardTitle}"
                           ... />
                    <BoxView BackgroundColor="{Binding BorderColor}"
                             ... />
                    <Label Text="{Binding CardDescription}"
                           ... />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout>        
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

在此範例中， [`Frame`](xref:Xamarin.Forms.Frame)（也就是 `ControlTemplate` 的根項目）會將其 `BindingContext` 設定為套用範本的執行時間物件實例。 因此，`Frame` 及其子系會針對每個 `CardView` 物件的屬性，解析其系結運算式：

[![TemplatedParent 模式相對系結（在 iOS 和 Android 上）的螢幕擷取畫面](relative-bindings-images/templatedparent-relative-binding.png "TemplatedParent 相對系結模式")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "TemplatedParent 相對系結模式")

如需控制項範本的詳細資訊，請參閱[Xamarin 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin. Forms 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)

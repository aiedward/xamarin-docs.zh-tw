---
title: Xamarin.Forms相對系結
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8d4e2696e6027f07b7b8e638cd1e0f1d65a5503d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139707"
---
# <a name="xamarinforms-relative-bindings"></a>Xamarin.Forms相對系結

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

相對系結提供設定系結來源相對於系結目標位置的功能。 系統會使用標記延伸來建立它們 `RelativeSource` ，並將設定為系結 `Source` 運算式的屬性。

`RelativeSource`類別支援標記延伸 `RelativeSourceExtension` ，其定義下列屬性：

- `Mode`，屬於類型 `RelativeBindingSourceMode` ，描述系結來源相對於系結目標位置的位置。
- `AncestorLevel`，屬於類型 `int` ，這是當屬性為時，要尋找的選擇性祖系層級 `Mode` `FindAncestor` 。 `AncestorLevel`的，會 `n` 略過的 `n-1` 實例 `AncestorType` 。
- `AncestorType`，屬於類型 `Type` ，當屬性為時，要尋找的上階類型 `Mode` `FindAncestor` 。

> [!NOTE]
> XAML 剖析器允許將 `RelativeSourceExtension` 類別縮寫為 `RelativeSource` 。

`Mode`屬性應設定為其中一個 `RelativeBindingSourceMode` 列舉成員：

- `TemplatedParent`表示套用繫結項目的範本所屬的元素。 如需詳細資訊，請參閱系結[至樣板化父系](#bind-to-a-templated-parent)。
- `Self`表示要設定系結的專案，可讓您將該專案的一個屬性系結至相同元素上的另一個屬性。 如需詳細資訊，請參閱系結[至本身](#bind-to-self)。
- `FindAncestor`表示繫結項目之視覺化樹狀結構中的上階。 此模式應該用來系結至屬性所表示的上階控制項 `AncestorType` 。 如需詳細資訊，請參閱[系結至](#bind-to-an-ancestor)上階。
- `FindAncestorBindingContext`表示在 `BindingContext` 繫結項目之視覺化樹狀結構中上階的。 此模式應該用來系結至 `BindingContext` 屬性所表示之上階的 `AncestorType` 。 如需詳細資訊，請參閱[系結至](#bind-to-an-ancestor)上階。

`Mode`屬性是類別的 content 屬性 `RelativeSourceExtension` 。 因此，對於以大括弧表示的 XAML 標記運算式，您可以消除 `Mode=` 運算式的部分。

如需標記延伸的詳細資訊 Xamarin.Forms ，請參閱[XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。

## <a name="bind-to-self"></a>系結至自我

相對系結 `Self` 模式是用來將元素的屬性系結至相同專案上的另一個屬性：

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

在此範例中，會 [`BoxView`](xref:Xamarin.Forms.BoxView) 將其 `WidthRequest` 屬性設定為固定大小，而屬性會系結 `HeightRequest` 至 `WidthRequest` 屬性。 因此，這兩個屬性相等，因此會繪製正方形：

[![IOS 和 Android 上的自我模式相對系結螢幕擷取畫面](relative-bindings-images/self-relative-binding.png "自我相對系結模式")](relative-bindings-images/self-relative-binding-large.png#lightbox "自我相對系結模式")

> [!IMPORTANT]
> 將元素的屬性系結至相同專案上的另一個屬性時，屬性必須是相同的類型。 或者，您可以在系結上指定轉換器來轉換值。

這個系結模式的常見用法是將物件本身設定 `BindingContext` 為屬性。 下列程式碼顯示這種情況的範例：

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

在此範例中， `BindingContext` 頁面的會設定為本身的 `DefaultViewModel` 屬性。 這個屬性定義于頁面的程式碼後置檔案中，並提供 viewmodel 實例。 會系結 [`ListView`](xref:Xamarin.Forms.ListView) 至 `Employees` viewmodel 的屬性。

## <a name="bind-to-an-ancestor"></a>系結至上階

`FindAncestor`和相對系結 `FindAncestorBindingContext` 模式是用來系結至視覺化樹狀結構中特定類型的父元素。 `FindAncestor`模式是用來系結至從類型衍生的父元素 [`Element`](xref:Xamarin.Forms.Element) 。 `FindAncestorBindingContext`模式是用來系結至 `BindingContext` 父元素的。

> [!WARNING]
> `AncestorType`使用和相對系結模式時，必須將屬性設定為 `Type` `FindAncestor` `FindAncestorBindingContext` ，否則 `XamlParseException` 會擲回。

如果 `Mode` 未明確設定屬性， `AncestorType` 將屬性設定為衍生自的類型， [`Element`](xref:Xamarin.Forms.Element) 會將屬性隱含地設定 `Mode` 為 `FindAncestor` 。 同樣地， `AncestorType` 將屬性設定為不是衍生自的類型， `Element` 會將屬性隱含地設定 `Mode` 為 `FindAncestorBindingContext` 。

> [!NOTE]
> `FindAncestorBindingContext`當任何祖系的發生變更時，會重新套用使用模式的相對系結 `BindingContext` 。

下列 XAML 會顯示一個範例，其中的 `Mode` 屬性會隱含地設定為 `FindAncestorBindingContext` ：

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

在此範例中， `BindingContext` 頁面的會設定為本身的 `DefaultViewModel` 屬性。 這個屬性定義于頁面的程式碼後置檔案中，並提供 viewmodel 實例。 會系結 [`ListView`](xref:Xamarin.Forms.ListView) 至 `Employees` viewmodel 的屬性。 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，定義中每個專案的外觀 `ListView` ，其中包含 [`Button`](xref:Xamarin.Forms.Button) 。 按鈕的 `Command` 屬性會系結至 `DeleteEmployeeCommand` 其父系 viewmodel 中的。 點擊會 `Button` 刪除員工：

[![FindAncestor 模式相對系結（在 iOS 和 Android 上）的螢幕擷取畫面](relative-bindings-images/findancestor-relative-binding.png "FindAncestor 相對系結模式")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "FindAncestor 相對系結模式")

此外，在 `AncestorLevel` 視覺化樹狀結構中可能有多個該類型的上階時，選擇性屬性可以協助讓祖系查閱具有歧義：

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

在此範例中， `Label.Text` 屬性會系結至在 `Text` 向上路徑上遇到的第二個屬性 [`Entry`](xref:Xamarin.Forms.Entry) （從系結的目標專案開始）。

> [!NOTE]
> `AncestorLevel`屬性應設定為1，以尋找最接近系結目標元素的上階。

## <a name="bind-to-a-templated-parent"></a>系結至樣板化父系

相對系結 `TemplatedParent` 模式是用來將控制項範本內的系結至套用範本的執行時間物件實例（稱為樣板化父系）。 只有當相對系結在控制項範本內，而且類似于設定時，才適用此模式 `TemplateBinding` 。

下列 XAML 顯示相對系結模式的範例 `TemplatedParent` ：

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

在此範例中， [`Frame`](xref:Xamarin.Forms.Frame) （是的根項目）會 `ControlTemplate` `BindingContext` 將其設定為套用範本的執行時間物件實例。 因此，及其 `Frame` 子系會針對每個物件的屬性，解析其系結運算式 `CardView` ：

[![TemplatedParent 模式相對系結（在 iOS 和 Android 上）的螢幕擷取畫面](relative-bindings-images/templatedparent-relative-binding.png "TemplatedParent 相對系結模式")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "TemplatedParent 相對系結模式")

如需控制項範本的詳細資訊，請參閱[ Xamarin.Forms 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-template.md)。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms控制項範本](~/xamarin-forms/app-fundamentals/templates/control-template.md)

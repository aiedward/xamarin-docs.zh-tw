---
title: Xamarin.Forms 相對系結
description: 本文說明如何使用 RelativeSource 標記延伸來設定相對於系結目標位置的系結來源，以建立相對系結。
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 24fa816519b05b46c38015fa602a4e70fbc07d16
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560386"
---
# <a name="no-locxamarinforms-relative-bindings"></a>Xamarin.Forms 相對系結

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

相對系結可讓您設定相對於系結目標位置的系結來源。 它們是使用 `RelativeSource` 標記延伸來建立，並設定為系結 `Source` 運算式的屬性。

`RelativeSource`類別支援標記延伸 `RelativeSourceExtension` ，其定義下列屬性：

- `Mode`型別為的，描述系結 `RelativeBindingSourceMode` 來源相對於系結目標位置的位置。
- `AncestorLevel`，類型為 `int` 要尋找的選擇性上階層級（當 `Mode` 屬性為時） `FindAncestor` 。 `AncestorLevel` `n` 略過 `n-1` 實例的 `AncestorType` 。
- `AncestorType`，類型為， `Type` 當屬性為時，要尋找的上階型別 `Mode` `FindAncestor` 。

> [!NOTE]
> XAML 剖析器允許將 `RelativeSourceExtension` 類別縮寫為 `RelativeSource` 。

`Mode`屬性應設定為其中一個 `RelativeBindingSourceMode` 列舉成員：

- `TemplatedParent` 指出套用了繫結項目之範本的元素。 如需詳細資訊，請參閱系結 [至樣板化父代](#bind-to-a-templated-parent)。
- `Self` 指出正在設定系結的專案，可讓您將該專案的一個屬性系結至相同元素上的另一個屬性。 如需詳細資訊，請參閱系結 [至本身](#bind-to-self)。
- `FindAncestor` 指出繫結項目的視覺化樹狀結構中的上階。 這個模式應該用來系結到屬性所代表的上階控制項 `AncestorType` 。 如需詳細資訊，請參閱 [系結至](#bind-to-an-ancestor)上階。
- `FindAncestorBindingContext` 表示 `BindingContext` 繫結項目之視覺化樹狀結構中的上階。 這個模式應該用來系結至 `BindingContext` 屬性所代表之上階的 `AncestorType` 。 如需詳細資訊，請參閱 [系結至](#bind-to-an-ancestor)上階。

`Mode`屬性是類別的 content 屬性 `RelativeSourceExtension` 。 因此，以大括弧表示的 XAML 標記運算式，您可以消除 `Mode=` 運算式的部分。

如需標記延伸的詳細資訊 Xamarin.Forms ，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。

## <a name="bind-to-self"></a>系結至 self

相對系結 `Self` 模式是用來將專案的屬性系結至相同元素上的另一個屬性：

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

在此範例中，會將 [`BoxView`](xref:Xamarin.Forms.BoxView) 其 `WidthRequest` 屬性設定為固定大小，且屬性會系結 `HeightRequest` 至 `WidthRequest` 屬性。 因此，兩個屬性都相等，所以會繪製一個正方形：

[![螢幕擷取畫面： iOS 和 Android 上的獨立模式相對系結](relative-bindings-images/self-relative-binding.png "自我相對系結模式")](relative-bindings-images/self-relative-binding-large.png#lightbox "自我相對系結模式")

> [!IMPORTANT]
> 將專案的屬性系結至相同元素上的另一個屬性時，屬性必須是相同的類型。 或者，您也可以在系結上指定轉換器來轉換值。

這個系結模式的常見用法是將物件本身設定 `BindingContext` 為屬性。 下列程式碼顯示此範例：

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

在此範例中， `BindingContext` 頁面的會設定為本身的 `DefaultViewModel` 屬性。 這個屬性是在頁面的程式碼後端檔案中定義，並提供 viewmodel 實例。 系結 [`ListView`](xref:Xamarin.Forms.ListView) 至 `Employees` viewmodel 的屬性。

## <a name="bind-to-an-ancestor"></a>系結至上階

`FindAncestor`和相對系結 `FindAncestorBindingContext` 模式可用來系結至視覺化樹狀結構中特定類型的父元素。 此 `FindAncestor` 模式會用來系結至衍生自型別的父元素 [`Element`](xref:Xamarin.Forms.Element) 。 此 `FindAncestorBindingContext` 模式會用來系結至 `BindingContext` 父元素的。

> [!WARNING]
> `AncestorType` `Type` 使用和相對系結模式時，屬性必須設定為 `FindAncestor` `FindAncestorBindingContext` ，否則 `XamlParseException` 會擲回。

如果 `Mode` 未明確設定屬性，將屬性（ `AncestorType` property）設定為衍生自的型別， [`Element`](xref:Xamarin.Forms.Element) 就會隱含地將屬性（property）設定 `Mode` 為 `FindAncestor` 。 同樣地， `AncestorType` 將屬性（property）設定為不是衍生自的型別， `Element` 會將屬性（property）隱含地設定 `Mode` 為 `FindAncestorBindingContext` 。

> [!NOTE]
> `FindAncestorBindingContext`當任何祖系的變更時，將會重新套用使用模式的相對系結 `BindingContext` 。

下列 XAML 顯示的範例會將 `Mode` 屬性隱含地設定為 `FindAncestorBindingContext` ：

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

在此範例中， `BindingContext` 頁面的會設定為本身的 `DefaultViewModel` 屬性。 這個屬性是在頁面的程式碼後端檔案中定義，並提供 viewmodel 實例。 系結 [`ListView`](xref:Xamarin.Forms.ListView) 至 `Employees` viewmodel 的屬性。 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，定義中每個專案的外觀 `ListView` ，包含 [`Button`](xref:Xamarin.Forms.Button) 。 按鈕的 `Command` 屬性會系結至 `DeleteEmployeeCommand` 其父系 viewmodel 中的。 將 `Button` 員工刪除：

[![螢幕擷取畫面： iOS 和 Android 上的 FindAncestor 模式相對系結](relative-bindings-images/findancestor-relative-binding.png "FindAncestor 相對系結模式")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "FindAncestor 相對系結模式")

此外，在 `AncestorLevel` 視覺化樹狀結構中可能有多個類型上階的案例中，選擇性屬性有助於區分上階查閱：

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

在此範例中， `Label.Text` 屬性會系結到在 `Text` 向上路徑上遇到的第二個路徑的屬性，從系結的 [`Entry`](xref:Xamarin.Forms.Entry) 目標元素開始。

> [!NOTE]
> `AncestorLevel`屬性應設定為1，以找出最接近系結目標專案的上階。

## <a name="bind-to-a-templated-parent"></a>系結至樣板化父系

相對系結 `TemplatedParent` 模式可用來從控制項範本內系結至套用範本的執行時間物件實例 (稱為樣板化父) 。 只有當相對系結在控制項範本中，而且類似于設定時，才適用此模式 `TemplateBinding` 。

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

在此範例中，（ [`Frame`](xref:Xamarin.Forms.Frame) 是的根項目）會 `ControlTemplate` 將其 `BindingContext` 設定為套用範本的執行時間物件實例。 因此， `Frame` 和其子系會針對每個物件的屬性來解析其系結運算式 `CardView` ：

[![螢幕擷取畫面： iOS 和 Android 上的 TemplatedParent 模式相對系結](relative-bindings-images/templatedparent-relative-binding.png "TemplatedParent 相對系結模式")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "TemplatedParent 相對系結模式")

如需控制項範本的詳細資訊，請參閱[ Xamarin.Forms 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-template.md)。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-template.md)
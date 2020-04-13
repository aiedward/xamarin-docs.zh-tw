---
title: Xamarin.Forms 相對繫結
description: 本文介紹如何使用相對源標記擴展來設置相對於綁定目標位置的綁定源,從而創建相對綁定。
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 63ce27fc871da12eabb1baad568af167c860926f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75955817"
---
# <a name="xamarinforms-relative-bindings"></a>Xamarin.Forms 相對繫結

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

相對綁定提供了相對於綁定目標的位置設置綁定源的能力。 它們使用`RelativeSource`標記擴展創建,並設置為綁定表達`Source`式 的屬性。

類別`RelativeSource`支援標記延伸,`RelativeSourceExtension`這個類別定義以下屬性:

- `Mode`的類型`RelativeBindingSourceMode`, 描述繫結源相對於繫結目標的位置的位置。
- `AncestorLevel`類型`int`,當屬性`Mode``FindAncestor`為 時要查找的可選祖先級別。 `AncestorLevel`的`n`跳`n-1`過 實`AncestorType`例。
- `AncestorType`類型`Type`,`Mode`當`FindAncestor`屬性為 時要查找的祖先的類型。

> [!NOTE]
> XAML 解析器允許`RelativeSourceExtension`將 類別縮`RelativeSource`寫為 。

該`Mode`屬性應設定為`RelativeBindingSourceMode`枚 舉成員之一:

- `TemplatedParent`指示應用綁定元素的範本的元素。 有關詳細資訊,請參閱[綁定到範本化父級](#bind-to-a-templated-parent)。
- `Self`指示要設置綁定的元素,允許您將該元素的一個屬性綁定到同一元素上的另一個屬性。 有關詳細資訊,請參閱[綁定到自我](#bind-to-self)。
- `FindAncestor`指示綁定元素的可視樹中的祖先。 此模式應用於綁定到屬性`AncestorType`表示的祖先控件。 有關詳細資訊,請參閱[綁定到祖先](#bind-to-an-ancestor)。
- `FindAncestorBindingContext``BindingContext`指示綁定元素的可視樹中祖先的。 此模式應用於綁定到`BindingContext``AncestorType`屬性表示的祖先。 有關詳細資訊,請參閱[綁定到祖先](#bind-to-an-ancestor)。

屬性`Mode``RelativeSourceExtension`是類的內容屬性。 因此,對於用大括弧表示的 XAML 標記表達式,`Mode=`可以消除 表達式的部分。

有關 Xamarin.表單標記延伸的詳細資訊,請參閱[XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。

## <a name="bind-to-self"></a>繫結到自我

相對`Self`繫結模式用於將元素的屬性繫結到同一元素上的另一個屬性:

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

[`BoxView`](xref:Xamarin.Forms.BoxView)在此範例中,將`WidthRequest`屬性設定為固定大小,`HeightRequest`並且該屬性綁定到`WidthRequest`該 屬性。 因此,兩個屬性相等,因此繪製一個正方形:

[![在 iOS 和 Android 上的自我模式相對綁定的螢幕截圖](relative-bindings-images/self-relative-binding.png "自相對繫結模式")](relative-bindings-images/self-relative-binding-large.png#lightbox "自相對繫結模式")

> [!IMPORTANT]
> 將元素的屬性綁定到同一元素上的另一個屬性時,該屬性必須為同一類型。 或者,您可以在綁定上指定轉換器以轉換該值。

此綁定模式的常見用途是將物件的`BindingContext`設置為其自身的屬性。 以下代碼顯示以下範例:

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

在此範例中,`BindingContext`頁面的`DefaultViewModel`設定為其屬性。 此屬性在頁面的代碼後面檔中定義,並提供檢視模型實例。 綁定[`ListView`](xref:Xamarin.Forms.ListView)`Employees`到 視圖模型的屬性。

## <a name="bind-to-an-ancestor"></a>繫結到祖先

和`FindAncestor``FindAncestorBindingContext`相對綁定模式用於綁定到可視化樹中特定類型的父元素。 該`FindAncestor`模式用於綁定到父元素,該元素派生自[`Element`](xref:Xamarin.Forms.Element)類型。 該`FindAncestorBindingContext`模式用於綁定到父元素`BindingContext`的。

> [!WARNING]
> `AncestorType``Type`使用`FindAncestor``FindAncestorBindingContext`與相對結合模式時,該屬性必須設定為 a,`XamlParseException`否則會引發 。

如果未顯示式`Mode`設定該屬性,`AncestorType`則將屬性設定為派生[`Element`](xref:Xamarin.Forms.Element)自的類型將隱式`Mode`設定為`FindAncestor`。 `AncestorType`同樣,將屬性設定為不派生`Element`的類型將隱式將屬性`Mode`設定`FindAncestorBindingContext`為 。

> [!NOTE]
> 當任何祖先`BindingContext`更改時`FindAncestorBindingContext`, 將重新應用使用該模式的相對綁定。

下面的 XAML 顯示了一個`Mode`範例 ,其中屬性`FindAncestorBindingContext`將隱式 設定為 :

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

在此範例中,`BindingContext`頁面的`DefaultViewModel`設定為其屬性。 此屬性在頁面的代碼後面檔中定義,並提供檢視模型實例。 綁定[`ListView`](xref:Xamarin.Forms.ListView)`Employees`到 視圖模型的屬性。 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)定義`ListView`每個項目的外觀包含[`Button`](xref:Xamarin.Forms.Button)。 按鍵的屬性`Command`繫結`DeleteEmployeeCommand`為 其父檢視模型中 。 點擊刪除`Button`員工:

[![在 iOS 和 Android 上尋找祖先模式相對綁定的螢幕截圖](relative-bindings-images/findancestor-relative-binding.png "尋找祖先相對繫結模式")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "尋找祖先相對繫結模式")

此外,可選`AncestorLevel`屬性可以説明在可視化樹中可能存在該類型的祖先的方案中消除歧義的祖先查找:

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

在此範例中,`Label.Text`屬性結合到向上路徑上遇到的第`Text`二[`Entry`](xref:Xamarin.Forms.Entry)個屬性,從綁定的目標元素開始。

> [!NOTE]
> 屬性`AncestorLevel`應設置為 1 以查找最接近綁定目標元素的祖先。

## <a name="bind-to-a-templated-parent"></a>繫結為樣本化父級

相對`TemplatedParent`綁定模式用於從控制項樣本中綁定到應用範本的運行時物件實例(稱為範本化父實例)。 僅當相對綁定位於控件範本中且類似於設置`TemplateBinding`時 ,此模式才適用。

以下 XAML`TemplatedParent`顯示了 相對綁定模式的範例:

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

在此範例中,作為的[`Frame`](xref:Xamarin.Forms.Frame)根元素的`ControlTemplate`的的將`BindingContext`將其設置為應用範本的運行時物件實例。 因此,`Frame`及其子級針對`CardView`每個 物件的屬性解析其綁定表示式:

[![iOS 和 Android 上的樣本父模式相對綁定的螢幕截圖](relative-bindings-images/templatedparent-relative-binding.png "樣本化父級相對繫結模式")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "樣本化父級相對繫結模式")

有關控制項樣本的詳細資訊,請參閱[Xamarin.窗體控制樣本](~/xamarin-forms/app-fundamentals/templates/control-template.md)。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.表單控制樣本](~/xamarin-forms/app-fundamentals/templates/control-template.md)

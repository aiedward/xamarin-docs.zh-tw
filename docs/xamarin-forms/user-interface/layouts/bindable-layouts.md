---
title: 在 Xamarin.Forms 中的可繫結配置
description: 可繫結的配置可讓版面配置的類別，以產生其內容繫結至項目的集合，可設定的每個項目使用 DataTemplate 外觀的選項。
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 28846e6e9590d2adf56114fce8bc6056c0112ac1
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970973"
---
# <a name="bindable-layouts-in-xamarinforms"></a>在 Xamarin.Forms 中的可繫結配置

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)

可繫結的配置可讓任何配置類別衍生自[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)類別來產生其內容繫結至項目的集合，可設定的每個項目外觀的選項來[ `DataTemplate`](xref:Xamarin.Forms.DataTemplate). 可繫結的配置由提供`BindableLayout`類別，會公開下列連接的屬性：

- `ItemsSource` – 指定的集合`IEnumerable`配置所要顯示的項目。
- `ItemTemplate` – 指定[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)来套用至配置所顯示的項目集合中的每個項目。
- `ItemTemplateSelector` – 指定[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) ，將會用來選擇[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)對於在執行階段的項目。

這些屬性可以附加至[ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout)， [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout)， [ `Grid` ](xref:Xamarin.Forms.Grid)， [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)與[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)類別，這些全都衍生自[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)類別。

> [!NOTE]
> `ItemTemplate`屬性會優先時同時`ItemTemplate`和`ItemTemplateSelector`設定屬性。

`Layout<T>`類別會公開[ `Children` ](xref:Xamarin.Forms.Layout`1.Children)版面配置的子元素所加入的集合。 當`BinableLayout.ItemsSource`屬性設定為項目的集合，並且附加至[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)-衍生的類別，在集合中的每個項目加入至`Layout<T>.Children` 顯示的版面配置的集合。 `Layout<T>`-基礎集合變更時，衍生的類別會接著更新子檢視。 如需有關 Xamarin.Forms 版面配置週期的詳細資訊，請參閱[建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)。

可繫結的配置應該只用於時要顯示的項目集合很小，且不需要的捲動和選取項目。 雖然可以提供藉由包裝在可繫結的版面配置的捲動[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)，建議您不要為可繫結的配置缺少 UI 虛擬化。 捲動時需要，可捲動的檢視，包括 UI 虛擬化，例如[ `ListView` ](xref:Xamarin.Forms.ListView)或是[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)，應該使用。 若要觀察這項建議的失敗可能會導致效能問題。

> [!IMPORTANT]
>雖然技術上可以將衍生自的任何配置類別可繫結的版面配置[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)類別，它不一定總是可行若要這樣做，特別是針對[ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout)[ `Grid` ](xref:Xamarin.Forms.Grid)，以及[ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)類別。 例如，假設的想要顯示集合中的資料[ `Grid` ](xref:Xamarin.Forms.Grid)使用可繫結的配置，其中集合中的每個項目一個物件包含多個屬性。 每個資料列`Grid`應該會顯示集合中每個資料行中具有從物件`Grid`顯示其中一個物件的屬性。 因為[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)可繫結的配置可以只包含單一物件，就必須為該物件可包含多個檢視，每一個物件的屬性中顯示特定配置類別`Grid`資料行。 雖然這種情況下可以膚淺與可繫結的配置，則會導致父代`Grid`包含子系`Grid`繫結集合中每個項目，這是高效率不佳，且有問題使用`Grid`版面配置。

## <a name="populating-a-bindable-layout-with-data"></a>填入資料的可繫結配置

可繫結的配置以資料填入藉由設定其`ItemsSource`屬性來實作任何集合`IEnumerable`，並附加至[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)-衍生的類別：

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

對等的 C# 程式碼是：

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

當`BindableLayout.ItemsSource`附加的屬性設定版面配置，但`BindableLayout.ItemTemplate`附加的屬性未設定，每個項目`IEnumerable`集合將會顯示由[ `Label` ](xref:Xamarin.Forms.Label) ，它由`BindableLayout`類別。

## <a name="defining-item-appearance"></a>定義項目外觀

可以藉由設定定義可繫結的配置中的每個項目的外觀`BindableLayout.ItemTemplate`附加屬性[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

對等的 C# 程式碼是：

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

在此範例中，在每個項目`TopFollowers`集合將會顯示所`CircleImage`檢視中定義[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

![可繫結的版面配置使用 DataTemplate](bindable-layouts-images/top-followers.png "可繫結的版面配置與資料範本")

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choosing-item-appearance-at-runtime"></a>在執行階段選擇的項目外觀

在執行階段，根據所設定的項目值，可以選擇可繫結的配置中的每個項目的外觀`BindableLayout.ItemTemplateSelector`附加屬性[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

對等的 C# 程式碼是：

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)用在此範例應用程式會顯示在下列範例中：

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

`TechItemTemplateSelector`類別會定義`DefaultTemplate`並`XamarinFormsTemplate` [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)屬性設定為不同的資料範本。 `OnSelectTemplate`方法會傳回`XamarinFormsTemplate`，其中顯示項目中使用的核心，深紅色等於"Xamarin.Forms 」 項目時。 當項目不等於"Xamarin.Forms，「`OnSelectTemplate`方法會傳回`DefaultTemplate`，其中顯示使用的預設色彩的項目[ `Label` ](xref:Xamarin.Forms.Label):

![可繫結的版面配置與 DataTemplateSelector](bindable-layouts-images/favorite-tech.png "可繫結的版面配置與資料範本選取器")

如需有關資料範本選取器的詳細資訊，請參閱[建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [可繫結配置示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)
- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

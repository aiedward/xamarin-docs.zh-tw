---
title: Xamarin 中可繫結的版面配置
description: 可繫結的版面配置可讓配置類別繫結項目的集合，以產生其內容。而且您可以使用 DataTemplate 來設定每個項目的外觀。
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: a824c892d21df9264b772bed09a4aef893f3b949
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "68647899"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Xamarin 中可繫結的版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

可繫結的版面配置可讓衍生自 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 的任何版面配置類別透過繫結至項目集合來產生其內容 (並提供使用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 來設定每個項目外觀的選項)。 可繫結的配置是由 `BindableLayout` 類別所提供，此類別會公開下列附加屬性：

- `ItemsSource`– 指定要由版面配置顯示的 `IEnumerable` 項目集合。
- `ItemTemplate` – 指定 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 以套用到版面配置顯示之項目的集合中的每個項目。
- `ItemTemplateSelector` – 指定將用來在執行階段為項目選擇 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 的 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)。

這些屬性可以附加至 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)、[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)、[`Grid`](xref:Xamarin.Forms.Grid)、[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 和 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 類別，它們全都衍生自 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 類別。

> [!NOTE]
> 當`ItemTemplate` 與 `ItemTemplateSelector`兩個屬性都已設定時，會優先使用 `ItemTemplate` 屬性。

`Layout<T>` 類別會公開 [`Children`](xref:Xamarin.Forms.Layout`1.Children)集合，版面配置的子元素會新增到此集合中。 當 `BinableLayout.ItemsSource`屬性設定為項目集合並附加到 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 衍生類別時，集合中的每個項目都會新增到 `Layout<T>.Children` 集合。以由版面配置顯示。 `Layout<T>` 衍生類別接著會在底層集合變更時更新其子檢視。 如需有關 [Xamarin.Forms] 版面配置週期的詳細資訊，請參閱[建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)(部分機器翻譯)。

只有當要顯示的專案集合很小，而且不需要進行捲動及選取時，才應該使用可繫結的版面配置。 雖然可以透過在 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 中包裝可繫結的版面配置來提供捲動，但建議不要這樣做，因為可繫結的版面配置缺乏 UI 虛擬化。 需要進行捲動時，應該使用包含 UI 虛擬化的可捲動檢視，例如 [`ListView`](xref:Xamarin.Forms.ListView) 或 [`CollectionView`](xref:Xamarin.Forms.CollectionView)。 若未遵循此建議，可能會導致效能問題。

> [!IMPORTANT]
>雖然技術上可以將可繫結的版面配置附加到衍生自 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 類別的任何版面配置類別，但這樣做並不一定可行，特別是對 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)、[`Grid`](xref:Xamarin.Forms.Grid) 與 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 類別而言。 例如，假設您想要使用可繫結的版面配置在 [`Grid`](xref:Xamarin.Forms.Grid) 中顯示資料集合，其中集合中的每個項目都是包含多個屬性的物件。 `Grid` 中的每列都應該會顯示來自集合的物件，而 `Grid` 中的每欄都會顯示物件的其中一個屬性。 因為可繫結版面配置的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 只能包含單一物件，所以該物件必須是包含多個檢視 (其中每個檢視都會顯示特定 `Grid` 欄中其中一個物件屬性) 的版面配置類別。 雖然可以使用可繫結版面配置來實現此案例，但它會針對繫結集合中的每個項目產生包含子系 `Grid` 的父系 `Grid`，這種使用 `Grid` 版面配置的方式不僅非常沒效率，而且很容易發生問題。

## <a name="populating-a-bindable-layout-with-data"></a>以資料填入可繫結的版面配置

可繫結的配置會透過將其 `ItemsSource` 屬性設定為實作`IEnumerable` 的任何集合並將它附加到 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 衍生類別，以填入資料：

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

對等的 C# 程式碼為：

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

當在版面配置上設定 `BindableLayout.ItemsSource` 附加屬性，但未設定 `BindableLayout.ItemTemplate` 附加屬性時，`IEnumerable` 集合中的每個項目都將會由 [`Label`](xref:Xamarin.Forms.Label) (由 `BindableLayout` 類別所建立) 來顯示。

## <a name="defining-item-appearance"></a>定義項目外觀

將 `BindableLayout.ItemTemplate` 附加屬性設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，即可定義可繫結版面配置中每個項目的外觀：

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

對等的 C# 程式碼為：

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

在此範例中，`TopFollowers` 集合中的每個項目都會由 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 中定義的 `CircleImage` 顯示：

![具有 DataTemplate 的可繫結版面配置](bindable-layouts-images/top-followers.png "具有資料範本的可繫結版面配置")

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choosing-item-appearance-at-runtime"></a>在執行階段選擇項目外觀

藉由將 `BindableLayout.ItemTemplateSelector` 附加屬性設定為 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)，可以根據項目值在執行階段選擇可繫結版面配置中每個項目的外觀：

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

對等的 C# 程式碼為：

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

範例應用程式中所使用的 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 如下列範例所示：

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

`TechItemTemplateSelector` 類別定義設定為不同資料範本的 `DefaultTemplate` 與 `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 屬性。 `OnSelectTemplate` 方法會傳回 `XamarinFormsTemplate`，這會以深紅色顯示項目，而且它旁邊有一個心形符號。 當項目不等於 "Xamarin.Forms" 時，`OnSelectTemplate` 方法會傳回 `DefaultTemplate`，它會使用 [`Label`](xref:Xamarin.Forms.Label) 的預設色彩來顯示項目：

![具有 DataTemplateSelector 的可繫結版面配置](bindable-layouts-images/favorite-tech.png "具有資料範本選取器的可繫結版面配置")

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [可繫結的版面配置示範 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

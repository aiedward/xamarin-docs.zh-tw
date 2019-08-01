---
title: Xamarin 中可繫結的版面配置
description: 可繫結的版面配置可讓配置類別繫結至專案集合, 以產生其內容, 並選擇使用 DataTemplate 來設定每個專案的外觀。
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: a824c892d21df9264b772bed09a4aef893f3b949
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647899"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Xamarin 中可繫結的版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

可繫結的版面配置可讓任何衍生[`Layout<T>`](xref:Xamarin.Forms.Layout`1)自類別的版面配置類別, 藉由繫結至專案集合來產生其內容, 並提供選項來設定每[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)個專案的外觀。 可繫結的配置是`BindableLayout`由類別所提供, 它會公開下列附加屬性:

- `ItemsSource`–指定要由配置`IEnumerable`顯示的專案集合。
- `ItemTemplate`–指定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)要套用至配置所顯示專案集合中每個專案的。
- `ItemTemplateSelector`–指定[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)將用來在執行時間為專案[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)選擇的。

這些屬性可以附加至[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)、 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)、 [`Grid`](xref:Xamarin.Forms.Grid)、 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)和[`StackLayout`](xref:Xamarin.Forms.StackLayout)類別, 它們全都衍生自[`Layout<T>`](xref:Xamarin.Forms.Layout`1)類別。

> [!NOTE]
> 當`ItemTemplate` 同時設定`ItemTemplateSelector`和屬性時, 會優先使用屬性。 `ItemTemplate`

`Layout<T>`類別會[`Children`](xref:Xamarin.Forms.Layout`1.Children)公開集合, 其中會加入配置的子項目。 當屬性設定為專案的集合, 並且附加[`Layout<T>`](xref:Xamarin.Forms.Layout`1)至衍生類別時, `Layout<T>.Children`集合中的每個專案都會加入集合中, 供配置顯示。 `BinableLayout.ItemsSource` 然後`Layout<T>`, 衍生的類別會在基礎集合變更時, 更新其子視圖。 如需有關 [Xamarin] 版面配置週期的詳細資訊, 請參閱[建立自訂版面](~/xamarin-forms/user-interface/layouts/custom.md)配置。

只有當要顯示的專案集合很小, 而且不需要進行滾動和選取時, 才應該使用可繫結的版面配置。 雖然可以藉由在中[`ScrollView`](xref:Xamarin.Forms.ScrollView)包裝可繫結的配置來提供滾動, 但不建議將此功能當做可繫結的配置缺乏 UI 虛擬化。 需要進行滾動時, 應該使用包含 UI 虛擬化的可滾動視圖[`ListView`](xref:Xamarin.Forms.ListView) , [`CollectionView`](xref:Xamarin.Forms.CollectionView)例如或。 若無法觀察這項建議, 可能會導致效能問題。

> [!IMPORTANT]
>雖然技術上可以將可繫結的配置附加[`Layout<T>`](xref:Xamarin.Forms.Layout`1)至衍生自類別的任何版面配置類別, 但這樣做並不一定可行, 特別是[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)針對、 [`Grid`](xref:Xamarin.Forms.Grid)和[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)類別。 例如, 假設您想要[`Grid`](xref:Xamarin.Forms.Grid)使用可繫結的版面配置來顯示中的資料集合, 其中集合中的每個專案都是包含多個屬性的物件。 中的`Grid`每個資料列應該會顯示集合中的物件, 而中的`Grid`每個資料行會顯示物件的其中一個屬性。 因為可[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)繫結配置的只能包含單一物件, 所以該物件必須是包含多個 views 的版面配置類別, 而每個視圖都會在特定`Grid`資料行中顯示物件的其中一個屬性。 雖然可以使用可繫結的配置來 realised 此案例, 但它`Grid`會產生一個`Grid`父系, 其中包含繫結集合中每個專案的子繫, 這對配置`Grid`而言非常沒有效率且有問題的用法。

## <a name="populating-a-bindable-layout-with-data"></a>以資料填入可繫結的版面配置

可繫結的配置會將其`ItemsSource`屬性設定為任何可執行檔集合`IEnumerable`, [`Layout<T>`](xref:Xamarin.Forms.Layout`1)並將其附加至衍生類別, 以填入資料:

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

對等的 C# 程式碼是：

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

`BindableLayout` [`Label`](xref:Xamarin.Forms.Label) `IEnumerable` `BindableLayout.ItemTemplate`當附加屬性設定于版面配置上, 但未設定附加屬性時, 集合中的每個專案都會由類別所建立的來顯示。 `BindableLayout.ItemsSource`

## <a name="defining-item-appearance"></a>定義專案外觀

將`BindableLayout.ItemTemplate`附加屬性設定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)為, 即可定義可系結配置中每個專案的外觀:

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

在此範例中, `TopFollowers`集合中的每個專案都會`CircleImage`由中定義的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)視圖顯示:

![具有 DataTemplate]的可繫結版面配置(bindable-layouts-images/top-followers.png "具有資料範本的")可繫結版面配置

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choosing-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉由將`BindableLayout.ItemTemplateSelector`附加屬性設定[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)為, 可以在執行時間根據專案值選擇可繫結版面配置中每個專案的外觀:

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

範例[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)應用程式中所使用的會如下列範例所示:

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

類別會定義`DefaultTemplate`設定`XamarinFormsTemplate`為不同資料範本的和[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)屬性。 `TechItemTemplateSelector` `OnSelectTemplate`方法會傳回,這會在專案與"Xamarin"相等時,以深紅色顯示專案,並在其旁邊`XamarinFormsTemplate`有一個心形。 當專案不等於 "Xamarin" 時, `OnSelectTemplate`方法`DefaultTemplate`會傳回, 它會使用的預設色彩[`Label`](xref:Xamarin.Forms.Label)來顯示專案:

![具有 DataTemplateSelector]的可繫結版面配置(bindable-layouts-images/favorite-tech.png "使用資料範本選取器進行")可繫結的版面配置

如需資料範本選取器的詳細資訊, 請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [可繫結的版面配置示範 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin. 表單資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [建立 Xamarin 表單 DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

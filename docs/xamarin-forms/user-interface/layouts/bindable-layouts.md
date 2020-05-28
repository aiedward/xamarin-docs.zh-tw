---
title: 中可系結的版面配置Xamarin.Forms
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
ms.openlocfilehash: 9d0497c0c0593b54f69bac84307976c4050e9c95
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138238"
---
# <a name="bindable-layouts-in-xamarinforms"></a>中可系結的版面配置Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

可系結的版面配置可讓任何衍生自類別的版面配置類別，藉由系結 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 至專案集合來產生其內容，並提供選項來設定每個專案的外觀 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 可系結的配置是由類別所提供 `BindableLayout` ，它會公開下列附加屬性：

- `ItemsSource`–指定 `IEnumerable` 要由配置顯示的專案集合。
- `ItemTemplate`–指定要套用至配置所 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 顯示專案集合中每個專案的。
- `ItemTemplateSelector`–指定 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 將用來 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 在執行時間為專案選擇的。

> [!NOTE]
> `ItemTemplate`當同時 `ItemTemplate` 設定和屬性時，會優先使用屬性 `ItemTemplateSelector` 。

此外，類別會 `BindableLayout` 公開下列可系結屬性：

- `EmptyView`–指定 `string` 當 `ItemsSource` 屬性為時 `null` ，或由屬性所指定的集合 `ItemsSource` 為 `null` 或空白時，將顯示的或 view。 預設值是 `null`。
- `EmptyViewTemplate`–指定 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 當 `ItemsSource` 屬性為時 `null` ，或由屬性所指定的集合 `ItemsSource` 為 `null` 或空白時，將顯示的。 預設值是 `null`。

> [!NOTE]
> `EmptyViewTemplate`當同時 `EmptyView` 設定和屬性時，會優先使用屬性 `EmptyViewTemplate` 。

所有這些屬性都可以附加至 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 、 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) 、 [`Grid`](xref:Xamarin.Forms.Grid) 、 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 和 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 類別，這些都是衍生自 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 類別。

`Layout<T>`類別 [`Children`](xref:Xamarin.Forms.Layout`1.Children) 會公開集合，其中會加入配置的子項目。 當 `BinableLayout.ItemsSource` 屬性設定為專案的集合，並且附加至 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 衍生類別時，集合中的每個專案都會加入集合中， `Layout<T>.Children` 供配置顯示。 `Layout<T>`然後，衍生的類別會在基礎集合變更時，更新其子視圖。 如需版面配置週期的詳細資訊 Xamarin.Forms ，請參閱[建立自訂版面](~/xamarin-forms/user-interface/layouts/custom.md)配置。

只有當要顯示的專案集合很小，而且不需要進行滾動和選取時，才應該使用可系結的版面配置。 雖然可以藉由在中包裝可系結的配置來提供滾動，但不建議將此功能當做可系結的配置 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 缺乏 UI 虛擬化。 需要進行滾動時，應該使用包含 UI 虛擬化的可滾動視圖，例如 [`ListView`](xref:Xamarin.Forms.ListView) 或 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 若無法觀察這項建議，可能會導致效能問題。

> [!IMPORTANT]
> 雖然技術上可以將可系結的配置附加至衍生自類別的任何版面配置類別，但這樣 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) 做並不一定可行，特別是針對 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 、 [`Grid`](xref:Xamarin.Forms.Grid) 和 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 類別。 例如，假設您想要使用可系結的版面配置來顯示中的資料集合 [`Grid`](xref:Xamarin.Forms.Grid) ，其中集合中的每個專案都是包含多個屬性的物件。 中的每個資料 `Grid` 列應該會顯示集合中的物件，而中的每個資料行會顯示 `Grid` 物件的其中一個屬性。 因為可系結配置的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 只能包含單一物件，所以該物件必須是包含多個 views 的版面配置類別，而每個視圖都會在特定資料行中顯示物件的其中一個屬性 `Grid` 。 雖然可以使用可系結的配置來 realised 此案例，但它會產生一個父系 `Grid` ，其中包含系結 `Grid` 集合中每個專案的子系，這對配置而言非常沒有效率且有問題的用法 `Grid` 。

## <a name="populate-a-bindable-layout-with-data"></a>以資料填入可系結的版面配置

可系結的配置會將其屬性設定 `ItemsSource` 為任何可執行檔集合，並將其 `IEnumerable` 附加至衍生類別，以填入資料 [`Layout<T>`](xref:Xamarin.Forms.Layout`1) ：

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

對等的 C# 程式碼為：

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

當 `BindableLayout.ItemsSource` 附加屬性設定于版面配置上，但 `BindableLayout.ItemTemplate` 未設定附加屬性時，集合中的每個專案 `IEnumerable` 都會由 [`Label`](xref:Xamarin.Forms.Label) 類別所建立的來顯示 `BindableLayout` 。

## <a name="define-item-appearance"></a>定義專案外觀

將附加屬性設定為，即可定義可系結配置中每個專案的外觀 `BindableLayout.ItemTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

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

在此範例中，集合中的每個專案 `TopFollowers` 都會由 `CircleImage` 中定義的視圖顯示 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

![具有 DataTemplate 的可系結版面配置](bindable-layouts-images/top-followers.png "具有資料範本的可系結版面配置")

如需資料範本的詳細資訊，請參閱[ Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉由將附加屬性設定為，可以在執行時間根據專案值選擇可系結版面配置中每個專案的外觀 `BindableLayout.ItemTemplateSelector` [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ：

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

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)範例應用程式中所使用的會如下列範例所示：

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

`TechItemTemplateSelector`類別會定義 `DefaultTemplate` `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 設定為不同資料範本的和屬性。 `OnSelectTemplate`方法 `XamarinFormsTemplate` 會傳回，這會在專案等於 "" 的情況下，以深紅色顯示具有一個心形的專案 Xamarin.Forms 。 當專案不等於 "" 時 Xamarin.Forms ，方法會傳回 `OnSelectTemplate` `DefaultTemplate` ，它會使用的預設色彩來顯示專案 [`Label`](xref:Xamarin.Forms.Label) ：

![具有 DataTemplateSelector 的可系結版面配置](bindable-layouts-images/favorite-tech.png "使用資料範本選取器進行可系結的版面配置")

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>當資料無法使用時顯示字串

`EmptyView`屬性可以設定為字串， [`Label`](xref:Xamarin.Forms.Label) 當 `ItemsSource` 屬性為時 `null` ，或由屬性所指定的集合 `ItemsSource` 為 `null` 或空白時，就會顯示。 下列 XAML 顯示此案例的範例：

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}"
             BindableLayout.EmptyView="No achievements">
    ...
</StackLayout>
```

結果是，當資料系結集合為時 `null` ， `EmptyView` 會顯示設定為屬性值的字串：

[![在 iOS 和 Android 上的可系結配置字串空白視圖的螢幕擷取畫面](bindable-layouts-images/emptyview-string.png "可系結的版面配置字串空白視圖")](bindable-layouts-images/emptyview-string-large.png#lightbox "可系結的版面配置字串空白視圖")

## <a name="display-views-when-data-is-unavailable"></a>資料無法使用時顯示視圖

`EmptyView`屬性可以設定為 view，當 `ItemsSource` 屬性為時 `null` ，或由屬性所指定的集合 `ItemsSource` 為 `null` 或空白時，就會顯示。 這可以是單一視圖，或包含多個子視圖的視圖。 下列 XAML 範例顯示將 `EmptyView` 屬性設定為包含多個子視圖的視圖：

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyView>
        <StackLayout>
            <Label Text="None."
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
            <Label Text="Try harder and return later?"
                   FontAttributes="Italic"
                   FontSize="{StaticResource smallTextSize}" />
        </StackLayout>
    </BindableLayout.EmptyView>
    ...
</StackLayout>
```

結果是，當資料系結集合為時 `null` ， [`StackLayout`](xref:Xamarin.Forms.StackLayout) 會顯示及其子視圖。

[![在 iOS 和 Android 上具有多個視圖的可系結配置空白視圖的螢幕擷取畫面](bindable-layouts-images/emptyview-views.png "可系結的版面配置空白視圖")](bindable-layouts-images/emptyview-views-large.png#lightbox "可系結的版面配置空白視圖")

同樣地， `EmptyViewTemplate` 可以設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，當 `ItemsSource` 屬性為時 `null` ，或由屬性所指定的集合 `ItemsSource` 為 `null` 或空白時，就會顯示。 `DataTemplate`可以包含單一視圖，或包含多個子視圖的視圖。 此外，的 `BindingContext` `EmptyViewTemplate` 會繼承自 `BindingContext` 的 `BindableLayout` 。 下列 XAML 範例顯示將 `EmptyViewTemplate` 屬性設定為 `DataTemplate` 包含單一視圖的：

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
    <BindableLayout.EmptyViewTemplate>
        <DataTemplate>
            <Label Text="{Binding Source={x:Reference usernameLabel}, Path=Text, StringFormat='{0} has no achievements.'}" />
        </DataTemplate>
    </BindableLayout.EmptyViewTemplate>
    ...
</StackLayout>
```

結果是，當資料系結集合為時 `null` ， [`Label`](xref:Xamarin.Forms.Label) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 會顯示中的：

[![在 iOS 和 Android 上可系結的版面配置空白視圖範本的螢幕擷取畫面](bindable-layouts-images/emptyviewtemplate.png "可系結的版面配置空白視圖範本")](bindable-layouts-images/emptyviewtemplate-large.png#lightbox "可系結的版面配置空白視圖範本")

> [!NOTE]
> `EmptyViewTemplate`屬性無法透過來設定 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。

## <a name="choose-an-emptyview-at-runtime"></a>在執行時間選擇 EmptyView

`EmptyView`當資料無法使用時，將會顯示為的視圖，可以定義為 [`ContentView`](xref:Xamarin.Forms.ContentView) 中的物件 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 接著，您 `EmptyView` 可以在 `ContentView` 執行時間根據某些商務邏輯，將屬性設定為特定的。 下列 XAML 顯示此案例的範例：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...    
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No achievements."
                       FontSize="14" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="None."
                       FontAttributes="Italic"
                       FontSize="14" />
                <Label Text="Try harder and return later?"
                       FontAttributes="Italic"
                       FontSize="14" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout>
        ...
        <Switch Toggled="OnEmptyViewSwitchToggled" />

        <StackLayout x:Name="stackLayout"
                     BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}">
            ...
        </StackLayout>
    </StackLayout>
</ContentPage>
```

XAML [`ContentView`](xref:Xamarin.Forms.ContentView) 會在頁面層級中定義兩個物件 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，其中 [`Switch`](xref:Xamarin.Forms.Switch) 物件會控制要將哪個 `ContentView` 物件設定為 `EmptyView` 屬性值。 當 `Switch` 切換時， `OnEmptyViewSwitchToggled` 事件處理常式會執行 `ToggleEmptyView` 方法：

```csharp
void ToggleEmptyView(bool isToggled)
{
    object view = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
    BindableLayout.SetEmptyView(stackLayout, view);
}
```

`ToggleEmptyView`方法會 `EmptyView` 根據屬性的值，將物件的屬性設定 `stackLayout` 為 [`ContentView`](xref:Xamarin.Forms.ContentView) 儲存在中的兩個物件的其中一個 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 。 然後，當資料系結集合為時 `null` ， `ContentView` `EmptyView` 就會顯示設定為屬性的物件：

[![在執行時間、iOS 和 Android 上的空白 view 選項螢幕擷取畫面](bindable-layouts-images/emptyview-runtime.png "可系結的版面配置空白視圖執行時間選擇")](bindable-layouts-images/emptyview-runtime-large.png#lightbox "可系結的版面配置空白視圖執行時間選擇")

## <a name="related-links"></a>相關連結

- [可系結的版面配置示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin.Forms資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

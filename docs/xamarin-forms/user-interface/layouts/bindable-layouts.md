---
title: Xamarin 中可系結的版面配置
description: 可系結的版面配置可讓配置類別系結至專案集合，以產生其內容，並選擇使用 DataTemplate 來設定每個專案的外觀。
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/09/2020
ms.openlocfilehash: d084d910786c24a4b0333ecbc3e893cfe144d404
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517186"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Xamarin 中可系結的版面配置

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

可系結的版面配置可讓任何衍生[`Layout<T>`](xref:Xamarin.Forms.Layout`1)自類別的版面配置類別，藉由系結至專案集合來產生其內容，並提供選項來設定每[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)個專案的外觀。 可系結的配置是`BindableLayout`由類別所提供，它會公開下列附加屬性：

- `ItemsSource`–指定要由配置`IEnumerable`顯示的專案集合。
- `ItemTemplate`–指定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)要套用至配置所顯示專案集合中每個專案的。
- `ItemTemplateSelector`–指定[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)將用來在執行時間為專案[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)選擇的。

> [!NOTE]
> 當`ItemTemplate`同時設定`ItemTemplate`和`ItemTemplateSelector`屬性時，會優先使用屬性。

此外， `BindableLayout`類別會公開下列可系結屬性：

- `EmptyView`–指定當`string` `ItemsSource`屬性為`null`時，或由`ItemsSource`屬性所指定的集合為`null`或空白時，將顯示的或 view。 預設值是 `null`。
- `EmptyViewTemplate`–指定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)當`ItemsSource`屬性為`null`時，或由`ItemsSource`屬性所指定的集合為`null`或空白時，將顯示的。 預設值是 `null`。

> [!NOTE]
> 當`EmptyViewTemplate`同時設定`EmptyView`和`EmptyViewTemplate`屬性時，會優先使用屬性。

所有這些屬性都可以[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)附加至、 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)、 [`Grid`](xref:Xamarin.Forms.Grid)、 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)和[`StackLayout`](xref:Xamarin.Forms.StackLayout)類別，這些都是衍生自[`Layout<T>`](xref:Xamarin.Forms.Layout`1)類別。

`Layout<T>`類別會公開[`Children`](xref:Xamarin.Forms.Layout`1.Children)集合，其中會加入配置的子項目。 當`BinableLayout.ItemsSource`屬性設定為專案的集合，並且附加至[`Layout<T>`](xref:Xamarin.Forms.Layout`1)衍生類別時，集合中的每個專案都會加入`Layout<T>.Children`集合中，供配置顯示。 然後`Layout<T>`，衍生的類別會在基礎集合變更時，更新其子視圖。 如需有關 [Xamarin] 版面配置週期的詳細資訊，請參閱[建立自訂版面](~/xamarin-forms/user-interface/layouts/custom.md)配置。

只有當要顯示的專案集合很小，而且不需要進行滾動和選取時，才應該使用可系結的版面配置。 雖然可以藉由在中包裝可系結的配置[`ScrollView`](xref:Xamarin.Forms.ScrollView)來提供滾動，但不建議將此功能當做可系結的配置缺乏 UI 虛擬化。 需要進行滾動時，應該使用包含 UI 虛擬化的可滾動視圖[`ListView`](xref:Xamarin.Forms.ListView) ， [`CollectionView`](xref:Xamarin.Forms.CollectionView)例如或。 若無法觀察這項建議，可能會導致效能問題。

> [!IMPORTANT]
> 雖然技術上可以將可系結的配置附加[`Layout<T>`](xref:Xamarin.Forms.Layout`1)至衍生自類別的任何版面配置類別，但這樣做並不一定可行，特別是針對[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)、 [`Grid`](xref:Xamarin.Forms.Grid)和[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)類別。 例如，假設您想要[`Grid`](xref:Xamarin.Forms.Grid)使用可系結的版面配置來顯示中的資料集合，其中集合中的每個專案都是包含多個屬性的物件。 中的`Grid`每個資料列應該會顯示集合中的物件，而中的`Grid`每個資料行會顯示物件的其中一個屬性。 因為可[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)系結配置的只能包含單一物件，所以該物件必須是包含多個 views 的版面配置類別，而每個視圖都會在特定`Grid`資料行中顯示物件的其中一個屬性。 雖然可以使用可系結的配置來 realised 此案例，但它`Grid`會產生一個`Grid`父系，其中包含系結集合中每個專案的子系，這對配置`Grid`而言非常沒有效率且有問題的用法。

## <a name="populate-a-bindable-layout-with-data"></a>以資料填入可系結的版面配置

可系結的配置會將其`ItemsSource`屬性設定為任何可執行檔集合`IEnumerable`，並將其附加至[`Layout<T>`](xref:Xamarin.Forms.Layout`1)衍生類別，以填入資料：

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

對等的 C# 程式碼為：

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

當`BindableLayout.ItemsSource`附加屬性設定于版面配置上，但未設定`BindableLayout.ItemTemplate`附加屬性時， `IEnumerable`集合中的每個專案都會由[`Label`](xref:Xamarin.Forms.Label) `BindableLayout`類別所建立的來顯示。

## <a name="define-item-appearance"></a>定義專案外觀

將`BindableLayout.ItemTemplate`附加屬性設定為，即可定義可系結配置中每個專案的外觀[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)：

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

在此範例中， `TopFollowers`集合中的每個專案都會由中`CircleImage`定義的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)視圖顯示：

![具有 DataTemplate 的可系結版面配置](bindable-layouts-images/top-followers.png "具有資料範本的可系結版面配置")

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉由將`BindableLayout.ItemTemplateSelector`附加屬性設定為，可以在執行時間根據專案值選擇可系結版面配置中每個專案的外觀[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)：

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

範例[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)應用程式中所使用的會如下列範例所示：

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

`TechItemTemplateSelector`類別會定義`DefaultTemplate`設定`XamarinFormsTemplate`為不同資料範本的和[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)屬性。 `OnSelectTemplate`方法會傳回`XamarinFormsTemplate`，這會在專案與 "Xamarin" 相等時，以深紅色顯示專案，並在其旁邊有一個心形。 當專案不等於 "Xamarin" 時， `OnSelectTemplate`方法會傳回`DefaultTemplate`，它會使用的預設色彩來顯示專案： [`Label`](xref:Xamarin.Forms.Label)

![具有 DataTemplateSelector 的可系結版面配置](bindable-layouts-images/favorite-tech.png "使用資料範本選取器進行可系結的版面配置")

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="display-a-string-when-data-is-unavailable"></a>當資料無法使用時顯示字串

`EmptyView`屬性可以設定為字串[`Label`](xref:Xamarin.Forms.Label) ，當`ItemsSource`屬性為`null`時，或由`ItemsSource`屬性所指定的集合為`null`或空白時，就會顯示。 下列 XAML 顯示此案例的範例：

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding UserWithoutAchievements.Achievements}"
             BindableLayout.EmptyView="No achievements">
    ...
</StackLayout>
```

結果是，當資料系結集合為`null`時，會顯示設定為`EmptyView`屬性值的字串：

[![在 iOS 和 Android 上的可系結配置字串空白視圖的螢幕擷取畫面](bindable-layouts-images/emptyview-string.png "可系結的版面配置字串空白視圖")](bindable-layouts-images/emptyview-string-large.png#lightbox "可系結的版面配置字串空白視圖")

## <a name="display-views-when-data-is-unavailable"></a>資料無法使用時顯示視圖

`EmptyView`屬性可以`ItemsSource`設定為 view，當屬性為`null`時，或由`ItemsSource`屬性所指定的集合為`null`或空白時，就會顯示。 這可以是單一視圖，或包含多個子視圖的視圖。 下列 XAML 範例顯示將`EmptyView`屬性設定為包含多個子視圖的視圖：

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

結果是，當資料系結集合為`null`時， [`StackLayout`](xref:Xamarin.Forms.StackLayout)會顯示及其子視圖。

[![在 iOS 和 Android 上具有多個視圖的可系結配置空白視圖的螢幕擷取畫面](bindable-layouts-images/emptyview-views.png "可系結的版面配置空白視圖")](bindable-layouts-images/emptyview-views-large.png#lightbox "可系結的版面配置空白視圖")

同樣地， `EmptyViewTemplate`可以[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)設定為， `ItemsSource`當屬性為`null`時，或由`ItemsSource`屬性所指定的集合為`null`或空白時，就會顯示。 `DataTemplate`可以包含單一視圖，或包含多個子視圖的視圖。 此外， `BindingContext`的`EmptyViewTemplate`會繼承自`BindingContext`的。 `BindableLayout` 下列 XAML 範例顯示將`EmptyViewTemplate`屬性設定為`DataTemplate`包含單一視圖的：

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

結果是，當資料系結集合為`null`時， [`Label`](xref:Xamarin.Forms.Label) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)會顯示中的：

[![在 iOS 和 Android 上可系結的版面配置空白視圖範本的螢幕擷取畫面](bindable-layouts-images/emptyviewtemplate.png "可系結的版面配置空白視圖範本")](bindable-layouts-images/emptyviewtemplate-large.png#lightbox "可系結的版面配置空白視圖範本")

> [!NOTE]
> `EmptyViewTemplate`屬性無法透過來設定[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)。

## <a name="choose-an-emptyview-at-runtime"></a>在執行時間選擇 EmptyView

當資料無法使用時，將`EmptyView`會顯示為的視圖，可以定義為[`ContentView`](xref:Xamarin.Forms.ContentView)中的物件[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)。 接著`EmptyView` ，您可以在執行時間根據某些`ContentView`商務邏輯，將屬性設定為特定的。 下列 XAML 顯示此案例的範例：

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

XAML 會在頁面[`ContentView`](xref:Xamarin.Forms.ContentView)層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中定義兩個物件，其中[`Switch`](xref:Xamarin.Forms.Switch)物件會控制`ContentView`要將哪個物件設定為`EmptyView`屬性值。 當切換`Switch`時， `OnEmptyViewSwitchToggled`事件處理常式會執行`ToggleEmptyView`方法：

```csharp
void ToggleEmptyView(bool isToggled)
{
    object view = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
    BindableLayout.SetEmptyView(stackLayout, view);
}
```

`ToggleEmptyView`方法`EmptyView`會根據`stackLayout` [`ContentView`](xref:Xamarin.Forms.ContentView) [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)屬性的值，將物件的屬性設定為儲存在中的兩個物件的其中一個。 [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) 然後，當資料系結集合為`null`時， `ContentView`就會顯示設定`EmptyView`為屬性的物件：

[![在執行時間、iOS 和 Android 上的空白 view 選項螢幕擷取畫面](bindable-layouts-images/emptyview-runtime.png "可系結的版面配置空白視圖執行時間選擇")](bindable-layouts-images/emptyview-runtime-large.png#lightbox "可系結的版面配置空白視圖執行時間選擇")

## <a name="related-links"></a>相關連結

- [可系結的版面配置示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [建立自訂版面配置](~/xamarin-forms/user-interface/layouts/custom.md)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

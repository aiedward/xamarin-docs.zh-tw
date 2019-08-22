---
title: Xamarin. 表單 CollectionView 資料
description: CollectionView 會藉由將其 ItemsSource 屬性設定為任何可執行 IEnumerable 的集合來填入資料。
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2019
ms.openlocfilehash: 6942baed6af2a2e9b2c713a8fe08cf4c8ed4416b
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888539"
---
# <a name="xamarinforms-collectionview-data"></a>Xamarin. 表單 CollectionView 資料

![](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義下列定義要顯示之資料的屬性, 以及其外觀:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), 屬於類型`IEnumerable`, 可指定要顯示的專案集合, 並具有的預設`null`值。
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), 屬於類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate), 可指定要套用至要顯示的專案集合中每個專案的範本。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這表示屬性可以是資料系結的目標。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)定義屬性, 代表將`CollectionView`新專案加入其中時的滾動行為。 `ItemsUpdatingScrollMode` 如需這個屬性的詳細資訊, 請參閱[在新增新專案時控制捲軸位置](scrolling.md#control-scroll-position-when-new-items-are-added)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)也可以在使用者滾動時, 以累加方式載入資料。 如需詳細資訊, 請參閱[以累加方式載入資料](#load-data-incrementally)。

## <a name="populate-a-collectionview-with-data"></a>在 CollectionView 中填入資料

會[`CollectionView`](xref:Xamarin.Forms.CollectionView)藉由[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)將資料的屬性設定`IEnumerable`為任何可執行檔集合, 來填入資料。 藉由從字串陣列初始化`ItemsSource`屬性, 可以在 XAML 中加入專案:

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> 請注意，`x:Array` 項目需要 `Type` 屬性，以指出陣列中的項目類型。

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!IMPORTANT]
> 如果在基礎集合中加入、移除或變更專案時需要重新整理, 基礎集合應該`IEnumerable`是傳送屬性`ObservableCollection`變更通知的集合, 例如。 [`CollectionView`](xref:Xamarin.Forms.CollectionView)

根據預設, [`CollectionView`](xref:Xamarin.Forms.CollectionView)會在垂直清單中顯示專案, 如下列螢幕擷取畫面所示:

[ (populate-data-images/text.png "CollectionView 中") ![iOS 和 Android 文字專案上包含文字專案之 CollectionView 的螢幕擷取畫面]](populate-data-images/text-large.png#lightbox "CollectionView 中的文字專案")

如需如何變更版面配置的[`CollectionView`](xref:Xamarin.Forms.CollectionView)詳細資訊, 請參閱[指定版面](layout.md)配置。 如需如何在中`CollectionView`定義每個專案外觀的詳細資訊, 請參閱[定義專案外觀](#define-item-appearance)。

### <a name="data-binding"></a>資料繫結

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以使用資料系結將其[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性`IEnumerable`系結至集合, 以填入資料。 在 XAML 中, 這是透過`Binding`標記延伸來達成:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

在此範例中, [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性資料會系結`Monkeys`至已連接之 viewmodel 的屬性。

> [!NOTE]
> 可以啟用編譯的系結, 以改善 Xamarin 中的資料系結效能。 如需詳細資訊，請參閱[編譯繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="define-item-appearance"></a>定義專案外觀

將[`CollectionView`](xref:Xamarin.Forms.CollectionView) [屬性`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate), 即可定義中每個專案的外觀:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
    ...
</CollectionView>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    Grid grid = new Grid { Padding = 10 };
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });

    Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 60, WidthRequest = 60 };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Label locationLabel = new Label { FontAttributes = FontAttributes.Italic, VerticalOptions = LayoutOptions.End };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Grid.SetRowSpan(image, 2);

    grid.Children.Add(image);
    grid.Children.Add(nameLabel, 1, 0);
    grid.Children.Add(locationLabel, 1, 1);

    return grid;
});
```

在中[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)指定的元素會定義清單中每個專案的外觀。 在此範例中, 中的`DataTemplate`配置是[`Grid`](xref:Xamarin.Forms.Grid)由所管理。 包含物件和[`Label`](xref:Xamarin.Forms.Label)兩個物件`Monkey` , 全都系結至類別的屬性: `Grid` [`Image`](xref:Xamarin.Forms.Image)

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

下列螢幕擷取畫面顯示清單中每個專案的範本化結果:

[ ![CollectionView 的螢幕擷取畫面, 其中每個專案都樣板化,](populate-data-images/datatemplate.png "CollectionView 中")的 iOS 和 Android 樣板化專案](populate-data-images/datatemplate-large.png#lightbox "CollectionView 中的樣板化專案")

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉由[`CollectionView`](xref:Xamarin.Forms.CollectionView) [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)將屬性設定為[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)物件, 可在執行時間根據專案值選擇中每個專案的外觀:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CollectionView ItemsSource="{Binding Monkeys}"
                    ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

屬性會設定`MonkeyDataTemplateSelector`為物件。 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) 下列範例顯示`MonkeyDataTemplateSelector`類別:

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

類別會定義`AmericanMonkey`設定`OtherMonkey`為不同資料範本的和[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)屬性。 `MonkeyDataTemplateSelector` `AmericanMonkey`此`OnSelectTemplate`覆寫會傳回範本, 當猴子名稱包含 "北美洲" 時, 會以青色顯示猴子名稱和位置。 當猴子名稱不包含 "北美洲" 時, `OnSelectTemplate`覆寫`OtherMonkey`會傳回範本, 以顯示銀級中的猴子名稱和位置:

[ ![CollectionView runtime 專案範本選取專案的螢幕擷取畫面, 在 IOS 和 Android 執行時間](populate-data-images/datatemplateselector.png "專案範本中選取 CollectionView") ](populate-data-images/datatemplateselector-large.png#lightbox "CollectionView 中的執行時間專案範本選擇")

如需資料範本選取器的詳細資訊, 請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

> [!IMPORTANT]
> 使用[`CollectionView`](xref:Xamarin.Forms.CollectionView)時, 絕對不會將[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件的根項目設定為`ViewCell`。 這會導致擲回例外狀況, 因為`CollectionView`沒有資料格的概念。

## <a name="load-data-incrementally"></a>以累加方式載入資料

[`CollectionView`](xref:Xamarin.Forms.CollectionView)當使用者透過專案滾動時, 支援以累加方式載入資料。 這可讓您在使用者滾動時, 從 web 服務以非同步方式載入資料頁等案例。 此外, 您可以設定載入更多資料的時間點, 讓使用者看不到空白空間, 或停止滾動。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義下列屬性來控制資料的累加式載入:

- `RemainingItemsThreshold`, 屬於類型`int`, `RemainingItemsThresholdReached`這是在引發事件的清單中尚未顯示的專案閾值。
- `RemainingItemsThresholdReachedCommand`, 屬於類型`ICommand`, 這是在到達`RemainingItemsThreshold`時執行的。
- `RemainingItemsThresholdReachedCommandParameter`，屬於 `object` 類型，這是傳遞至 `RemainingItemsThresholdReachedCommand` 的參數。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)也會定義`RemainingItemsThresholdReached`當向上滾動到`CollectionView`尚未顯示專案的`RemainingItemsThreshold`程度時, 所引發的事件。 您可以處理這個事件, 以載入更多專案。 此外, 當`RemainingItemsThresholdReached`事件`RemainingItemsThresholdReachedCommand`引發時, 會執行, 以便在 viewmodel 中進行累加式資料載入。

`RemainingItemsThreshold`屬性的預設值為-1, 表示`RemainingItemsThresholdReached`永遠不會引發事件。 當屬性值為0時, 當`RemainingItemsThresholdReached` [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)中的最後一個專案顯示時, 就會引發事件。 對於大於0的值, `RemainingItemsThresholdReached` `ItemsSource`當包含尚未滾動到的專案數目時, 就會引發事件。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)`RemainingItemsThreshold`驗證屬性, 使其值一律大於或等於-1。

下列 XAML 範例顯示[`CollectionView`](xref:Xamarin.Forms.CollectionView) , 它會以累加方式載入資料:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                RemainingItemsThreshold="5"
                RemainingItemsThresholdReached="OnCollectionViewRemainingItemsThresholdReached">
    ...
</CollectionView>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    RemainingItemsThreshold = 5
};
collectionView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

在此程式碼範例中`RemainingItemsThresholdReached` , 當有5個專案尚未滾動到時, 就會引發事件, 而`OnCollectionViewRemainingItemsThresholdReached`在回應中會執行事件處理常式:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> 您也可以藉由`RemainingItemsThresholdReachedCommand`將系結`ICommand`至 viewmodel 中的實作為增量方式載入資料。

## <a name="related-links"></a>相關連結

- [CollectionView (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. 表單資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [建立 Xamarin 表單 DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

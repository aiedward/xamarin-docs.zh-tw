---
title: Xamarin. 表單地圖釘選
description: 本文說明如何在 Xamarin. 表單地圖上建立 pin。
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/23/2019
ms.openlocfilehash: 76535f9c31a9dc138e132a3e582b986daf89bdb0
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697336"
---
# <a name="xamarinforms-map-pins"></a>Xamarin. 表單地圖釘選

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

@No__t_0 控制項的 Xamarin 可以讓位置以 `Pin` 物件標記。 @No__t_0 是在按一下或按下時，會開啟 [資訊] 視窗的地圖示記。

@No__t_0 類別具有下列屬性：

- `Type` 是 `PinType` 列舉值： Generic、Place、SavedPin 或 SearchResult。
- `Position` 是包含 pin 緯度和經度的 `Position` 實例。
- `Label` 是通常會顯示為釘選標題的 `string`。
- `Address` 是將顯示在 [資訊] 視窗中的 `string`。 它可以是任何 `string` 內容，而不只是位址。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示 `Pin` 可以是資料系結的目標。 如需詳細資訊，請參閱[建立具有資料系結的 pin](#create-pins-with-data-binding)。

## <a name="create-map-pins"></a>建立地圖釘選

您可以在程式碼中建立 `Pin` 實例，並將其新增至對應：

```csharp
Pin pin1 = new Pin
{
    Type = PinType.Place,
    Position = new Position(47.6368678, -122.137305),
    Label = "Example Pin 1",
    Address = "Example custom details..."
};
map.Pins.Add(pin1);
```

> [!NOTE]
> @No__t_0 值會影響根據平臺呈現 pin 的方式。 若要自訂 pin 的外觀，您必須建立自訂轉譯器。 如需詳細資訊，請參閱[自訂地圖釘](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)選。

## <a name="create-pins-with-data-binding"></a>建立具有資料系結的 pin

[@No__t_1](xref:Xamarin.Forms.Maps.Map)類別會公開下列屬性：

- `ItemsSource` –指定要顯示 `IEnumerable` 專案的集合。
- `ItemTemplate` –指定要套用至所顯示專案集合中每個專案的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。
- `ItemTemplateSelector` –指定將用來在執行時間選擇專案[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。

> [!NOTE]
> 當同時設定 `ItemTemplate` 和 `ItemTemplateSelector` 屬性時，`ItemTemplate` 屬性會優先使用。

您可以使用資料系結將其 `ItemsSource` 屬性系結至 `IEnumerable` 集合，以填入[`Map`](xref:Xamarin.Forms.Maps.Map)的資料：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  MoveToLastRegionOnLayoutChange="false"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

@No__t_0 屬性資料會系結至已連線視圖模型的 `Locations` 屬性，它會傳回 `Location` 物件的 `ObservableCollection`，也就是自訂類型。 每個 `Location` 物件會定義 `string` 類型的 `Address` 和 `Description` 屬性，[以及 `Position` 類型](xref:Xamarin.Forms.Maps.Position)的 `Position` 屬性。

@No__t_0 集合中每個專案的外觀都是藉由將 `ItemTemplate` 屬性設定為包含[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)來定義，而資料會系結至適當的屬性。

下列螢幕擷取畫面顯示使用資料系結顯示[`Pin`](xref:Xamarin.Forms.Maps.Pin)集合的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

[![IOS 和 Android 上具有資料系結圖釘的對應螢幕擷取畫面](map-images/pins-itemssource.png "具有資料系結圖釘的對應")](map-images/pins-itemssource-large.png#lightbox "具有資料系結圖釘的對應")

### <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉由將 `ItemTemplateSelector` 屬性設定為[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)，可以在執行時間根據專案值選擇 `IEnumerable` 集合中每個專案的外觀：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:WorkingWithMaps"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <ContentPage.Resources>
        <local:MapItemTemplateSelector x:Key="MapItemTemplateSelector">
            <local:MapItemTemplateSelector.DefaultTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </local:MapItemTemplateSelector.DefaultTemplate>
            <local:MapItemTemplateSelector.XamarinTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="Xamarin!" />
                </DataTemplate>
            </local:MapItemTemplateSelector.XamarinTemplate>    
        </local:MapItemTemplateSelector>
    </ContentPage.Resources>

    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}"
                  ItemTemplateSelector="{StaticResource MapItemTemplateSelector}" />
        ...
    </Grid>
</ContentPage>
```

下列範例顯示 `MapItemTemplateSelector` 類別：

```csharp
public class MapItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Location)item).Address.Contains("San Francisco") ? XamarinTemplate : DefaultTemplate;
    }
}
```

@No__t_0 類別會定義設定為不同資料範本的 `DefaultTemplate` 和 `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)屬性。 @No__t_0 方法會傳回 `XamarinTemplate`，當您按下 `Pin` 時，會將 "Xamarin" 顯示為標籤，而當專案具有包含「三藩市」的位址時。 當專案沒有包含「三藩市」的位址時，`OnSelectTemplate` 方法會傳回 `DefaultTemplate`。

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="pin-events"></a>釘選事件

@No__t_0 類別提供兩個事件：

- 當按一下或按下 pin 時，就會引發 `MarkerClicked`。
- 當按一下或點擊 [資訊] 視窗時，就會引發 `InfoWindowClicked`。

事件處理常式可以附加至代碼中的 pin：

```csharp
public class PinEvents: ContentPage
{
    public PinEvents()
    {
        // ...

        pin1.MarkerClicked += OnMarkerClickedAsync;
        pin1.InfoWindowClicked += OnInfoWindowClickedAsync;
    }

    async void OnMarkerClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
    }

    async void OnInfoWindowClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
    }
}
```

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [建立 Xamarin 表單 DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

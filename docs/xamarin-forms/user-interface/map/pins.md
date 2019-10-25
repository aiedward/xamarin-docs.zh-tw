---
title: Xamarin. 表單地圖釘選
description: 本文說明如何在 Xamarin. 表單地圖上建立 pin。
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
ms.openlocfilehash: a2fb0ba2036dfe34e85c7bebab6ecb55cd868ad5
ms.sourcegitcommit: 5c22097bed2a8d51ecaf6ca197bf4d449dfe1377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72810533"
---
# <a name="xamarinforms-map-pins"></a>Xamarin. 表單地圖釘選

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[`Map`](xref:Xamarin.Forms.Maps.Map)控制項的 Xamarin 可以讓位置以[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件標記。 `Pin` 是一種地圖示記，會在攻絲時開啟資訊視窗：

[![在 iOS 和 Android 上的地圖釘選和其資訊視窗的螢幕擷取畫面](pins-images/pin-and-information-window.png "地圖釘選與資訊視窗")](pins-images/pin-and-information-window-large.png#lightbox "地圖釘選與資訊視窗")

當[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件加入至[`Map.Pins`](xref:Xamarin.Forms.Maps.Pin)集合時，會在地圖上轉譯該釘選。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別具有下列屬性：

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)，屬於 `string`類型，通常代表 pin 位置的位址。 不過，它可以是任何 `string` 內容，而不只是位址。
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)，屬於 `string`類型，通常代表釘選標題。
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)，屬於[`Position`](xref:Xamarin.Forms.Maps.Position)類型，表示釘選的緯度和經度。
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)，屬於[`PinType`](xref:Xamarin.Forms.Maps.PinType)類型，表示 pin 類型。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，`Pin` 可以是資料系結的目標。 如需資料系結 `Pin` 物件的詳細資訊，請參閱[顯示 pin 集合](#display-a-pin-collection)。

此外， [`Pin`](xref:Xamarin.Forms.Maps.Pin)類別會定義 `MarkerClicked` 和 `InfoWindowClicked` 事件。 當按下釘選時，就會引發 `MarkerClicked` 事件，而 `InfoWindowClicked` 事件則會在使用資訊視窗時引發。 伴隨兩個事件的 `PinClickedEventArgs` 物件都有 `bool`類型的單一 `HideInfoWindow` 屬性。

## <a name="display-a-pin"></a>顯示 pin

[`Pin`](xref:Xamarin.Forms.Maps.Pin)可以新增至 XAML 中的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map x:Name="map"
               IsShowingUser="True"
               MoveToLastRegionOnLayoutChange="False"
               HeightRequest="100"                  
               WidthRequest="960"
               VerticalOptions="FillAndExpand">
         <x:Arguments>
             <maps:MapSpan>
                 <x:Arguments>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     <x:Double>0.01</x:Double>
                     <x:Double>0.01</x:Double>
                 </x:Arguments>
             </maps:MapSpan>
         </x:Arguments>
         <maps:Map.Pins>
             <maps:Pin Label="Santa Cruz"
                       Address="The city with a boardwalk"
                       Type="Place">
                 <maps:Pin.Position>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                 </maps:Pin.Position>
             </maps:Pin>
         </maps:Map.Pins>
     </maps:Map>
</ContentPage>
```

此 XAML 會建立[`Map`](xref:Xamarin.Forms.Maps.Map)物件，以顯示[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)物件所指定的區域。 `MapSpan` 物件是以[`Position`](xref:Xamarin.Forms.Maps.Position)物件所代表的緯度和經度為中心，這會擴充0.01 緯度和經度度。 [`Pin`](xref:Xamarin.Forms.Maps.Pin)物件會加入至[`Map.Pins`](xref:Xamarin.Forms.Maps.Pin)集合，並在 `Map` 的[`Position`](xref:Xamarin.Forms.Maps.Pin.Position)屬性所指定的位置上繪製。 如需將 XAML 中的引數傳遞給缺少預設的函式之物件的詳細資訊，請參閱[在 xaml 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)

> [!NOTE]
> [`Position`](xref:Xamarin.Forms.Maps.Position)結構會定義唯讀[`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)和[`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)屬性，這兩種類型都 `double`。 透過其「函式」建立 `Position` 物件時，會在-90.0 和90.0 之間壓制緯度值，而經度值將會壓制到-180.0 和180.0 之間。

對等的 C# 程式碼為：

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
Pin pin = new Pin
{
  Label = "Santa Cruz",
  Address = "The city with a boardwalk",
  Type = PinType.Place,
  Position = new Position(36.9628066, -122.0194722)
};
map.Pins.Add(pin);
```

> [!WARNING]
> 當[`Pin`](xref:Xamarin.Forms.Maps.Pin)新增至[`Map`](xref:Xamarin.Forms.Maps.Map)時，無法設定[`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label)屬性會導致 `ArgumentException` 擲回。

此範例程式碼會產生一個在地圖上轉譯的單一圖釘：

[![地圖釘選在 iOS 和 Android 上的螢幕擷取畫面](pins-images/pin-only.png "地圖釘選")](pins-images/pin-only-large.png#lightbox "地圖釘選")

## <a name="interact-with-a-pin"></a>與 pin 互動

根據預設，當[`Pin`](xref:Xamarin.Forms.Maps.Pin)時，會顯示其資訊視窗：

[![在 iOS 和 Android 上的地圖釘選和其資訊視窗的螢幕擷取畫面](pins-images/pin-and-information-window.png "地圖釘選與資訊視窗")](pins-images/pin-and-information-window-large.png#lightbox "地圖釘選與資訊視窗")

在地圖上的其他地方，會關閉 [資訊] 視窗。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別會定義 `MarkerClicked` 事件，這會在按下 `Pin` 時引發。 不需要處理此事件，即可顯示 [資訊] 視窗。 相反地，只有在需要通知特定的 pin 已被按下時，才會處理這個事件。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別也會定義在使用資訊視窗時所引發的 `InfoWindowClicked` 事件。 當需要通知特定資訊視窗已按下時，應該處理這個事件。

下列程式碼顯示處理這些事件的範例：

```csharp
using Xamarin.Forms.Maps;
// ...
Pin boardwalkPin = new Pin
{
    Position = new Position(36.9641949, -122.0177232),
    Label = "Boardwalk",
    Address = "Santa Cruz",
    Type = PinType.Place
};
boardwalkPin.MarkerClicked += async (s, args) =>
{
    args.HideInfoWindow = true;
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
};

Pin wharfPin = new Pin
{
    Position = new Position(36.9571571, -122.0173544),
    Label = "Wharf",
    Address = "Santa Cruz",
    Type = PinType.Place
};
wharfPin.InfoWindowClicked += async (s, args) =>
{
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
};
```

伴隨兩個事件的 `PinClickedEventArgs` 物件都有 `bool`類型的單一 `HideInfoWindow` 屬性。 當此屬性設定為事件處理常式內的 `true` 時，將會隱藏資訊視窗。

## <a name="pin-types"></a>Pin 類型

[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件包括[`Type`](xref:Xamarin.Forms.Maps.Pin.Type)屬性，其類型為[`PinType`](xref:Xamarin.Forms.Maps.PinType)，表示 pin 類型。 `PinType` 列舉會定義下列成員：

- `Generic`，代表泛型的 pin。
- `Place`，代表某個位置的 pin。
- `SavedPin`，代表儲存位置的 pin。
- `SearchResult`，代表搜尋結果的 pin。

不過，將[`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type)屬性設定為任何[`PinType`](xref:Xamarin.Forms.Maps.PinType)成員並不會變更轉譯之釘選的外觀。 相反地，您必須建立自訂轉譯器來自訂釘選外觀。 如需詳細資訊，請參閱[自訂地圖釘](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)選。

## <a name="display-a-pin-collection"></a>顯示 pin 集合

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義下列屬性：

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)，屬於 `IEnumerable`類型，指定要顯示 `IEnumerable` 專案的集合。
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)，屬於[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)類型，它會指定要套用至所顯示專案集合中每個專案的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。
- `ItemTemplateSelector`，屬於[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)類型，它會指定在執行時間用來選擇專案[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。

> [!IMPORTANT]
> 當同時設定 `ItemTemplate` 和 `ItemTemplateSelector` 屬性時， [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)屬性會優先使用。

您可以使用資料系結將其[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)屬性系結至 `IEnumerable` 集合，以使用釘選來填入[`Map`](xref:Xamarin.Forms.Maps.Map) ：

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

[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)的屬性資料會系結至已連線 viewmodel 的 `Locations` 屬性，它會傳回 `Location` 物件的 `ObservableCollection`，也就是自訂類型。 每個 `Location` 物件會定義 `string` 類型的 `Address` 和 `Description` 屬性，[以及 `Position` 類型](xref:Xamarin.Forms.Maps.Position)的 `Position` 屬性。

`IEnumerable` 集合中每個專案的外觀都是藉由將[`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)屬性設定為包含[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)來定義，而資料會系結至適當的屬性。

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

`MapItemTemplateSelector` 類別會定義設定為不同資料範本的 `DefaultTemplate` 和 `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)屬性。 `OnSelectTemplate` 方法會傳回 `XamarinTemplate`，當您按下 `Pin` 時，會將 "Xamarin" 顯示為標籤，而當專案具有包含「三藩市」的位址時。 當專案沒有包含「三藩市」的位址時，`OnSelectTemplate` 方法會傳回 `DefaultTemplate`。

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
- [建立 Xamarin 表單 DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

---
title: Xamarin.Forms 地圖釘選
description: 本文說明如何在地圖上建立釘選 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 476ef1b8fd6c8e16c5fb3e5fdd1ac60eafd90b7c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559840"
---
# <a name="no-locxamarinforms-map-pins"></a>Xamarin.Forms 地圖釘選

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Xamarin.Forms [`Map`](xref:Xamarin.Forms.Maps.Map) 控制項允許以物件標記位置 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 。 `Pin`是地圖示記，會在點擊時開啟資訊視窗：

[![IOS 和 Android 上地圖釘選及其資訊視窗的螢幕擷取畫面](pins-images/pin-and-information-window.png "使用資訊視窗地圖釘選")](pins-images/pin-and-information-window-large.png#lightbox "使用資訊視窗地圖釘選")

當 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 物件加入至 [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) 集合時，會在地圖上轉譯該圖釘。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別具有下列屬性：

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)，類型為 `string` ，通常表示 pin 位置的位址。 不過，它可以是任何 `string` 內容，而不只是位址。
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)，類型為 `string` ，通常表示 pin 標題。
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)， [`Position`](xref:Xamarin.Forms.Maps.Position) 代表圖釘的緯度和經度的型別。
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)， [`PinType`](xref:Xamarin.Forms.Maps.PinType) 代表 pin 類型的類型。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示 `Pin` 可以是資料系結的目標。 如需資料系結物件的詳細資訊 `Pin` ，請參閱 [顯示 pin 集合](#display-a-pin-collection)。

此外，類別會 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 定義 `MarkerClicked` 和 `InfoWindowClicked` 事件。 `MarkerClicked`當按下釘選時，就會引發事件，並在 `InfoWindowClicked` 點擊資訊視窗時引發事件。 `PinClickedEventArgs`伴隨兩個事件的物件都有一個 `HideInfoWindow` 屬性，類型為 `bool` 。

## <a name="display-a-pin"></a>顯示 pin

[`Pin`](xref:Xamarin.Forms.Maps.Pin)可以 [`Map`](xref:Xamarin.Forms.Maps.Map) 在 XAML 中加入至：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map x:Name="map"
               IsShowingUser="True"
               MoveToLastRegionOnLayoutChange="False">
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

此 XAML 會建立 [`Map`](xref:Xamarin.Forms.Maps.Map) 物件，以顯示物件所指定的區域 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 。 `MapSpan`物件是以物件所代表的緯度和經度為中心 [`Position`](xref:Xamarin.Forms.Maps.Position) ，它會擴充0.01 的緯度和經度度數。 [`Pin`](xref:Xamarin.Forms.Maps.Pin)物件會加入至 [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) 集合，並在 `Map` 其屬性所指定的位置上繪製 [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) 。 如需結構的詳細資訊 [`Position`](xref:Xamarin.Forms.Maps.Position) ，請參閱 [地圖位置和距離](position-distance.md)。 如需將 XAML 中的引數傳遞給缺少預設中繼資料之物件的詳細資訊，請參閱 [在 xaml 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)。

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
> 若無法設定 [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label) 屬性， `ArgumentException` 當加入至時，將會擲回 [`Pin`](xref:Xamarin.Forms.Maps.Pin) [`Map`](xref:Xamarin.Forms.Maps.Map) 。

此範例程式碼會導致在地圖上呈現單一 pin：

[![螢幕擷取畫面： iOS 和 Android 上的地圖釘選](pins-images/pin-only.png "地圖釘選")](pins-images/pin-only-large.png#lightbox "地圖釘選")

## <a name="interact-with-a-pin"></a>與 pin 互動

依預設，當按下時，會 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 顯示其資訊視窗：

[![IOS 和 Android 上地圖釘選及其資訊視窗的螢幕擷取畫面](pins-images/pin-and-information-window.png "使用資訊視窗地圖釘選")](pins-images/pin-and-information-window-large.png#lightbox "使用資訊視窗地圖釘選")

點擊地圖上的其他位置會關閉資訊視窗。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別會定義 `MarkerClicked` 事件，當按下時，就會引發此事件 `Pin` 。 不需要處理此事件，即可顯示資訊視窗。 相反地，當需要通知有特定的 pin 已被點擊時，應處理此事件。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別也 `InfoWindowClicked` 會定義在資訊視窗被點擊時所引發的事件。 當需要通知有特定資訊視窗被點擊時，應處理此事件。

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

`PinClickedEventArgs`伴隨兩個事件的物件都有一個 `HideInfoWindow` 屬性，類型為 `bool` 。 當這個屬性設定為 `true` 事件處理常式內的時，資訊視窗將會隱藏。

## <a name="pin-types"></a>Pin 類型

[`Pin`](xref:Xamarin.Forms.Maps.Pin) 物件包含 [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) 類型的屬性， [`PinType`](xref:Xamarin.Forms.Maps.PinType) 其代表 pin 類型。 `PinType` 列舉會定義下列成員：

- `Generic`表示泛型圖釘。
- `Place`代表地點的釘選。
- `SavedPin`，代表儲存位置的 pin。
- `SearchResult`代表搜尋結果的 pin。

但是，將 [`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type) 屬性設為任何 [`PinType`](xref:Xamarin.Forms.Maps.PinType) 成員，並不會變更轉譯的釘選外觀。 相反地，您必須建立自訂轉譯器，以自訂 pin 外觀。 如需詳細資訊，請參閱 [自訂地圖釘](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)選。

## <a name="display-a-pin-collection"></a>顯示 PIN 集合

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義下列屬性：

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)，類型為 `IEnumerable` ，指定 `IEnumerable` 要顯示之專案的集合。
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)，類型為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，指定要套用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 至所顯示專案集合中的每個專案。
- `ItemTemplateSelector`，類型為 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ，指定 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 將用來 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 在執行時間為專案選擇的。

> [!IMPORTANT]
> [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)當 `ItemTemplate` 設定和屬性時，屬性會優先使用 `ItemTemplateSelector` 。

您 [`Map`](xref:Xamarin.Forms.Maps.Map) 可以使用資料系結，將其屬性系結至集合，以填入 pin [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) `IEnumerable` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
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

[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)屬性資料系結至 `Locations` 連接 viewmodel 的屬性，它 `ObservableCollection` 會傳回 `Location` 物件的，也就是自訂型別。 每個物件都會定義類型的 `Location` `Address` 和屬性，以及 `Description` `string` `Position` 類型的屬性 [`Position`](xref:Xamarin.Forms.Maps.Position) 。

集合中每個專案的外觀 `IEnumerable` 是藉由將屬性設定 [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) 為，其 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 包含資料系結 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 至適當屬性的物件所定義。

下列螢幕擷取畫面顯示 [`Map`](xref:Xamarin.Forms.Maps.Map) [`Pin`](xref:Xamarin.Forms.Maps.Pin) 使用資料系結來顯示集合的：

[![螢幕擷取畫面： iOS 和 Android 上的地圖與資料系結釘選](pins-images/pins-itemsource.png "地圖與資料系結釘選")](pins-images/pins-itemsource-large.png#lightbox "地圖與資料系結釘選")

### <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉 `IEnumerable` 由將屬性設定為，即可在執行時間根據專案值選擇集合中每個專案的外觀 `ItemTemplateSelector` [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ：

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
                    <!-- Change the property values, or the properties that are bound to. -->
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

`MapItemTemplateSelector`類別會定義 `DefaultTemplate` `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 設定為不同資料範本的和屬性。 `OnSelectTemplate` `XamarinTemplate` `Pin` 當專案具有包含 "三藩市" 的位址時，方法會傳回，以在點擊時顯示 "Xamarin" 作為標籤。 當專案沒有包含 "三藩市" 的位址時，方法會傳回 `OnSelectTemplate` `DefaultTemplate` 。

> [!NOTE]
> 這項功能的使用案例是根據子類型，將子歸類物件的屬性系結 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 至不同的屬性 `Pin` 。

如需資料範本選取器的詳細資訊，請參閱 [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [Maps 範例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
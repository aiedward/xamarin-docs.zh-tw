---
title: Xamarin.Forms地圖釘選
description: 本文說明如何在地圖上建立 pin Xamarin.Forms 。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e22888291a430863b8e45ee21d359a5acec750f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138433"
---
# <a name="xamarinforms-map-pins"></a>Xamarin.Forms地圖釘選

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Xamarin.Forms [`Map`](xref:Xamarin.Forms.Maps.Map) 控制項允許以物件標記位置 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 。 `Pin`是一種地圖示記，會在點擊時開啟資訊視窗：

[![在 iOS 和 Android 上的地圖釘選和其資訊視窗的螢幕擷取畫面](pins-images/pin-and-information-window.png "地圖釘選與資訊視窗")](pins-images/pin-and-information-window-large.png#lightbox "地圖釘選與資訊視窗")

將 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 物件新增至 [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) 集合時，會在地圖上轉譯該釘選。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別具有下列屬性：

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)，屬於類型 `string` ，通常代表釘選位置的位址。 不過，它可以是任何 `string` 內容，而不只是位址。
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)，屬於類型 `string` ，通常代表釘選標題。
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)，屬於類型 [`Position`](xref:Xamarin.Forms.Maps.Position) ，表示釘選的緯度和經度。
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)，屬於類型 [`PinType`](xref:Xamarin.Forms.Maps.PinType) ，表示 pin 類型。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示 `Pin` 可以是資料系結的目標。 如需資料系結物件的詳細資訊 `Pin` ，請參閱[顯示 pin 集合](#display-a-pin-collection)。

此外，類別會 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 定義 `MarkerClicked` 和 `InfoWindowClicked` 事件。 `MarkerClicked`當按下釘選時，就會引發事件，而 `InfoWindowClicked` 當您點擊資訊視窗時，就會引發事件。 `PinClickedEventArgs`伴隨兩個事件的物件都具有單一 `HideInfoWindow` 屬性，其類型為 `bool` 。

## <a name="display-a-pin"></a>顯示 pin

[`Pin`](xref:Xamarin.Forms.Maps.Pin)可以 [`Map`](xref:Xamarin.Forms.Maps.Map) 在 XAML 中新增至：

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

此 XAML 會建立 [`Map`](xref:Xamarin.Forms.Maps.Map) 物件，以顯示物件所指定的區域 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) 。 `MapSpan`物件是以物件所代表的緯度和經度為中心 [`Position`](xref:Xamarin.Forms.Maps.Position) ，它會擴充0.01 緯度和經度度。 [`Pin`](xref:Xamarin.Forms.Maps.Pin)物件會加入至 [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) 集合，並在 `Map` 其屬性所指定的位置上繪製 [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) 。 如需結構的詳細資訊 [`Position`](xref:Xamarin.Forms.Maps.Position) ，請參閱[地圖位置和距離](position-distance.md)。 如需將 XAML 中的引數傳遞給缺少預設的函式之物件的詳細資訊，請參閱[在 xaml 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)

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
> 當加入時，無法設定 [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label) 屬性會導致擲回 `ArgumentException` [`Pin`](xref:Xamarin.Forms.Maps.Pin) [`Map`](xref:Xamarin.Forms.Maps.Map) 。

此範例程式碼會產生一個在地圖上轉譯的單一圖釘：

[![地圖釘選在 iOS 和 Android 上的螢幕擷取畫面](pins-images/pin-only.png "地圖釘選")](pins-images/pin-only-large.png#lightbox "地圖釘選")

## <a name="interact-with-a-pin"></a>與 pin 互動

根據預設，當按下時，會 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 顯示其資訊視窗：

[![在 iOS 和 Android 上的地圖釘選和其資訊視窗的螢幕擷取畫面](pins-images/pin-and-information-window.png "地圖釘選與資訊視窗")](pins-images/pin-and-information-window-large.png#lightbox "地圖釘選與資訊視窗")

在地圖上的其他地方，會關閉 [資訊] 視窗。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別 `MarkerClicked` 會定義事件，這會在按下時引發 `Pin` 。 不需要處理此事件，即可顯示 [資訊] 視窗。 相反地，當需要通知特定的 pin 時，就應該處理這個事件。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別也 `InfoWindowClicked` 會定義在使用資訊視窗時所引發的事件。 當需要通知特定資訊視窗已按下時，應該處理這個事件。

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

`PinClickedEventArgs`伴隨兩個事件的物件都具有單一 `HideInfoWindow` 屬性，其類型為 `bool` 。 當這個屬性在 `true` 事件處理常式內設定為時，資訊視窗將會隱藏。

## <a name="pin-types"></a>Pin 類型

[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件包含型別為的 [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) 屬性 [`PinType`](xref:Xamarin.Forms.Maps.PinType) ，表示 pin 型別。 `PinType` 列舉會定義下列成員：

- `Generic`代表泛型 pin。
- `Place`，代表地點的 pin。
- `SavedPin`，代表儲存位置的 pin。
- `SearchResult`代表搜尋結果的 pin。

不過，將 [`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type) 屬性設定為任何 [`PinType`](xref:Xamarin.Forms.Maps.PinType) 成員並不會變更呈現之釘選的外觀。 相反地，您必須建立自訂轉譯器來自訂釘選外觀。 如需詳細資訊，請參閱[自訂地圖釘](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)選。

## <a name="display-a-pin-collection"></a>顯示 PIN 集合

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義下列屬性：

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)，屬於類型 `IEnumerable` ，指定 `IEnumerable` 要顯示的專案集合。
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)，屬於類型 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，指定要套用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 至所顯示專案集合中的每個專案。
- `ItemTemplateSelector`，屬於型別 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ，它 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 會指定將用來 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 在執行時間為專案選擇的。

> [!IMPORTANT]
> [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)當同時 `ItemTemplate` 設定和屬性時，會優先使用屬性 `ItemTemplateSelector` 。

可以使用資料系結將其屬性系結至集合，以 [`Map`](xref:Xamarin.Forms.Maps.Map) 填入 pin [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) `IEnumerable` ：

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

[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)屬性資料會系結至 `Locations` 已連接之 viewmodel 的屬性，它 `ObservableCollection` 會傳回 `Location` 物件的，這是自訂類型。 每個物件都會定義類型的 `Location` `Address` 和 `Description` 屬性， `string` 以及 `Position` 類型的屬性 [`Position`](xref:Xamarin.Forms.Maps.Position) 。

集合中每個專案的外觀 `IEnumerable` 定義方式是將屬性設定 [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) 為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，其中包含資料系結 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 至適當屬性的物件。

下列螢幕擷取畫面顯示 [`Map`](xref:Xamarin.Forms.Maps.Map) 使用資料系結顯示 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 集合：

[![IOS 和 Android 上具有資料系結圖釘的對應螢幕擷取畫面](pins-images/pins-itemsource.png "具有資料系結圖釘的對應")](pins-images/pins-itemsource-large.png#lightbox "具有資料系結圖釘的對應")

### <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉 `IEnumerable` 由將屬性設定為，您可以在執行時間根據專案值選擇集合中每個專案的外觀 `ItemTemplateSelector` [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ：

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

`MapItemTemplateSelector`類別會定義 `DefaultTemplate` `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 設定為不同資料範本的和屬性。 `OnSelectTemplate` `XamarinTemplate` 當專案具有包含「三藩市」的位址時，此方法會傳回，當按下時，會將 "Xamarin" 顯示為標籤 `Pin` 。 當專案沒有包含「三藩市」的位址時，此方法會傳回 `OnSelectTemplate` `DefaultTemplate` 。

> [!NOTE]
> 此功能的使用案例是根據子類型，將子歸類物件的屬性系結 [`Pin`](xref:Xamarin.Forms.Maps.Pin) 至不同的屬性 `Pin` 。

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

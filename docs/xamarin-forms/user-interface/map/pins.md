---
title: Xamarin. 表單地圖釘選
description: 本文說明如何在 Xamarin. 表單地圖上建立 pin。
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
ms.openlocfilehash: 3df78a7c8eaf12306ade182f134f8d294d203af5
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517590"
---
# <a name="xamarinforms-map-pins"></a>Xamarin. 表單地圖釘選

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[Xamarin] [`Map`](xref:Xamarin.Forms.Maps.Map)控制項允許以[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件標記位置。 `Pin`是一種地圖示記，會在點擊時開啟資訊視窗：

[![在 iOS 和 Android 上的地圖釘選和其資訊視窗的螢幕擷取畫面](pins-images/pin-and-information-window.png "地圖釘選與資訊視窗")](pins-images/pin-and-information-window-large.png#lightbox "地圖釘選與資訊視窗")

將[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件新增至[`Map.Pins`](xref:Xamarin.Forms.Maps.Pin)集合時，會在地圖上轉譯該釘選。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別具有下列屬性：

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)，屬於類型`string`，通常代表釘選位置的位址。 不過，它可以是任何`string`內容，而不只是位址。
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)，屬於類型`string`，通常代表釘選標題。
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)，屬於類型[`Position`](xref:Xamarin.Forms.Maps.Position)，表示釘選的緯度和經度。
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)，屬於類型[`PinType`](xref:Xamarin.Forms.Maps.PinType)，表示 pin 類型。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示`Pin`可以是資料系結的目標。 如需資料系結`Pin`物件的詳細資訊，請參閱[顯示 pin 集合](#display-a-pin-collection)。

此外， [`Pin`](xref:Xamarin.Forms.Maps.Pin)類別會定義`MarkerClicked`和`InfoWindowClicked`事件。 當`MarkerClicked`按下釘選時，就會引發事件， `InfoWindowClicked`而當您點擊資訊視窗時，就會引發事件。 伴隨`PinClickedEventArgs`兩個事件的物件都具有單一`HideInfoWindow`屬性，其類型`bool`為。

## <a name="display-a-pin"></a>顯示 pin

[`Pin`](xref:Xamarin.Forms.Maps.Pin)可以[`Map`](xref:Xamarin.Forms.Maps.Map)在 XAML 中新增至：

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

此 XAML 會建立[`Map`](xref:Xamarin.Forms.Maps.Map)物件，以顯示[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)物件所指定的區域。 `MapSpan`物件是以[`Position`](xref:Xamarin.Forms.Maps.Position)物件所代表的緯度和經度為中心，它會擴充0.01 緯度和經度度。 [`Pin`](xref:Xamarin.Forms.Maps.Pin)物件會加入至[`Map.Pins`](xref:Xamarin.Forms.Maps.Pin)集合，並在其[`Position`](xref:Xamarin.Forms.Maps.Pin.Position)屬性所指定`Map`的位置上繪製。 如需[`Position`](xref:Xamarin.Forms.Maps.Position)結構的詳細資訊，請參閱[地圖位置和距離](position-distance.md)。 如需將 XAML 中的引數傳遞給缺少預設的函式之物件的詳細資訊，請參閱[在 xaml 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)

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
> 當[`Pin`](xref:Xamarin.Forms.Maps.Pin)加入時， [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label)無法設定屬性會導致`ArgumentException`擲回。 [`Map`](xref:Xamarin.Forms.Maps.Map)

此範例程式碼會產生一個在地圖上轉譯的單一圖釘：

[![地圖釘選在 iOS 和 Android 上的螢幕擷取畫面](pins-images/pin-only.png "地圖釘選")](pins-images/pin-only-large.png#lightbox "地圖釘選")

## <a name="interact-with-a-pin"></a>與 pin 互動

根據預設，當按[`Pin`](xref:Xamarin.Forms.Maps.Pin)下時，會顯示其資訊視窗：

[![在 iOS 和 Android 上的地圖釘選和其資訊視窗的螢幕擷取畫面](pins-images/pin-and-information-window.png "地圖釘選與資訊視窗")](pins-images/pin-and-information-window-large.png#lightbox "地圖釘選與資訊視窗")

在地圖上的其他地方，會關閉 [資訊] 視窗。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別會定義`MarkerClicked`事件，這會在按下時`Pin`引發。 不需要處理此事件，即可顯示 [資訊] 視窗。 相反地，當需要通知特定的 pin 時，就應該處理這個事件。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)類別也會定義在`InfoWindowClicked`使用資訊視窗時所引發的事件。 當需要通知特定資訊視窗已按下時，應該處理這個事件。

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

伴隨`PinClickedEventArgs`兩個事件的物件都具有單一`HideInfoWindow`屬性，其類型`bool`為。 當這個屬性在事件處理`true`程式內設定為時，資訊視窗將會隱藏。

## <a name="pin-types"></a>Pin 類型

[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件包含型[`Type`](xref:Xamarin.Forms.Maps.Pin.Type) [`PinType`](xref:Xamarin.Forms.Maps.PinType)別為的屬性，表示 pin 型別。 `PinType` 列舉會定義下列成員：

- `Generic`代表泛型 pin。
- `Place`，代表地點的 pin。
- `SavedPin`，代表儲存位置的 pin。
- `SearchResult`代表搜尋結果的 pin。

不過，將[`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type)屬性設定為任何[`PinType`](xref:Xamarin.Forms.Maps.PinType)成員並不會變更呈現之釘選的外觀。 相反地，您必須建立自訂轉譯器來自訂釘選外觀。 如需詳細資訊，請參閱[自訂地圖釘](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)選。

## <a name="display-a-pin-collection"></a>顯示 PIN 集合

[`Map`](xref:Xamarin.Forms.Maps.Map)類別會定義下列屬性：

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)，屬於類型`IEnumerable`，指定要顯示的`IEnumerable`專案集合。
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)，屬於類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，指定[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)要套用至所顯示專案集合中的每個專案。
- `ItemTemplateSelector`，屬於型[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)別，它會[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)指定將用來在執行時間[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)為專案選擇的。

> [!IMPORTANT]
> 當[`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)同時設定`ItemTemplate`和`ItemTemplateSelector`屬性時，會優先使用屬性。

[`Map`](xref:Xamarin.Forms.Maps.Map)可以使用資料系結將其[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)屬性系結至`IEnumerable`集合，以填入 pin：

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

[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)屬性資料會系結至`Locations`已連接之 viewmodel 的屬性，它會`ObservableCollection`傳回`Location`物件的，這是自訂類型。 每`Location`個物件`Address`都會`Description`定義類型的和`string`屬性，以及`Position`類型[`Position`](xref:Xamarin.Forms.Maps.Position)的屬性。

集合中每個專案的外觀定義方式是將[`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)屬性設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，其中包含資料系[`Pin`](xref:Xamarin.Forms.Maps.Pin)結至適當屬性的物件。 `IEnumerable`

下列螢幕擷取畫面顯示使用[`Map`](xref:Xamarin.Forms.Maps.Map)資料系[`Pin`](xref:Xamarin.Forms.Maps.Pin)結顯示集合：

[![IOS 和 Android 上具有資料系結圖釘的對應螢幕擷取畫面](pins-images/pins-itemsource.png "具有資料系結圖釘的對應")](pins-images/pins-itemsource-large.png#lightbox "具有資料系結圖釘的對應")

### <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉由將`IEnumerable` `ItemTemplateSelector`屬性設定為，您可以在執行時間根據專案值選擇集合中每個專案的外觀[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)：

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

下列範例顯示`MapItemTemplateSelector`類別：

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

`MapItemTemplateSelector`類別會定義`DefaultTemplate`設定`XamarinTemplate`為不同資料範本的和[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)屬性。 當`OnSelectTemplate`專案具有包含`XamarinTemplate`「三藩市」的位址時，此方法`Pin`會傳回，當按下時，會將 "Xamarin" 顯示為標籤。 當專案沒有包含「三藩市」的位址時，此`OnSelectTemplate`方法會傳回。 `DefaultTemplate`

> [!NOTE]
> 此功能的使用案例是根據子類型，將子歸類[`Pin`](xref:Xamarin.Forms.Maps.Pin)物件的屬性系結至不同的`Pin`屬性。

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [對應自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [在 XAML 中傳遞引數](~/xamarin-forms/xaml/passing-arguments.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)

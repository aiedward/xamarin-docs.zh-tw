---
title: 自訂地圖釘選
description: 這篇文章會示範如何建立自訂轉譯器地圖控制項，可顯示每個平台使用自訂的 pin 碼和自訂的檢視釘選資料的原生對應。
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 351119a8b0089f78d4ce98729a1516c3cd7bae7b
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203081"
---
# <a name="customizing-a-map-pin"></a>自訂地圖釘選

_這篇文章會示範如何建立自訂轉譯器地圖控制項，可顯示每個平台使用自訂的 pin 碼和自訂的檢視釘選資料的原生對應。_

Xamarin.Forms 中的每個檢視都有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `Map` ](xref:Xamarin.Forms.Maps.Map) Xamarin.Forms 應用程式在 iOS 中，呈現`MapRenderer`類別具現化，以依序具現化原生`MKMapView`控制項。 Android 平台上，`MapRenderer`類別會具現化原生`MapView`控制項。 在通用 Windows 平台 (UWP)，`MapRenderer`類別會具現化原生`MapControl`。 如需有關轉譯器和 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱 <<c0> [ 轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `Map` ](xref:Xamarin.Forms.Maps.Map)和對應的原生控制項實作它：

![](customized-pin-images/map-classes.png "地圖控制項和實作的原生控制項之間的關聯性")

轉譯程序可以用來建立自訂轉譯器來實作平台專屬的自訂[ `Map` ](xref:Xamarin.Forms.Maps.Map)每個平台。 執行此動作的程序如下所示：

1. [建立](#Creating_the_Custom_Map)Xamarin.Forms 自訂地圖。
1. [取用](#Consuming_the_Custom_Map)Xamarin.Forms 自訂對應。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)每個平台上的地圖的自訂轉譯器。

每個項目會現在依次討論實作`CustomMap`會原生的對應自訂的 pin 與 pin 資料的自訂的檢視顯示每個平台的轉譯器。

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) 必須初始化，並使用之前設定。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map.md)。

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>建立自訂的對應

自訂地圖控制項可以由子類別化[ `Map` ](xref:Xamarin.Forms.Maps.Map)類別，如下列程式碼範例所示：

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

`CustomMap`控制項建立.NET Standard 程式庫專案中，並定義自訂對應的 API。 自訂地圖會公開`CustomPins`所表示的集合屬性`CustomPin`轉譯的每個平台的原生的地圖控制項的物件。 `CustomPin`類別以下列程式碼範例所示：

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

這個類別會定義`CustomPin`做為繼承的屬性[ `Pin` ](xref:Xamarin.Forms.Maps.Pin)類別，並新增`Url`屬性。

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>使用自訂地圖

`CustomMap`控制項可以在 XAML 中參考.NET Standard 程式庫專案中，藉由宣告其位置的命名空間，並使用自訂地圖控制項上的命名空間前置詞。 下列程式碼範例示範如何`CustomMap`控制項可供 XAML 頁面：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <ContentPage.Content>
        <local:CustomMap x:Name="myMap" MapType="Street"
          WidthRequest="{x:Static local:App.ScreenWidth}"
          HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

`local`命名空間前置詞可以命名為任何項目。 不過，`clr-namespace`和`assembly`值必須符合自訂地圖的詳細資料。 一旦宣告命名空間，前置詞用來參考自訂對應。

下列程式碼範例示範如何`CustomMap`控制項可供 C# 頁面：

```csharp
public class MapPageCS : ContentPage
{
  public MapPageCS ()
  {
    var customMap = new CustomMap {
      MapType = MapType.Street,
      WidthRequest = App.ScreenWidth,
      HeightRequest = App.ScreenHeight
    };
    ...

    Content = customMap;
  }
}
```

`CustomMap`執行個體將會用來顯示每個平台的原生的對應。 它有[ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType)屬性設定的顯示樣式[ `Map` ](xref:Xamarin.Forms.Maps.Map)，包含可能的值所定義[ `MapType` ](xref:Xamarin.Forms.Maps.MapType)列舉型別。 適用於 iOS 和 Android，對應的高度與寬度透過屬性設定`App`平台專屬專案中初始化的類別。

地圖和 pin 的位置就包含，會初始化，如下列程式碼範例所示：

```csharp
public MapPage ()
{
  ...
  var pin = new CustomPin {
    Type = PinType.Place,
    Position = new Position (37.79752, -122.40183),
    Label = "Xamarin San Francisco Office",
    Address = "394 Pacific Ave, San Francisco CA",
    Id = "Xamarin",
    Url = "http://xamarin.com/about/"
  };

  customMap.CustomPins = new List<CustomPin> { pin };
  customMap.Pins.Add (pin);
  customMap.MoveToRegion (MapSpan.FromCenterAndRadius (
    new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
}
```

這項初始化加入自訂的 pin，並將置於具有地圖的檢視[ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*)方法，藉由建立變更的位置和地圖的縮放層級[ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) 從[`Position` ](xref:Xamarin.Forms.Maps.Position)並[ `Distance` ](xref:Xamarin.Forms.Maps.Distance)。

自訂轉譯器現在可以新增至每個應用程式專案，以自訂原生的對應控制項。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`MapRenderer`呈現自訂對應的類別。
1. 覆寫`OnElementChanged`呈現的自訂地圖和撰寫邏輯來自訂它的方法。 建立對應的 Xamarin.Forms 自訂地圖時，會呼叫這個方法。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 自訂地圖的自訂轉譯器類別。 這個屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 它是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，則會使用預設的轉譯器控制項的基底類別。

下圖說明範例應用程式，以及其間的關聯性中的每個專案的責任：

![](customized-pin-images/solution-structure.png "CustomMap 自訂轉譯器專案責任")

`CustomMap`平台特定的轉譯器類別，衍生自所要呈現控制項`MapRenderer`每個平台的類別。 這會導致每個`CustomMap`控制項所呈現的平台特定控制項，如下列螢幕擷取畫面所示：

![](customized-pin-images/screenshots.png "每個平台的 CustomMap")

`MapRenderer`類別會公開`OnElementChanged`方法，這個方法會建立 Xamarin.Forms 自訂對應以呈現對應的原生控制項時呼叫。 這個方法會採用`ElementChangedEventArgs`參數，其中包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加至，和 Xamarin.Forms 的項目，轉譯器*是*附加分別。 在範例應用程式`OldElement`屬性會是`null`並`NewElement`屬性會包含參考`CustomMap`執行個體。

覆寫的新版`OnElementChanged`方法，在每個平台特定的轉譯器類別，可供執行原生控制項自訂。 您可以透過平台上使用原生控制項的型別的參考`Control`屬性。 此外，所呈現的 Xamarin.Forms 控制項的參考可以透過取得`Element`屬性。

訂閱中的事件處理常式時，就必須特別注意`OnElementChanged`方法，如下列程式碼範例所示：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

應該設定原生控制項，並自訂轉譯器會附加至新的 Xamarin.Forms 元素時，才要訂閱事件處理常式。 同樣地，任何已訂閱的事件處理常式，應先取消訂閱只有附加於項目有所變更時。 採用這個方法可協助您建立自訂轉譯器，不會發生記憶體流失。

每個自訂轉譯器類別裝飾了`ExportRenderer`向 Xamarin.Forms 中的轉譯器的屬性。 屬性會採用兩個參數-Xamarin.Forms 自訂控制項所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列各節將討論每個平台專屬的自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

以下的螢幕擷取畫面會顯示地圖上，自訂的前後：

![](customized-pin-images/map-layout-ios.png "自訂前後的地圖控制項")

在 iOS 上稱為釘選*註釋*，而且可以是自訂映像或各種色彩的系統定義的 pin。 可以選擇性地顯示附註*圖說文字*，會顯示在回應使用者選取註釋。 圖說文字會顯示`Label`並`Address`的屬性`Pin`執行個體，具有選擇性的左和右配件的檢視。 左方的配件檢視在上面的螢幕擷取畫面，向右配件的檢視則是 monkey、 映像*資訊* 按鈕。

下列程式碼範例顯示適用於 iOS 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        UIView customPinView;
        List<CustomPin> customPins;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        ...
    }
}
```

`OnElementChanged`方法所執行的下列組態[ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/)執行個體，前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素：

- [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/)屬性設定為`GetViewForAnnotation`方法。 這個方法時，會呼叫[註釋的位置會出現在地圖上](#Displaying_the_Annotation)，並用來自訂顯示註解之前。
- 事件處理常式`CalloutAccessoryControlTapped`， `DidSelectAnnotationView`，和`DidDeselectAnnotationView`註冊事件。 這些事件引發時使用者[點選圖說文字在右邊的配件](#Tapping_on_the_Right_Callout_Accessory_View)，和當使用者[選取](#Selecting_the_Annotation)和[會取消選取](#Deselecting_the_Annotation)註釋，分別。 事件在項目轉譯器附加到變更時，才會取消訂閱。

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>顯示註釋

`GetViewForAnnotation`註釋的位置會出現在地圖上，並用來自訂顯示註解之前時，會呼叫方法。 註解有兩個部分：

- `MkAnnotation` – 包含標題、 子標題和註解的位置。
- `MkAnnotationView` – 包含該映像，來代表註解，以及 （選擇性） 圖說文字會顯示當使用者點選註釋。

`GetViewForAnnotation`方法會接受`IMKAnnotation`會包含註解的資料，並傳回`MKAnnotationView`顯示在地圖上，以及下列的程式碼範例所示：

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null) {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Id.ToString());
    if (annotationView == null) {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Id.ToString());
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Id = customPin.Id.ToString();
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

此方法可確保註釋將顯示為自訂映像，而不是系統定義的 pin，而且為註解點選圖說文字就會顯示包含左側和右側的註解的標題和位址的其他內容. 達成，如下所示：

1. `GetCustomPin`方法呼叫以傳回自訂的釘選資料的註解。
1. 為了節省記憶體，集中重複使用的呼叫註解的檢視[ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/)。
1. `CustomMKAnnotationView`類別會擴充`MKAnnotationView`類別`Id`並`Url`屬性會對應至相同的屬性，在`CustomPin`執行個體。 新執行個體`CustomMKAnnotationView`建立，前提是註解是`null`:
    - `CustomMKAnnotationView.Image`屬性設定為 映像，以代表地圖上的註解。
    - `CustomMKAnnotationView.CalloutOffset`屬性設定為`CGPoint`，指定將會在圖說文字置中，上方的註解。
    - `CustomMKAnnotationView.LeftCalloutAccessoryView`屬性設為註解的標題和位址的左邊會出現 monkey 的映像。
    - `CustomMKAnnotationView.RightCalloutAccessoryView`屬性設定為*資訊*按鈕，將會出現在右邊的註解的標題和地址。
    - `CustomMKAnnotationView.Id`屬性設定為`CustomPin.Id`所傳回的屬性`GetCustomPin`方法。 這可讓識別，使其具有註釋[圖說文字，可以進一步自訂](#Selecting_the_Annotation)視。
    - `CustomMKAnnotationView.Url`屬性設定為`CustomPin.Url`所傳回的屬性`GetCustomPin`方法。 將瀏覽 URL 時使用者[點選按鈕顯示在右邊的圖說文字配件檢視](#Tapping_on_the_Right_Callout_Accessory_View)。
1. [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/)屬性設定為`true`，因此圖說文字會顯示在點選註解時。
1. 註解就會傳回顯示在地圖上。

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>選取 註解

當使用者註解，點選`DidSelectAnnotationView`事件引發時，它會接著執行`OnDidSelectAnnotationView`方法：

```csharp
void OnDidSelectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  customPinView = new UIView ();

  if (customView.Id == "Xamarin") {
    customPinView.Frame = new CGRect (0, 0, 200, 84);
    var image = new UIImageView (new CGRect (0, 0, 200, 84));
    image.Image = UIImage.FromFile ("xamarin.png");
    customPinView.AddSubview (image);
    customPinView.Center = new CGPoint (0, -(e.View.Frame.Height + 75));
    e.View.AddSubview (customPinView);
  }
}
```

此方法擴充現有的註標 （其中包含左邊和右邊配件的檢視），加上`UIView`它包含的 Xamarin 標誌影像，前提是選取的註解的執行個體其`Id`屬性設定為`Xamarin`. 如此一來，不同的圖說文字可能會顯示不同的註解的案例。 `UIView`上方現有的圖說文字置中位置就會顯示執行個體。

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>點選正確圖說文字的附屬應用程式檢視

當使用者點選*資訊*在正確的圖說文字配件的檢視中，按鈕`CalloutAccessoryControlTapped`事件引發時，它會接著執行`OnCalloutAccessoryControlTapped`方法：

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

這個方法會開啟網頁瀏覽器並瀏覽至儲存在的位址`CustomMKAnnotationView.Url`屬性。 請注意，在建立時定義位址`CustomPin`.NET Standard 程式庫專案中的集合。

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>取消註解

當顯示註釋，以及使用者在地圖上，點選`DidDeselectAnnotationView`事件引發時，它會接著執行`OnDidDeselectAnnotationView`方法：

```csharp
void OnDidDeselectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  if (!e.View.Selected) {
    customPinView.RemoveFromSuperview ();
    customPinView.Dispose ();
    customPinView = null;
  }
}
```

這個方法可確保當未選取現有的圖說文字、 註標 （Xamarin 標誌的影像） 的擴充的部分也會停止顯示，並會釋放其資源。

如需有關自訂`MKMapView`執行個體，請參閱 < [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

以下的螢幕擷取畫面會顯示地圖上，自訂的前後：

![](customized-pin-images/map-layout-android.png "自訂前後的地圖控制項")

在 Android 上稱為釘選*標記*，並可以是自訂映像或使用系統定義的標記的各種不同的色彩。 標記可以顯示*資訊視窗*，會顯示在使用者點選在標記上的回應。 [資訊] 視窗會顯示`Label`並`Address`的屬性`Pin`執行個體，並可自訂以包含其他內容。 不過，只有一個 [資訊] 視窗可以顯示一次。

下列程式碼範例會顯示 Android 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.Droid
{
    public class CustomMapRenderer : MapRenderer, GoogleMap.IInfoWindowAdapter
    {
        List<CustomPin> customPins;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                NativeMap.InfoWindowClick -= OnInfoWindowClick;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                customPins = formsMap.CustomPins;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(GoogleMap map)
        {
            base.OnMapReady(map);

            NativeMap.InfoWindowClick += OnInfoWindowClick;
            NativeMap.SetInfoWindowAdapter(this);
        }
        ...
    }
}
```

前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素`OnElementChanged`方法呼叫`MapView.GetMapAsync`方法，以取得基礎`GoogleMap`繫結至檢視。 一次`GoogleMap`執行個體可供使用，`OnMapReady`覆寫會叫用。 這個方法會註冊事件處理常式`InfoWindowClick`時引發的事件[資訊視窗中按一下](#Clicking_on_the_Info_Window)，和項目轉譯器附加到變更時，才會取消。 `OnMapReady`覆寫也呼叫`SetInfoWindowAdapter`方法，以指定的`CustomMapRenderer`類別執行個體將會提供方法以自訂 [資訊] 視窗。

`CustomMapRenderer`類別會實作`GoogleMap.IInfoWindowAdapter`介面[自訂 [資訊] 視窗](#Customizing_the_Info_Window)。 這個介面會指定，必須實作下列方法：

- `public Android.Views.View GetInfoWindow(Marker marker)` – 此方法呼叫以傳回自訂的資訊視窗中，標記。 如果它傳回`null`，則會使用預設視窗呈現。 如果它傳回`View`，然後，`View`將會置於資訊視窗框架。
- `public Android.Views.View GetInfoContents(Marker marker)` – 此方法呼叫以傳回`View`其中包含的內容資訊 視窗中，並將只會呼叫如果`GetInfoWindow`方法會傳回`null`。 如果它傳回`null`，則會使用資訊視窗內容的預設轉譯。

在範例應用程式中，自訂只資訊視窗內容，因此`GetInfoWindow`方法會傳回`null`若要這麼做。

#### <a name="customizing-the-marker"></a>自訂標記

用來表示標記的圖示可以藉由呼叫自訂`MarkerOptions.SetIcon`方法。 這可藉由覆寫`CreateMarker`方法，叫用的每個`Pin`加入至對應：

```csharp
protected override MarkerOptions CreateMarker(Pin pin)
{
    var marker = new MarkerOptions();
    marker.SetPosition(new LatLng(pin.Position.Latitude, pin.Position.Longitude));
    marker.SetTitle(pin.Label);
    marker.SetSnippet(pin.Address);
    marker.SetIcon(BitmapDescriptorFactory.FromResource(Resource.Drawable.pin));
    return marker;
}
```

這個方法會建立新`MarkerOption`針對每個執行個體`Pin`執行個體。 設定位置、 標籤和標記的位址之後, 設定其圖示`SetIcon`方法。 這個方法會採用`BitmapDescriptor`物件，其中包含要呈現的圖示，使用所需的資料`BitmapDescriptorFactory`類別提供 helper 方法，以簡化建立`BitmapDescriptor`。

如需使用詳細資訊`BitmapDescriptorFactory`類別，以自訂的標記，請參閱[自訂標記](~/android/platform/maps-and-location/maps/maps-api.md)。

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>自訂 [資訊] 視窗

當使用者在標記中，點選`GetInfoContents`方法執行，但前提`GetInfoWindow`方法會傳回`null`。 下列程式碼範例示範`GetInfoContents`方法：

```csharp
public Android.Views.View GetInfoContents (Marker marker)
{
  var inflater = Android.App.Application.Context.GetSystemService (Context.LayoutInflaterService) as Android.Views.LayoutInflater;
  if (inflater != null) {
    Android.Views.View view;

    var customPin = GetCustomPin (marker);
    if (customPin == null) {
      throw new Exception ("Custom pin not found");
    }

    if (customPin.Id.ToString() == "Xamarin") {
      view = inflater.Inflate (Resource.Layout.XamarinMapInfoWindow, null);
    } else {
      view = inflater.Inflate (Resource.Layout.MapInfoWindow, null);
    }

    var infoTitle = view.FindViewById<TextView> (Resource.Id.InfoWindowTitle);
    var infoSubtitle = view.FindViewById<TextView> (Resource.Id.InfoWindowSubtitle);

    if (infoTitle != null) {
      infoTitle.Text = marker.Title;
    }
    if (infoSubtitle != null) {
      infoSubtitle.Text = marker.Snippet;
    }

    return view;
  }
  return null;
}
```

這個方法會傳回`View`包含內容的資訊視窗。 達成，如下所示：

- A`LayoutInflater`擷取執行個體。 這用來產生成其對應的版面配置的 XML 檔案`View`。
- `GetCustomPin`方法呼叫以傳回自訂的釘選資料的資訊視窗。
- `XamarinMapInfoWindow`版面配置會擴大，如果`CustomPin.Id`屬性等於`Xamarin`。 否則，`MapInfoWindow`版面配置會擴大。 這可讓案例，其中顯示不同的資訊視窗版面配置，針對不同的標記。
- `InfoWindowTitle`並`InfoWindowSubtitle`在擴大的版面配置中，擷取資源及其`Text`屬性設定為對應的資料，從`Marker`執行個體，能提供的資源不會`null`。
- `View`顯示在地圖上傳回執行個體。

> [!NOTE]
> [資訊] 視窗不是即時`View`。 相反地，Android 會將轉換`View`設為靜態點陣圖和顯示畫面做為映像。 這表示雖然資訊視窗可以回應 click 事件，它無法回應任何觸控事件或筆勢，和 [資訊] 視窗中的個別控制項無法回應到他們自己 click 事件。

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>按一下 [資訊] 視窗

當使用者按一下 [資訊] 視窗中，`InfoWindowClick`事件引發時，它會接著執行`OnInfoWindowClick`方法：

```csharp
void OnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
  var customPin = GetCustomPin (e.Marker);
  if (customPin == null) {
    throw new Exception ("Custom pin not found");
  }

  if (!string.IsNullOrWhiteSpace (customPin.Url)) {
    var url = Android.Net.Uri.Parse (customPin.Url);
    var intent = new Intent (Intent.ActionView, url);
    intent.AddFlags (ActivityFlags.NewTask);
    Android.App.Application.Context.StartActivity (intent);
  }
}
```

這個方法會開啟網頁瀏覽器並瀏覽至儲存在的位址`Url`所擷取的屬性`CustomPin`執行個體`Marker`。 請注意，在建立時定義位址`CustomPin`.NET Standard 程式庫專案中的集合。

如需有關自訂`MapView`執行個體，請參閱 <<c2> [ 地圖服務 API](~/android/platform/maps-and-location/maps/maps-api.md)。

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>通用 Windows 平台上建立自訂轉譯器

以下的螢幕擷取畫面會顯示地圖上，自訂的前後：

![](customized-pin-images/map-layout-uwp.png "自訂前後的地圖控制項")

在 UWP 上稱為釘選*對應圖示*，並可以是自訂映像或系統定莪預設映像。 可以顯示一個 map 圖示`UserControl`，會顯示在回應使用者點選 [地圖] 圖示。 `UserControl`可以顯示任何內容，包括`Label`並`Address`的屬性`Pin`執行個體。

下列程式碼範例示範 UWP 的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        MapControl nativeMap;
        List<CustomPin> customPins;
        XamarinMapOverlay mapOverlay;
        bool xamarinOverlayShown = false;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                nativeMap.MapElementClick -= OnMapElementClick;
                nativeMap.Children.Clear();
                mapOverlay = null;
                nativeMap = null;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                nativeMap = Control as MapControl;
                customPins = formsMap.CustomPins;

                nativeMap.Children.Clear();
                nativeMap.MapElementClick += OnMapElementClick;

                foreach (var pin in customPins)
                {
                    var snPosition = new BasicGeoposition { Latitude = pin.Pin.Position.Latitude, Longitude = pin.Pin.Position.Longitude };
                    var snPoint = new Geopoint(snPosition);

                    var mapIcon = new MapIcon();
                    mapIcon.Image = RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///pin.png"));
                    mapIcon.CollisionBehaviorDesired = MapElementCollisionBehavior.RemainVisible;
                    mapIcon.Location = snPoint;
                    mapIcon.NormalizedAnchorPoint = new Windows.Foundation.Point(0.5, 1.0);

                    nativeMap.MapElements.Add(mapIcon);                    
                }
            }
        }
        ...
    }
}
```

`OnElementChanged`方法會執行下列作業，前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素：

- 它會清除`MapControl.Children`要移除對應中的任何現有的使用者介面項目，再註冊的事件處理常式集合`MapElementClick`事件。 當使用者點選或按一下，就會引發這個事件`MapElement`上`MapControl`，和項目轉譯器附加到變更時，才會取消。
- 每個插針`customPins`集合隨即出現在地圖上正確的地理位置，如下所示：
  - 指針的位置會建立為`Geopoint`執行個體。
  - A`MapIcon`來代表 pin 建立執行個體。
  - 用來代表映像`MapIcon`設定，來指定`MapIcon.Image`屬性。 不過，對應圖示的影像不一律保證會顯示，因為它可能會被遮蔽地圖上的其他項目。 因此，對應圖示`CollisionBehaviorDesired`屬性設定為`MapElementCollisionBehavior.RemainVisible`，以確保它保持可見狀態。
  - 位置`MapIcon`設定，來指定`MapIcon.Location`屬性。
  - `MapIcon.NormalizedAnchorPoint`屬性設定為映像上之指標的概略位置。 如果這個屬性會保留其預設值 (0，0)，代表影像的左上的角，地圖的縮放層級中的變更可能會導致指向不同位置的映像。
  - `MapIcon`執行個體新增至`MapControl.MapElements`集合。 這會導致上顯示的地圖圖示`MapControl`。

> [!NOTE]
> 多個對應的圖示，使用相同的映像時`RandomAccessStreamReference`應該在網頁或應用程式層級宣告執行個體，以達到最佳效能。

#### <a name="displaying-the-usercontrol"></a>顯示使用者控制項

當使用者在 [對應] 圖示，點選`OnMapElementClick`執行方法。 下列程式碼範例示範此方法：

```csharp
private void OnMapElementClick(MapControl sender, MapElementClickEventArgs args)
{
    var mapIcon = args.MapElements.FirstOrDefault(x => x is MapIcon) as MapIcon;
    if (mapIcon != null)
    {
        if (!xamarinOverlayShown)
        {
            var customPin = GetCustomPin(mapIcon.Location.Position);
            if (customPin == null)
            {
                throw new Exception("Custom pin not found");
            }

            if (customPin.Id.ToString() == "Xamarin")
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Pin.Position.Latitude, Longitude = customPin.Pin.Position.Longitude };
                var snPoint = new Geopoint(snPosition);

                nativeMap.Children.Add(mapOverlay);
                MapControl.SetLocation(mapOverlay, snPoint);
                MapControl.SetNormalizedAnchorPoint(mapOverlay, new Windows.Foundation.Point(0.5, 1.0));
                xamarinOverlayShown = true;
            }
        }
        else
        {
            nativeMap.Children.Remove(mapOverlay);
            xamarinOverlayShown = false;
        }
    }
}
```

這個方法會建立`UserControl`顯示 pin 碼的相關資訊的執行個體。 達成，如下所示：

- `MapIcon`擷取執行個體。
- `GetCustomPin`方法呼叫以傳回自訂的釘選資料將會顯示。
- A`XamarinMapOverlay`建立執行個體，以顯示自訂的釘選的資料。 這個類別是一個使用者控制項。
- 要顯示的地理位置`XamarinMapOverlay`執行個體`MapControl`會建立為`Geopoint`執行個體。
- `XamarinMapOverlay`執行個體新增至`MapControl.Children`集合。 此集合包含 XAML 使用者介面項目將會顯示在地圖。
- 地理位置`XamarinMapOverlay`在地圖上的執行個體由呼叫設定`SetLocation`方法。
- 上的相對位置`XamarinMapOverlay`執行個體，對應至指定的位置，由呼叫設定`SetNormalizedAnchorPoint`方法。 這可確保在對應結果中的縮放層級變更`XamarinMapOverlay`執行個體一律顯示在正確的位置。

或者，如果已經在地圖上顯示的 pin 碼的相關資訊，在地圖上的點選移除`XamarinMapOverlay`執行個體`MapControl.Children`集合。

#### <a name="tapping-on-the-information-button"></a>點選 [資訊] 按鈕

當使用者點選*資訊*按鈕`XamarinMapOverlay`使用者控制`Tapped`事件引發時，它會接著執行`OnInfoButtonTapped`方法：

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

這個方法會開啟網頁瀏覽器並瀏覽至儲存在的位址`Url`屬性`CustomPin`執行個體。 請注意，在建立時定義位址`CustomPin`.NET Standard 程式庫專案中的集合。

如需有關自訂`MapControl`執行個體，請參閱 <<c2> [ 地圖與位置概觀](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx)MSDN 上。

## <a name="summary"></a>總結

本文示範如何建立自訂轉譯器`Map`控制項，讓開發人員覆寫預設原生呈現自己的平台特定自訂。 Xamarin.Forms.Maps 提供顯示用於使用者的體驗提供快速且熟悉的地圖上每個平台 Api 的原生地圖的地圖的跨平台抽象概念。


## <a name="related-links"></a>相關連結

- [地圖控制項](~/xamarin-forms/user-interface/map.md)
- [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)
- [Maps API](~/android/platform/maps-and-location/maps/maps-api.md)
- [自訂的 pin 碼 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)

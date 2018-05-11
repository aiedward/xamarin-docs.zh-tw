---
title: 自訂地圖 Pin
description: 本文示範如何建立自訂轉譯器對應控制項，可顯示每個平台的原生的對應自訂的 pin 與 pin 資料的自訂的檢視。
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 04d3d99a5d85dd77c93e9b926e8952cc3d8a771e
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="customizing-a-map-pin"></a>自訂地圖 Pin

_本文示範如何建立自訂轉譯器對應控制項，可顯示每個平台的原生的對應自訂的 pin 與 pin 資料的自訂的檢視。_

Xamarin.Forms 中的每個檢視有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) Xamarin.Forms 應用程式在 iOS 中，呈現`MapRenderer`類別具現化，這又會具現化原生`MKMapView`控制項。 Android 平台上，`MapRenderer`類別具現化的原生`MapView`控制項。 在通用 Windows 平台 (UWP)，`MapRenderer`類別具現化的原生`MapControl`。 如需有關轉譯器，而且 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱[轉譯器的基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)和對應的原生控制項實作它：

![](customized-pin-images/map-classes.png "地圖控制項和實作的原生控制項之間的關聯性")

轉譯程序可用來藉由建立自訂轉譯器的實作特定平台自訂[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)每個平台上。 執行此程序如下所示：

1. [建立](#Creating_the_Custom_Map)Xamarin.Forms 自訂地圖。
1. [取用](#Consuming_the_Custom_Map)Xamarin.Forms 從自訂的地圖。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)自訂轉譯器，為每個平台上的地圖。

每個項目將現在依次討論實作`CustomMap`轉譯器，會顯示每個平台上的自訂的 pin 與 pin 資料的自訂的檢視的原生對應。

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) 必須初始化，並使用之前設定。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map.md)。

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>建立自訂地圖

您可以建立自訂地圖控制項子類別化[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)類別，如下列程式碼範例所示：

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

`CustomMap`控制.NET 標準程式庫專案中建立和定義自訂地圖的 API。 自訂地圖公開`CustomPins`表示的集合屬性`CustomPin`轉譯的每個平台上原生地圖控制項的物件。 `CustomPin`類別以下列程式碼範例所示：

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

這個類別會定義`CustomPin`繼承的屬性[ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/)類別，並加入`Url`屬性。

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>使用自訂地圖

`CustomMap`控制項可以在 XAML 中參考.NET 標準程式庫專案中，宣告的命名空間，做為其位置，並使用自訂地圖控制項上的命名空間前置詞。 下列程式碼範例示範如何`CustomMap`控制項可供 XAML 頁面：

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

`CustomMap`執行個體將會用來顯示每個平台上的原生對應。 它有[ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/)屬性設定的顯示樣式[ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/)，包含可能的值中所定義[ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/)列舉型別。 適用於 iOS 和 Android，對應的高度與寬度會透過內容設定`App`平台專屬專案中初始化的類別。

地圖和 pin 碼的位置它包含，會初始化，如下列程式碼範例所示：

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

這類初始化加入自訂的 pin，並具有地圖的檢視[ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/)方法，以建立變更的位置和地圖的縮放層級[ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/)從[`Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)和[ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/)。

自訂轉譯器現在加入自訂原生地圖控制項的每個應用程式專案。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立的自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`MapRenderer`呈現自訂地圖的類別。
1. 覆寫`OnElementChanged`呈現的自訂地圖和自訂，撰寫邏輯方法。 建立對應的 Xamarin.Forms 自訂對應時，會呼叫這個方法。
1. 新增`ExportRenderer`屬性來指定，它會用來呈現 Xamarin.Forms 自訂地圖的自訂轉譯器類別。 此屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 它是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，將使用預設的產生器控制項的基底類別。

下圖說明範例應用程式，以及它們之間的關聯性中的每一個專案的責任：

![](customized-pin-images/solution-structure.png "CustomMap 自訂轉譯器專案責任")

`CustomMap`要呈現控制項的平台特定轉譯器類別，衍生自`MapRenderer`每個平台的類別。 這會導致每個`CustomMap`控制所呈現使用平台專屬的控制項，如下列螢幕擷取畫面所示：

![](customized-pin-images/screenshots.png "每個平台上 CustomMap")

`MapRenderer`類別會公開`OnElementChanged`方法，來呈現對應的原生控制項建立 Xamarin.Forms 自訂對應時呼叫。 這個方法會採用`ElementChangedEventArgs`參數包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加和 Xamarin.Forms 的項目，轉譯器*是*附加至分別。 範例應用程式中`OldElement`屬性會是`null`和`NewElement`屬性會包含參考`CustomMap`執行個體。

覆寫的版本`OnElementChanged`方法，在每個平台特定轉譯器類別，是執行原生控制項自訂的位置。 平台上使用原生控制項的型別的參考可以透過存取`Control`屬性。 此外，要呈現 Xamarin.Forms 控制項的參考可以透過取得`Element`屬性。

必須小心訂閱中的事件處理常式時`OnElementChanged`方法，如下列程式碼範例所示：

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

應該設定原生控制項和事件處理常式的自訂轉譯器附加至新的 Xamarin.Forms 項目時，才訂閱。 同樣地，任何已訂閱的事件處理常式應該從只有當轉譯器附加至的項目變成取消訂閱。 採用這個方法將有助於您建立自訂轉譯器，不會發生記憶體流失。

每個自訂轉譯器類別以裝飾`ExportRenderer`xamarin.forms 註冊轉譯器的屬性。 屬性會採用兩個參數 – Xamarin.Forms 自訂控制項所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列章節會討論每個平台專屬的自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列螢幕擷取畫面顯示地圖上之前, 和之後自訂：

![](customized-pin-images/map-layout-ios.png "地圖控制項之前和之後的自訂")

在 iOS 上稱為 pin*註解*，而且可以是自訂映像或系統定義色彩的 pin。 標註可以選擇性地顯示*圖說文字*，便會顯示以回應使用者選取註解。 圖說文字顯示`Label`和`Address`屬性`Pin`執行個體，具有選擇性左和右配件的檢視。 在上面的螢幕擷取畫面，左方的配件檢視是猴子，映像與右配件檢視正在*資訊* 按鈕。

下列程式碼範例會顯示 iOS 平台的自訂轉譯器：

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

`OnElementChanged`方法會執行下列組態的[ `MKMapView` ](https://developer.xamarin.com/api/type/MapKit.MKMapView/)執行個體，前提是自訂轉譯器附加至新的 Xamarin.Forms 項目：

- [ `GetViewForAnnotation` ](https://developer.xamarin.com/api/property/MapKit.MKMapView.GetViewForAnnotation/)屬性設定為`GetViewForAnnotation`方法。 這個方法時，會呼叫[註解的位置會顯示在地圖上](#Displaying_the_Annotation)，用來自訂顯示註解之前。
- 事件處理常式`CalloutAccessoryControlTapped`， `DidSelectAnnotationView`，和`DidDeselectAnnotationView`登錄事件。 這些事件引發時使用者[點選右中圖說文字裝飾](#Tapping_on_the_Right_Callout_Accessory_View)，和當使用者[選取](#Selecting_the_Annotation)和[會取消選取](#Deselecting_the_Annotation)註釋，分別。 事件在轉譯器的項目附加至變更時，才可從取消訂閱。

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>顯示註解

`GetViewForAnnotation`時的註解位置隨即顯示在地圖上，以及用來自訂顯示註釋前，呼叫方法。 註釋具有兩個部分：

- `MkAnnotation` – 包含標題、 subtitle 和註解的位置。
- `MkAnnotationView` – 包含代表註解，以及 （選擇性） 當使用者點選註解顯示圖說文字的影像。

`GetViewForAnnotation`方法會接受`IMKAnnotation`，包含註解的資料，並傳回`MKAnnotationView`顯示在地圖上，下列程式碼範例所示：

```csharp
MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
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

這個方法可確保註釋將顯示為自訂映像，而不是和系統定莪 pin 碼，當點選註解圖說文字就會顯示包含其他內容，左側和右側的註解標題和位址. 完成此作業，如下所示：

1. `GetCustomPin`方法呼叫以傳回自訂 pin 資料的註解。
1. 節省記憶體，註解的檢視管理的集區以供重複使用與呼叫[ `DequeueReusableAnnotation` ](https://developer.xamarin.com/api/member/MapKit.MKMapView.DequeueReusableAnnotation/(System.String)/)。
1. `CustomMKAnnotationView`類別會擴充`MKAnnotationView`類別`Id`和`Url`屬性會對應至相同的內容中`CustomPin`執行個體。 新執行個體`CustomMKAnnotationView`建立時，前提是註解`null`:
  - `CustomMKAnnotationView.Image`屬性設定為將代表地圖上的註解的影像。
  - `CustomMKAnnotationView.CalloutOffset`屬性設定為`CGPoint`指定圖說文字將上方的註解會置中。
  - `CustomMKAnnotationView.LeftCalloutAccessoryView`屬性設為註解標題和地址的左邊會出現猴子的映像。
  - `CustomMKAnnotationView.RightCalloutAccessoryView`屬性設定為*資訊*右邊的註解標題和地址會顯示的按鈕。
  - `CustomMKAnnotationView.Id`屬性設定為`CustomPin.Id`屬性所傳回`GetCustomPin`方法。 這可讓用來識別，使其具有註釋[圖說文字可以進一步自訂](#Selecting_the_Annotation)視。
  - `CustomMKAnnotationView.Url`屬性設定為`CustomPin.Url`屬性所傳回`GetCustomPin`方法。 將瀏覽的 URL，當使用者[點選該按鈕右邊的圖說文字配件檢視中顯示](#Tapping_on_the_Right_Callout_Accessory_View)。
1. [ `MKAnnotationView.CanShowCallout` ](https://developer.xamarin.com/api/property/MapKit.MKAnnotationView.CanShowCallout/)屬性設定為`true`，因此圖說文字會顯示當點選註解。
1. 註解就會傳回顯示在地圖上。

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>選取註解

當使用者點選註解，`DidSelectAnnotationView`事件引發，接著執行`OnDidSelectAnnotationView`方法：

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

此方法擴充現有的註標 （其包含左和右配件檢視） 加入`UIView`它包含 Xamarin 標誌的影像，前提是已選取的註解的執行個體其`Id`屬性設定為`Xamarin`. 這可讓其中不同圖說文字可能會顯示不同的註解的案例。 `UIView`上方現有的圖說文字置中位置就會顯示執行個體。

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>在右邊的圖說文字裝飾檢視上的點選

當使用者點選上*資訊*在右邊的圖說文字配件檢視中，按鈕`CalloutAccessoryControlTapped`事件引發，接著執行`OnCalloutAccessoryControlTapped`方法：

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

這個方法會開啟網頁瀏覽器並瀏覽至的位址儲存在`CustomMKAnnotationView.Url`屬性。 請注意，在建立時，已定義位址`CustomPin`.NET 標準程式庫專案中的集合。

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>取消選取註解

當顯示註解，以及使用者在地圖上，點選`DidDeselectAnnotationView`事件引發，接著執行`OnDidDeselectAnnotationView`方法：

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

這個方法確保當未選取現有的圖說文字、 註標 （Xamarin 標誌的影像） 的延伸的部分也會停止顯示，而且將會釋放其資源。

如需有關自訂`MKMapView`執行個體，請參閱[iOS 對應](~/ios/user-interface/controls/ios-maps/index.md)。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立的自訂轉譯器

下列螢幕擷取畫面顯示地圖上之前, 和之後自訂：

![](customized-pin-images/map-layout-android.png "地圖控制項之前和之後的自訂")

在 Android 上稱為 pin*標記*，並可以是自訂映像或系統定義色彩的標記。 標記可以顯示*資訊視窗*，便會顯示以回應使用者在標記上點選。 [資訊] 視窗會顯示`Label`和`Address`屬性`Pin`執行個體，並且可以自訂為包含其他內容。 不過，只有一個資訊視窗可以顯示一次。

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

提供的自訂轉譯器附加至新的 Xamarin.Forms 元素`OnElementChanged`方法呼叫`MapView.GetMapAsync`方法，取得基礎`GoogleMap`繫結至檢視。 一次`GoogleMap`執行個體可供使用，`OnMapReady`覆寫會叫用。 這個方法會註冊事件處理常式`InfoWindowClick`時引發的事件[資訊視窗中按下](#Clicking_on_the_Info_Window)，和轉譯器的項目附加至變更時，才會取消訂閱。 `OnMapReady`也覆寫呼叫`SetInfoWindowAdapter`方法，以指定的`CustomMapRenderer`類別執行個體將會提供方法以自訂 [資訊] 視窗。

`CustomMapRenderer`類別會實作`GoogleMap.IInfoWindowAdapter`介面[自訂 [資訊] 視窗](#Customizing_the_Info_Window)。 這個介面會指定，必須實作下列方法：

- `public Android.Views.View GetInfoWindow(Marker marker)` – 此方法呼叫以傳回標記的自訂資訊視窗中。 如果它傳回`null`，則會使用預設視窗呈現。 如果它傳回`View`，，然後`View`置於資訊視窗框架。
- `public Android.Views.View GetInfoContents(Marker marker)` – 此方法呼叫以傳回`View`包含內容的 [資訊] 視窗中，而且將只在如果才呼叫`GetInfoWindow`方法會傳回`null`。 如果它傳回`null`，則會使用預設呈現的資訊視窗內容。

範例應用程式中自訂只資訊視窗內容，所以`GetInfoWindow`方法會傳回`null`啟用此設定。

#### <a name="customizing-the-marker"></a>自訂標記

用來表示標記的圖示可以藉由呼叫自訂`MarkerOptions.SetIcon`方法。 這可藉由覆寫`CreateMarker`方法，這個方法會叫用每個`Pin`加入對應至：

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

這個方法會建立新`MarkerOption`每個執行個體`Pin`執行個體。 設定之後的位置、 標籤和標記的位址，設定它的圖示`SetIcon`方法。 這個方法會採用`BitmapDescriptor`物件，其中包含與呈現圖示，所需的資料`BitmapDescriptorFactory`類別提供 helper 方法，來簡化建立`BitmapDescriptor`。

如需有關使用`BitmapDescriptorFactory`類別，自訂標記，請參閱[自訂標記](~/android/platform/maps-and-location/maps/maps-api.md)。

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>自訂 [資訊] 視窗

當使用者在標記中，點選`GetInfoContents`執行方法，但前提是`GetInfoWindow`方法會傳回`null`。 下列程式碼範例示範`GetInfoContents`方法：

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

這個方法會傳回`View`包含內容的 [資訊] 視窗。 完成此作業，如下所示：

- A`LayoutInflater`擷取執行個體。 這用來執行個體化到其對應的版面配置的 XML 檔案`View`。
- `GetCustomPin`方法呼叫以傳回自訂 pin 資料的 [資訊] 視窗。
- `XamarinMapInfoWindow`版面配置膨脹如果`CustomPin.Id`屬性等於`Xamarin`。 否則，`MapInfoWindow`版面配置膨脹。 這可讓在不同的資訊視窗版面配置可能會顯示不同的標記案例。
- `InfoWindowTitle`和`InfoWindowSubtitle`資源會擷取在擴大的版面配置，以及其`Text`屬性會設定為對應的資料從`Marker`執行個體，能提供的資源不會`null`。
- `View`顯示在地圖上傳回執行個體。

> [!NOTE]
> 不是即時的資訊視窗`View`。 相反地，會將轉換 Android`View`為靜態點陣圖和顯示做為映像。 這表示，雖然資訊視窗可以回應 click 事件，它無法回應任何觸控事件或軌跡，以及 [資訊] 視窗中的個別控制項無法回應自己 click 事件。

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>按一下 [資訊] 視窗

當使用者按一下 [資訊] 視窗上`InfoWindowClick`事件引發，接著執行`OnInfoWindowClick`方法：

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

這個方法會開啟網頁瀏覽器並瀏覽至的位址儲存在`Url`屬性擷取`CustomPin`例項而言`Marker`。 請注意，在建立時，已定義位址`CustomPin`.NET 標準程式庫專案中的集合。

如需有關自訂`MapView`執行個體，請參閱[Maps API](~/android/platform/maps-and-location/maps/maps-api.md)。

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>通用 Windows 平台上建立的自訂轉譯器

下列螢幕擷取畫面顯示地圖上之前, 和之後自訂：

![](customized-pin-images/map-layout-uwp.png "地圖控制項之前和之後的自訂")

在 UWP 上稱為 pin*對應圖示*，並可以是自訂映像或系統定義的預設映像。 可以顯示的地圖圖示`UserControl`，便會顯示以回應使用者點選 [地圖] 圖示。 `UserControl`可以顯示任何內容，包括`Label`和`Address`屬性`Pin`執行個體。

下列程式碼範例示範 UWP 自訂轉譯器：

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

`OnElementChanged`方法會執行下列作業，前提是自訂轉譯器附加至新的 Xamarin.Forms 項目：

- 它會清除`MapControl.Children`對應，移除任何現有的使用者介面項目，再註冊事件處理常式的集合`MapElementClick`事件。 當使用者點選或按一下，就會引發這個事件`MapElement`上`MapControl`，和轉譯器的項目附加至變更時，才會取消訂閱。
- 中的每個 pin`customPins`集合會顯示在地圖上的正確地理位置如下：
  - 指針的位置建立為`Geopoint`執行個體。
  - A`MapIcon`執行個體是用來代表 pin。
  - 用來代表影像`MapIcon`設定來指定`MapIcon.Image`屬性。 不過，對應圖示的影像不保證一定要顯示，因為它可能會變模糊地圖上的其他項目。 因此，對應圖示的`CollisionBehaviorDesired`屬性設定為`MapElementCollisionBehavior.RemainVisible`，以確保它仍會顯示。
  - 位置`MapIcon`設定來指定`MapIcon.Location`屬性。
  - `MapIcon.NormalizedAnchorPoint`屬性設定為影像上之指標的概略位置。 如果這個屬性會保留其預設值為 (0，0)，代表影像的左上的角，地圖的縮放層級中的變更可能會導致指向不同位置的映像。
  - `MapIcon`執行個體新增至`MapControl.MapElements`集合。 這會導致顯示的地圖圖示`MapControl`。

> [!NOTE]
> 多個對應的圖示，使用相同的映像時`RandomAccessStreamReference`應該在網頁或應用程式層級宣告執行個體，為了達到最佳效能。

#### <a name="displaying-the-usercontrol"></a>顯示使用者控制項

當使用者在 [地圖] 圖示，點選`OnMapElementClick`執行方法。 下列程式碼範例會示範這個方法：

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

這個方法會建立`UserControl`顯示 pin 的相關資訊的執行個體。 完成此作業，如下所示：

- `MapIcon`擷取執行個體。
- `GetCustomPin`方法呼叫以傳回自訂 pin 資料將會顯示。
- A`XamarinMapOverlay`顯示自訂的 pin 碼資料建立執行個體。 這個類別是一個使用者控制項。
- 要顯示的地理位置`XamarinMapOverlay`執行個體上`MapControl`建立為`Geopoint`執行個體。
- `XamarinMapOverlay`執行個體新增至`MapControl.Children`集合。 此集合包含 XAML 使用者介面項目將會顯示在地圖。
- 地理位置`XamarinMapOverlay`地圖上的執行個體已設定藉由呼叫`SetLocation`方法。
- 上的相對位置`XamarinMapOverlay`設定執行個體，對應到指定的位置，藉由呼叫`SetNormalizedAnchorPoint`方法。 這可確保，對應結果中的縮放層級中的變更`XamarinMapOverlay`一律顯示在正確的位置執行個體。

或者，如果已經在地圖上顯示的 pin 碼的相關資訊，在地圖上的點選移除`XamarinMapOverlay`的執行個體`MapControl.Children`集合。

#### <a name="tapping-on-the-information-button"></a>點選 [資訊] 按鈕

當使用者點選上*資訊*按鈕`XamarinMapOverlay`使用者控制項，`Tapped`事件引發，接著執行`OnInfoButtonTapped`方法：

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

這個方法會開啟網頁瀏覽器並瀏覽至的位址儲存在`Url`屬性`CustomPin`執行個體。 請注意，在建立時，已定義位址`CustomPin`.NET 標準程式庫專案中的集合。

如需有關自訂`MapControl`執行個體，請參閱[地圖與位置概觀](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx)MSDN 上。

## <a name="summary"></a>總結

本文示範如何建立自訂轉譯器`Map`控制項，讓開發人員覆寫預設原生呈現使用他們自己平台專屬的自訂。 Xamarin.Forms.Maps 提供抽象的跨平台，顯示所使用的使用者體驗上每個平台服務 Api 用於提供快速且熟悉的對應的原生對應的對應。


## <a name="related-links"></a>相關連結

- [地圖控制項](~/xamarin-forms/user-interface/map.md)
- [iOS 對應](~/ios/user-interface/controls/ios-maps/index.md)
- [Maps API](~/android/platform/maps-and-location/maps/maps-api.md)
- [自訂的 Pin （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)

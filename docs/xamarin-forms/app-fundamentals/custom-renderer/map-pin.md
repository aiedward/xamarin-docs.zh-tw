---
標題：「自訂地圖釘選」描述：「本文將示範如何建立地圖控制項的自訂轉譯器，這會顯示具有自訂釘選的原生地圖，以及在每個平臺上的 pin 資料自訂視圖。」
assetid： C5481D86-80E9-4E3D-9FB6-57B0F93711A6 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：11/06/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="customizing-a-map-pin"></a>自訂地圖釘選

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin)

_本文示範如何建立 Map 控制項的自訂轉譯器，該控制項會在每個平台上以釘選資料的自訂釘選和自訂檢視來顯示原生地圖。_

每 Xamarin.Forms 個視圖都會針對每個建立原生控制項實例的平臺，隨附一個轉譯器。 當 [`Map`](xref:Xamarin.Forms.Maps.Map) Xamarin.Forms 應用程式在 iOS 中呈現時， `MapRenderer` 類別會具現化，然後再具現化原生 `MKMapView` 控制項。 在 Android 平台上，`MapRenderer` 類別會具現化原生 `MapView` 控制項。 在通用 Windows 平台 (UWP) 上，`MapRenderer` 類別會具現化原生的 `MapControl`。 如需控制項對應之轉譯器和原生控制項類別的詳細資訊 Xamarin.Forms ，請參閱轉譯器[基類和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明 [`Map`](xref:Xamarin.Forms.Maps.Map) 和執行它的對應原生控制項之間的關聯性：

![](map-pin-images/map-classes.png "Relationship Between the Map Control and the Implementing Native Controls")

轉譯程式可以 [`Map`](xref:Xamarin.Forms.Maps.Map) 在每個平臺上建立的自訂轉譯器，用來執行平臺特定的自訂。 執行這項作業的流程如下：

1. [建立](#creating-the-custom-map) Xamarin.Forms 自訂地圖。
1. [使用](#consuming-the-custom-map)自訂地圖 Xamarin.Forms 。
1. 在每個平台上[建立](#creating-the-custom-renderer-on-each-platform)地圖的自訂轉譯器。

每個項目現在會依序進行討論，以實作在每個平台上顯示自訂釘選及釘選資料自定檢視的 `CustomMap` 轉譯器。

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps)必須先初始化並設定，才能使用。 如需詳細資訊，請參閱 [`Maps Control`](~/xamarin-forms/user-interface/map/index.md)。

## <a name="creating-the-custom-map"></a>建立自訂地圖

您可以子類別化類別來建立自訂地圖控制項 [`Map`](xref:Xamarin.Forms.Maps.Map) ，如下列程式碼範例所示：

```csharp
public class CustomMap : Map
{
    public List<CustomPin> CustomPins { get; set; }
}
```

`CustomMap` 控制項會在 .NET Standard 程式庫專案中建立，並定義自訂地圖的 API。 自訂地圖會公開 `CustomPins` 屬性，代表會由每個平台原生地圖控制項轉譯的 `CustomPin` 物件集合。 `CustomPin` 類別如下列程式碼範例所示：

```csharp
public class CustomPin : Pin
{
    public string Name { get; set; }
    public string Url { get; set; }
}
```

這個類別會將定義 `CustomPin` 為繼承類別的屬性 [`Pin`](xref:Xamarin.Forms.Maps.Pin) ，以及加入 `Name` 和 `Url` 屬性。

## <a name="consuming-the-custom-map"></a>使用自訂地圖

您可以宣告控制項的位置命名空間並使用自訂地圖控制項上的命名空間前置詞，在 .NET Standard 程式庫專案的 XAML 中參考 `CustomMap` 控制項。 下列程式碼範例示範 XAML 頁面如何使用 `CustomMap` 控制項：

```xaml
<ContentPage ...
                   xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <local:CustomMap x:Name="customMap"
                   MapType="Street" />
</ContentPage>

```

`local` 命名空間前置詞沒有命名限制。 不過，`clr-namespace` 和 `assembly` 值必須符合自訂地圖的詳細資料。 一旦宣告命名空間，即會使用前置詞來參考自訂地圖。

下列程式碼範例示範 C# 頁面如何使用 `CustomMap` 控制項：

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS()
    {
        CustomMap customMap = new CustomMap
        {
            MapType = MapType.Street
        };
        // ...
        Content = customMap;
    }
}
```

`CustomMap` 執行個體會用來在每個平台上顯示原生地圖。 它的 [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) 屬性會設定的顯示樣式 [`Map`](xref:Xamarin.Forms.Maps.Map) ，並在列舉中定義可能的值 [`MapType`](xref:Xamarin.Forms.Maps.MapType) 。

地圖的位置以及其包含的釘選會進行初始化，如下列程式碼範例所示：

```csharp
public MapPage()
{
    // ...
    CustomPin pin = new CustomPin
    {
        Type = PinType.Place,
        Position = new Position(37.79752, -122.40183),
        Label = "Xamarin San Francisco Office",
        Address = "394 Pacific Ave, San Francisco CA",
        Name = "Xamarin",
        Url = "http://xamarin.com/about/"
    };
    customMap.CustomPins = new List<CustomPin> { pin };
    customMap.Pins.Add(pin);
    customMap.MoveToRegion(MapSpan.FromCenterAndRadius(new Position(37.79752, -122.40183), Distance.FromMiles(1.0)));
}
```

此初始化會加入自訂的釘選，並使用方法定位地圖的視圖 [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) ，這會藉由從和建立來變更地圖的位置和縮放層級 [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) [`Position`](xref:Xamarin.Forms.Maps.Position) [`Distance`](xref:Xamarin.Forms.Maps.Distance) 。

自訂轉譯器現在可以新增至每個應用程式專案，來自訂原生地圖控制項。

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每個平台上建立自訂轉譯器

建立自訂轉譯器類別的程序如下：

1. 建立轉譯自訂地圖 `MapRenderer` 類別的子類別。
1. 覆寫轉譯自訂地圖的 `OnElementChanged` 方法，並撰寫自訂它的邏輯。 建立對應的自訂地圖時，會呼叫這個方法 Xamarin.Forms 。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，以指定它將用來呈現 Xamarin.Forms 自訂地圖。 這個屬性是用來向註冊自訂轉譯器 Xamarin.Forms 。

> [!NOTE]
> 您可以選擇在每個平台專案中提供自訂轉譯器。 如果自訂轉譯器尚未註冊，則會使用控制項基底類別的預設轉譯器。

下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](map-pin-images/solution-structure.png "CustomMap Custom Renderer Project Responsibilities")

`CustomMap` 控制項是由平台特定轉譯器類別轉譯，其全部衍生自各平台的 `MapRenderer` 類別。 這會導致每個 `CustomMap` 控制項都使用平台特定控制項轉譯，如下列螢幕擷取畫面所示：

![](map-pin-images/screenshots.png "CustomMap on each Platform")

`MapRenderer`類別 `OnElementChanged` 會公開方法，這會在 Xamarin.Forms 建立自訂地圖以轉譯對應的原生控制項時呼叫。 此方法會接受 `ElementChangedEventArgs` 參數，其中包含 `OldElement` 和 `NewElement` 屬性。 這些屬性代表轉譯器 Xamarin.Forms 附加到的*was*專案，以及轉譯器 Xamarin.Forms 附加至的元素。 *is* 在應用程式範例中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `CustomMap` 執行個體的參考。

在每個平台特定轉譯器類別中，`OnElementChanged` 方法的覆寫版本是執行原生控制項自訂的位置。 平台上所使用的原生控制項具型別參考可透過 `Control` 屬性存取。 此外，您 Xamarin.Forms 可以透過屬性取得所要轉譯之控制項的參考 `Element` 。

在 `OnElementChanged` 方法中訂閱事件處理常式時必須留意，如下列程式碼範例中所示：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.View> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
      // Unsubscribe from event handlers
  }

  if (e.NewElement != null)
  {
      // Configure the native control and subscribe to event handlers
  }
}
```

只有在自訂轉譯器附加至新的專案時，才會設定原生控制項並訂閱事件處理常式 Xamarin.Forms 。 同樣地，您應該只在轉譯器附加到的項目變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法將有助於建立不會發生記憶體流失的自訂轉譯器。

每個自訂轉譯器類別都會以向註冊轉譯器的 `ExportRenderer` 屬性裝飾 Xamarin.Forms 。 屬性會採用兩個參數– Xamarin.Forms 所呈現之自訂控制項的類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

下列各節會討論每個平台特定自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列螢幕擷取畫面會顯示自訂前和自訂後的地圖：

![](map-pin-images/map-layout-ios.png "Map Control Before and After Customization")

在 iOS 上，釘選稱為「註釋」**，且可以是自訂影像或系統定義的各種顏色圖釘。 釘選可以選擇性地顯示「圖說文字」**，該文字會在回應選取註釋的使用者時顯示。 圖說文字會顯示 `Pin` 執行個體的 `Label` 和 `Address` 屬性，且可以選擇性的附帶左側或右側裝飾檢視。 在上方的螢幕擷取畫面中，左側裝飾檢視是一隻猴子的影像，右側裝飾檢視則是「資訊」** 按鈕。

下列程式碼範例示範適用於 iOS 平台的自訂轉譯器：

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

            if (e.OldElement != null)
            {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null)
                {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        // ...
    }
}
```

`OnElementChanged`方法會執行實例的下列設定 [`MKMapView`](xref:MapKit.MKMapView) ，前提是自訂轉譯器已附加至新的 Xamarin.Forms 元素：

- [`GetViewForAnnotation`](xref:MapKit.MKMapView.GetViewForAnnotation*)屬性會設定為 `GetViewForAnnotation` 方法。 此方法會在[註釋位置於地圖上可見時](#displaying-the-annotation)呼叫，且會用於在顯示前自訂註釋。
- `CalloutAccessoryControlTapped`、`DidSelectAnnotationView` 和 `DidDeselectAnnotationView` 的事件處理常式會進行註冊。 這些事件分別會在使用者[點選圖說文字中的右側裝飾](#tapping-on-the-right-callout-accessory-view)，以及使用者[選取](#selecting-the-annotation)和[取消選取](#deselecting-the-annotation)註釋時引發。 只有在轉譯器附加到的項目變更時，才會取消訂閱事件。

#### <a name="displaying-the-annotation"></a>顯示註釋

`GetViewForAnnotation` 方法會在註釋位置於地圖上可見時呼叫，且會用於在顯示前自訂註釋。 註釋有兩個部分：

- `MkAnnotation` – 包含標題、子標題，以及註釋的位置。
- `MkAnnotationView` – 包含表示註釋的影像，以及在使用者點選註釋時顯示的選擇性圖說文字。

`GetViewForAnnotation` 方法接受 `IMKAnnotation`，其中包含註釋的資料，並會傳回 `MKAnnotationView`，用於在地圖上顯示，如下列程式碼範例所示：

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Name);
    if (annotationView == null)
    {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Name);
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Name = customPin.Name;
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

此方法可確保將註釋顯示為自訂影像，而非系統定義的圖釘，且在註釋受到點選時，顯示在註釋標題和地址左側和右側包含額外內容的圖說文字。 執行此作業的流程如下：

1. 呼叫 `GetCustomPin` 方法傳回註釋的自訂釘選資料。
1. 為了節省記憶體，批註的視圖會共用，以便在呼叫時重複使用 [`DequeueReusableAnnotation`](xref:MapKit.MKMapView.DequeueReusableAnnotation*) 。
1. `CustomMKAnnotationView` 類別會使用對應到 `CustomPin` 執行個體中相同屬性的 `Name` 和 `Url` 屬性來擴充 `MKAnnotationView` 類別。 若註釋為 `null`，則建立 `CustomMKAnnotationView` 的新執行個體：
    - `CustomMKAnnotationView.Image` 屬性會設為表示地圖上註釋的影像。
    - `CustomMKAnnotationView.CalloutOffset` 屬性會設為 `CGPoint`，其指定圖說文字置中並位於註釋上方。
    - `CustomMKAnnotationView.LeftCalloutAccessoryView` 屬性會設為猴子影像，顯示在註釋標題和地址的左側。
    - `CustomMKAnnotationView.RightCalloutAccessoryView` 屬性會設為「資訊」** 按鈕，顯示在註釋標題和地址的右側。
    - `CustomMKAnnotationView.Name` 屬性會設為 `GetCustomPin` 方法傳回的 `CustomPin.Name` 屬性。 這可以用來識別註釋，使其圖說文字[能供進一步自訂](#selecting-the-annotation) (若需要的話)。
    - `CustomMKAnnotationView.Url` 屬性會設為 `GetCustomPin` 方法傳回的 `CustomPin.Url` 屬性。 當使用者[點選顯示在右側圖說文字裝飾檢視中的按鈕](#tapping-on-the-right-callout-accessory-view)時，便會巡覽至該 URL。
1. [`MKAnnotationView.CanShowCallout`](xref:MapKit.MKAnnotationView.CanShowCallout*)屬性會設定為， `true` 以便在按注釋時，顯示標注。
1. 註釋接著便會傳回，以供在地圖上顯示。

#### <a name="selecting-the-annotation"></a>選取註釋

當使用者點選註釋時，便會引發 `DidSelectAnnotationView` 事件，該事件接著便會執行 `OnDidSelectAnnotationView` 方法：

```csharp
void OnDidSelectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    customPinView = new UIView();

    if (customView.Name.Equals("Xamarin"))
    {
        customPinView.Frame = new CGRect(0, 0, 200, 84);
        var image = new UIImageView(new CGRect(0, 0, 200, 84));
        image.Image = UIImage.FromFile("xamarin.png");
        customPinView.AddSubview(image);
        customPinView.Center = new CGPoint(0, -(e.View.Frame.Height + 75));
        e.View.AddSubview(customPinView);
    }
}
```

若選取的註釋已將其 `Name` 屬性設為 `Xamarin`，此方法便會藉由將包含 Xamarin 標誌影像的 `UIView` 執行個體新增到其中，來擴充現有的圖說文字 (包含左側及右側裝飾檢視)。 這可應用在為不同註釋顯示不同圖說文字的案例中。 `UIView` 執行個體會置中並顯示在現有圖說文字的上方。

#### <a name="tapping-on-the-right-callout-accessory-view"></a>點選右側圖說文字裝飾檢視

當使用者點選位於右側圖說文字裝飾檢視中的「資訊」** 按鈕時，便會引發 `CalloutAccessoryControlTapped` 事件，該事件接著便會執行 `OnCalloutAccessoryControlTapped` 方法：

```csharp
void OnCalloutAccessoryControlTapped(object sender, MKMapViewAccessoryTappedEventArgs e)
{
    CustomMKAnnotationView customView = e.View as CustomMKAnnotationView;
    if (!string.IsNullOrWhiteSpace(customView.Url))
    {
        UIApplication.SharedApplication.OpenUrl(new Foundation.NSUrl(customView.Url));
    }
}
```

此方法會開啟網頁瀏覽器，並巡覽至儲存在 `CustomMKAnnotationView.Url` 屬性中的網址。 請注意，網址會在於 .NET Standard 程式庫專案中建立 `CustomPin` 集合時定義。

#### <a name="deselecting-the-annotation"></a>取消選取註釋

當註釋已顯示並且使用者點選地圖時，便會引發 `DidDeselectAnnotationView` 事件，該事件接著便會執行 `OnDidDeselectAnnotationView` 方法：

```csharp
void OnDidDeselectAnnotationView(object sender, MKAnnotationViewEventArgs e)
{
    if (!e.View.Selected)
    {
        customPinView.RemoveFromSuperview();
        customPinView.Dispose();
        customPinView = null;
    }
}
```

此方法可確保當未選取現有圖說文字時，圖說文字的擴充部分 (Xamarin 標誌的影像) 也會停止顯示，並釋放其資源。

如需自訂 `MKMapView` 執行個體的詳細資訊，請參閱 [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

下列螢幕擷取畫面會顯示自訂前和自訂後的地圖：

![](map-pin-images/map-layout-android.png "Map Control Before and After Customization")

在 Android 上，釘選稱為「標記」**，且可以是自訂影像或系統定義的各種顏色圖釘。 標記可以顯示「資訊視窗」**，該視窗會在回應點選標記的使用者時顯示。 資訊視窗會顯示 `Pin` 執行個體的 `Label` 及 `Address` 屬性，且可以進行自訂以包含其它內容。 但是，一次只能顯示一個資訊視窗。

下列程式碼範例示範適用於 Android 平台的自訂轉譯器：

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

假設自訂轉譯器已附加至新的專案 Xamarin.Forms ，則此 `OnElementChanged` 方法會從控制項抓取自訂釘選清單。 一旦 `GoogleMap` 可供使用，便會叫用 `OnMapReady` 覆寫。 此方法會註冊 `InfoWindowClick` 事件的事件處理常式，該事件會在使用者[按一下資訊視窗](#clicking-on-the-info-window)時引發，且只會在轉譯器附加到的項目變更時取消訂閱。 `OnMapReady` 覆寫也會呼叫 `SetInfoWindowAdapter` 方法來指定 `CustomMapRenderer` 類別執行個體提供自訂資訊視窗的方法。

`CustomMapRenderer` 類別會實作 `GoogleMap.IInfoWindowAdapter` 介面，來[自訂資訊視窗](#customizing-the-info-window)。 此介面會指定必須實作下列方法：

- `public Android.Views.View GetInfoWindow(Marker marker)` – 此方法會進行呼叫以傳回標記的自訂資訊視窗。 若其傳回 `null`，則會使用預設視窗轉譯。 若其傳回 `View`，便會將該 `View` 放置在資訊視窗框架中。
- `public Android.Views.View GetInfoContents(Marker marker)` – 此方法會進行呼叫，以傳回包含資訊視窗內容的 `View`，且只會在 `GetInfoWindow` 方法傳回 `null` 時呼叫。 若其傳回 `null`，則會使用資訊視窗內容的預設轉譯。

在應用程式範例中，由於只有資訊視窗內容經過自訂，因此 `GetInfoWindow` 方法會傳回 `null` 來啟用它。

#### <a name="customizing-the-marker"></a>自訂標記

用來表示標記的圖示可透過呼叫 `MarkerOptions.SetIcon` 方法來自訂。 這可透過覆寫 `CreateMarker` 方法來完成，該方法會針對每個新增到地圖的 `Pin` 叫用：

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

此方法會為每個 `Pin` 執行個體建立新的 `MarkerOption` 執行個體。 在設定標記的位置、標籤及地址後，便會使用 `SetIcon` 方法來設定其圖示。 此方法接受 `BitmapDescriptor` 物件，該物件包含轉譯圖示所需要的資料，並使用 `BitmapDescriptorFactory` 類別提供協助程式方法來簡化 `BitmapDescriptor` 的建立。 如需使用 `BitmapDescriptorFactory` 類別自訂標記的詳細資訊，請參閱[自訂標記](~/android/platform/maps-and-location/maps/maps-api.md)。

> [!NOTE]
> 如有需要，可以在您的地圖轉譯器中叫用 `GetMarkerForPin` 方法，來從 `Pin` 擷取 `Marker`。

#### <a name="customizing-the-info-window"></a>自訂資訊視窗

若 `GetInfoWindow` 方法傳回 `null`，則當使用者點選標記時，便會執行 `GetInfoContents` 方法。 下列程式碼範例示範 `GetInfoContents` 方法：

```csharp
public Android.Views.View GetInfoContents(Marker marker)
{
    var inflater = Android.App.Application.Context.GetSystemService(Context.LayoutInflaterService) as Android.Views.LayoutInflater;
    if (inflater != null)
    {
        Android.Views.View view;

        var customPin = GetCustomPin(marker);
        if (customPin == null)
        {
            throw new Exception("Custom pin not found");
        }

        if (customPin.Name.Equals("Xamarin"))
        {
            view = inflater.Inflate(Resource.Layout.XamarinMapInfoWindow, null);
        }
        else
        {
            view = inflater.Inflate(Resource.Layout.MapInfoWindow, null);
        }

        var infoTitle = view.FindViewById<TextView>(Resource.Id.InfoWindowTitle);
        var infoSubtitle = view.FindViewById<TextView>(Resource.Id.InfoWindowSubtitle);

        if (infoTitle != null)
        {
            infoTitle.Text = marker.Title;
        }
        if (infoSubtitle != null)
        {
            infoSubtitle.Text = marker.Snippet;
        }

        return view;
    }
    return null;
}
```

此方法會傳回包含資訊視窗內容的 `View`。 執行此作業的流程如下：

- 擷取 `LayoutInflater` 執行個體。 這可用來將配置 XML 檔案具現化為對應的 `View`。
- 呼叫 `GetCustomPin` 方法，傳回資訊視窗的自訂釘選資料。
- 若 `CustomPin.Name` 屬性等於 `Xamarin`，則 `XamarinMapInfoWindow` 配置將會擴大。 否則，`MapInfoWindow` 配置便會擴大。 這可應用在為不同標記顯示不同資訊視窗配置的案例中。
- 若資源並非 `null`，便會從擴大的配置中擷取 `InfoWindowTitle` 和 `InfoWindowSubtitle` 資源，且其 `Text` 屬性會設為 `Marker` 執行個體中的對應資料。
- 接著便會傳回 `View` 執行個體，以供在地圖上顯示。

> [!NOTE]
> 資訊視窗並非即時 `View`。 相反地，Android 會將 `View` 轉換成靜態點陣圖並將其顯示為影像。 這表示雖然資訊視窗可以回應 Click 事件，但它無法回應任何觸控事件或手勢，且資訊視窗中的個別控制項也都無法回應各自 Click 事件。

#### <a name="clicking-on-the-info-window"></a>按一下資訊視窗

當使用者按一下資訊視窗時，便會引發 `InfoWindowClick` 事件，該事件接著便會執行 `OnInfoWindowClick` 方法：

```csharp
void OnInfoWindowClick(object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    var customPin = GetCustomPin(e.Marker);
    if (customPin == null)
    {
        throw new Exception("Custom pin not found");
    }

    if (!string.IsNullOrWhiteSpace(customPin.Url))
    {
        var url = Android.Net.Uri.Parse(customPin.Url);
        var intent = new Intent(Intent.ActionView, url);
        intent.AddFlags(ActivityFlags.NewTask);
        Android.App.Application.Context.StartActivity(intent);
    }
}
```

此方法會開啟網頁瀏覽器，並巡覽至儲存在針對 `Marker` 所擷取到 `CustomPin` 執行個體中 `Url` 屬性內的網址。 請注意，網址會在於 .NET Standard 程式庫專案中建立 `CustomPin` 集合時定義。

如需自訂 `MapView` 執行個體的詳細資訊，請參閱 [Maps API](~/android/platform/maps-and-location/maps/maps-api.md)。

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>在通用 Windows 平台上建立自訂轉譯器

下列螢幕擷取畫面會顯示自訂前和自訂後的地圖：

![](map-pin-images/map-layout-uwp.png "Map Control Before and After Customization")

在 UWP 上，釘選稱為「地圖圖示」**，且可以是自訂影像或系統定義的預設影像。 地圖圖示可以顯示 `UserControl`，該控制項會在回應點選地圖圖示的使用者時顯示。 `UserControl` 可以顯示任何內容，包括 `Pin` 執行個體的 `Label` 及 `Address` 屬性。

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

`OnElementChanged`方法會執行下列作業，前提是自訂轉譯器已附加至新的 Xamarin.Forms 元素：

- 清除 `MapControl.Children` 集合以從地圖移除任何現有的使用者介面項目，再註冊 `MapElementClick` 事件的事件處理常式。 此事件會在使用者點選或按一下 `MapControl` 上的 `MapElement` 時引發，且只會在轉譯器附加到的項目變更時取消訂閱。
- 透過下列方式，將每個 `customPins` 集合中的釘選顯示在地圖上的正確的地理位置：
  - 將釘選的位置作為 `Geopoint` 執行個體建立。
  - 建立 `MapIcon` 執行個體以代表釘選。
  - 透過設定 `MapIcon.Image` 屬性，指定用來代表 `MapIcon` 的影像。 但是，無法保證一律會顯示地圖圖示的影像，因為影像可能會被地圖上的其它項目遮蔽。 因此，地圖圖示的 `CollisionBehaviorDesired` 屬性會設為 `MapElementCollisionBehavior.RemainVisible`，以確保它保持在可見狀態。
  - 透過設定 `MapIcon.Location` 屬性，指定 `MapIcon` 的位置。
  - 將 `MapIcon.NormalizedAnchorPoint` 屬性設為影像上指標的近似位置。 若此屬性維持在預設值 (0,0) (表示影像的左上角)，則變更地圖的縮放比例可能會導致影像指向不同位置。
  - 將 `MapIcon` 執行個體新增到 `MapControl.MapElements` 集合。 這會導致在 `MapControl` 中顯示地圖圖示。

> [!NOTE]
> 當針對多個地圖圖示使用相同影像時，應在頁面或應用程式層級宣告 `RandomAccessStreamReference` 執行個體，以取得最佳效能。

#### <a name="displaying-the-usercontrol"></a>顯示使用者控制項

當使用者點選地圖圖示時，便會執行 `OnMapElementClick` 方法。 下列程式碼範例示範此方法：

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

            if (customPin.Name.Equals("Xamarin"))
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Position.Latitude, Longitude = customPin.Position.Longitude };
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

此方法會建立 `UserControl` 執行個體，顯示釘選的相關資訊。 執行此作業的流程如下：

- 擷取 `MapIcon` 執行個體。
- 呼叫 `GetCustomPin` 方法，傳回要顯示的自訂釘選資料。
- 建立 `XamarinMapOverlay` 執行個體來顯示自訂釘選資料。 此類別為使用者控制項。
- 將要在 `MapControl` 上顯示 `XamarinMapOverlay` 執行個體的地理位置作為 `Geopoint` 執行個體建立。
- 將 `XamarinMapOverlay` 執行個體新增到 `MapControl.Children` 集合。 此集合包含將顯示在地圖上的 XAML 使用者介面項目。
- 透過呼叫 `SetLocation` 方法，設定 `XamarinMapOverlay` 執行個體在地圖上的地理位置。
- 透過呼叫 `SetNormalizedAnchorPoint` 方法，設定對應到指定位置的 `XamarinMapOverlay` 執行個體相對位置。 這可確保即使變更地圖的縮放比例，也一律會將 `XamarinMapOverlay` 執行個體顯示在正確的位置。

或者，若釘選的相關資訊已顯示在地圖上，點選地圖會將 `XamarinMapOverlay` 執行個體從 `MapControl.Children` 集合中移除。

#### <a name="tapping-on-the-information-button"></a>點選資訊按鈕

當使用者點選 `XamarinMapOverlay` 使用者控制項中的「資訊」** 按鈕時，便會引發 `Tapped` 事件，該事件接著便會執行 `OnInfoButtonTapped` 方法：

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

此方法會開啟網頁瀏覽器，並巡覽至儲存在 `CustomPin` 執行個體 `Url` 屬性中的網址。 請注意，網址會在於 .NET Standard 程式庫專案中建立 `CustomPin` 集合時定義。

如需自訂 `MapControl` 執行個體的詳細資訊，請參閱 MSDN 上的[地圖和位置概觀](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx)。

## <a name="related-links"></a>相關連結

- [地圖控制項](~/xamarin-forms/user-interface/map/index.md)
- [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md)
- [地圖 API](~/android/platform/maps-and-location/maps/maps-api.md)
- [Customized Pin (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-pin) (自訂釘選 (範例))

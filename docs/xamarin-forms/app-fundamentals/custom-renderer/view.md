---
標題：執行視圖描述：本文說明如何建立自訂控制項的自訂轉譯器 Xamarin.Forms ，以用來從裝置相機顯示預覽影片串流。
ms. 生產： xamarin assetid： 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2 ms. 技術： xamarin-forms author： davidbritch ms. author： dabritch ms. date： 10/30/2020 no loc：
- "Xamarin.Forms"
- "Xamarin.Essentials"

---
# <a name="implementing-a-view"></a>實作檢視

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/customrenderers-view)

_Xamarin.Forms 自訂使用者介面控制項應該衍生自 View 類別，這是用來在螢幕上放置配置和控制項。本文示範如何建立自訂控制項的自訂轉譯器 Xamarin.Forms ，以用來從裝置相機顯示預覽影片串流。_

每個 Xamarin.Forms 視圖都有每個平臺的轉譯器，可建立原生控制項的實例。 當 [`View`](xref:Xamarin.Forms.View) Xamarin.Forms 應用程式在 iOS 中轉譯時， `ViewRenderer` 類別會具現化，進而將原生控制項具現化 `UIView` 。 在 Android 平台上，`ViewRenderer` 類別會具現化原生 `View` 控制項。 在通用 Windows 平台 (UWP) 上，`ViewRenderer` 類別會具現化原生 `FrameworkElement` 控制項。 如需控制項對應之轉譯器和原生控制項類別的詳細資訊 Xamarin.Forms ，請參閱轉譯器 [基類和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

> [!NOTE]
> Android 上的某些控制項會使用快速轉譯器，而不會使用 `ViewRenderer` 類別。 如需快速轉譯器的詳細資訊，請參閱[ Xamarin.Forms 快速](~/xamarin-forms/internals/fast-renderers.md)轉譯器。

下圖說明 [`View`](xref:Xamarin.Forms.View) 與執行它的對應原生控制項之間的關聯性：

![View 類別與其實作原生類別之間的關聯性](view-images/view-classes.png)

您可以 [`View`](xref:Xamarin.Forms.View) 在每個平臺上建立的自訂轉譯器，使用轉譯程式來執行平臺特定的自訂。 執行這項作業的流程如下：

1. [建立](#creating-the-custom-control) Xamarin.Forms 自訂控制項。
1. [使用](#consuming-the-custom-control) 自訂控制項 Xamarin.Forms 。
1. 在每個平台上[建立](#creating-the-custom-renderer-on-each-platform)控制項的自訂轉譯器。

現在將依序討論每個項目，以實作 `CameraPreview` 轉譯器從裝置相機顯示預覽視訊資料流。 只要加以點選，就會停止和啟動視訊資料流。

## <a name="creating-the-custom-control"></a>建立自訂控制項

您可以子類別化類別來建立自訂控制項 [`View`](xref:Xamarin.Forms.View) ，如下列程式碼範例所示：

```csharp
public class CameraPreview : View
{
  public static readonly BindableProperty CameraProperty = BindableProperty.Create (
    propertyName: "Camera",
    returnType: typeof(CameraOptions),
    declaringType: typeof(CameraPreview),
    defaultValue: CameraOptions.Rear);

  public CameraOptions Camera
  {
    get { return (CameraOptions)GetValue (CameraProperty); }
    set { SetValue (CameraProperty, value); }
  }
}
```

`CameraPreview` 自訂控制項是在 .NET Standard 程式庫專案中建立，並能定義控制項的 API。 此自訂控制項會公開 `Camera` 屬性，用來控制應該從裝置的前方或後方相機顯示視訊資料流。 如果在建立控制項時未指定 `Camera` 屬性的值，則預設會指定為後方相機。

## <a name="consuming-the-custom-control"></a>使用自訂控制項

您可以宣告自訂控制項的位置命名空間，並使用自訂控制項元素上的命名空間前置詞，來在 .NET Standard 程式庫專案的 XAML 中參考 `CameraPreview` 自訂控制項。 下列程式碼範例示範 XAML 頁面如何使用 `CameraPreview` 自訂控制項：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             ...>
    <StackLayout>
        <Label Text="Camera Preview:" />
        <local:CameraPreview Camera="Rear"
                             HorizontalOptions="FillAndExpand"
                             VerticalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

`local` 命名空間前置詞沒有命名限制。 不過，`clr-namespace` 和 `assembly` 值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，即會使用前置詞來參考自訂控制項。

下列程式碼範例示範 C# 頁面如何使用 `CameraPreview` 自訂控制項：

```csharp
public class MainPageCS : ContentPage
{
  public MainPageCS ()
  {
    ...
    Content = new StackLayout
    {
      Children =
      {
        new Label { Text = "Camera Preview:" },
        new CameraPreview
        {
          Camera = CameraOptions.Rear,
          HorizontalOptions = LayoutOptions.FillAndExpand,
          VerticalOptions = LayoutOptions.FillAndExpand
        }
      }
    };
  }
}
```

`CameraPreview` 自訂控制項執行個體將用來從裝置相機顯示預覽視訊資料流。 除了可選擇性指定 `Camera` 屬性的值，控制項的自訂作業會在自訂轉譯器中完成。

自訂轉譯器現在可以新增至每個應用程式專案，來建立平台特定的相機預覽控制項。

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每個平台上建立自訂轉譯器

在 iOS 和 UWP 上建立自訂轉譯器類別的程式如下所示：

1. 建立轉譯自訂控制項之 `ViewRenderer<T1,T2>` 類別的子類別。 第一個型別引數應該是轉譯器所針對的自訂控制項，在本例中為 `CameraPreview`。 第二個型別引數應該是將實作自訂控制項的原生控制項。
1. 覆寫轉譯自訂控制項的 `OnElementChanged` 方法，並撰寫自訂方法的邏輯。 建立對應的控制項時，會呼叫這個方法 Xamarin.Forms 。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，以指定它將用來呈現 Xamarin.Forms 自訂控制項。 這個屬性是用來向註冊自訂轉譯器 Xamarin.Forms 。

以快速轉譯器的形式，在 Android 上建立自訂轉譯器類別的程式如下所示：

1. 建立可呈現自訂控制項之 Android 控制項的子類別。 此外，指定子類別將會執行 `IVisualElementRenderer` 和 `IViewRenderer` 介面。
1. `IVisualElementRenderer` `IViewRenderer` 在快速轉譯器類別中執行和介面。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，以指定它將用來呈現 Xamarin.Forms 自訂控制項。 這個屬性是用來向註冊自訂轉譯器 Xamarin.Forms 。

> [!NOTE]
> 針對大部分的 Xamarin.Forms 專案，您可以選擇性地在每個平臺專案中提供自訂轉譯器。 如果自訂轉譯器尚未註冊，則會使用控制項基底類別的預設轉譯器。 不過，轉譯 [View](xref:Xamarin.Forms.View) 項目時，每個平台專案都必須要有自訂轉譯器。

下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![CameraPreview 自訂轉譯器專案責任](view-images/solution-structure.png)

`CameraPreview`自訂控制項是由平臺特定轉譯器類別轉譯，其衍生自 `ViewRenderer` IOS 和 UWP 上的類別，以及從 `FrameLayout` Android 上的類別。 這會導致每個 `CameraPreview` 自訂控制項都使用平台特定控制項轉譯，如下列螢幕擷取畫面所示：

![每個平台上的 CameraPreview](view-images/screenshots.png)

`ViewRenderer`類別 `OnElementChanged` 會公開方法，這個方法會在 Xamarin.Forms 建立自訂控制項以轉譯對應的原生控制項時呼叫。 此方法會接受 `ElementChangedEventArgs` 參數，其中包含 `OldElement` 和 `NewElement` 屬性。 這些屬性代表轉譯器 Xamarin.Forms 附加到的 *was* 專案，以及轉譯器 Xamarin.Forms 附加到的元素。 *is* 在範例應用程式中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `CameraPreview` 執行個體的參考。

在每個平台特定的轉譯器類別中，`OnElementChanged` 方法之覆寫版本是執行原生控制項具現化和自訂的位置。 您應該使用 `SetNativeControl` 方法來具現化原生控制項，而且此方法也會將控制項參考指派給 `Control` 屬性。 此外，您 Xamarin.Forms 可以透過屬性取得正在轉譯之控制項的參考 `Element` 。

在某些情況下，可能會一再呼叫 `OnElementChanged` 方法。 因此，為避免記憶體流失，在具現化新的原生控制項時，請務必謹慎。 下列程式碼範例顯示在自訂轉譯器中具現化新的原生控制項時所使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null)
  {    
    if (Control == null)
    {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。 此外，只有在將自訂轉譯器附加至新的專案時，才應該建立、設定控制項，以及訂閱的事件處理常式 Xamarin.Forms 。 同樣地，應該只在轉譯器附加到的項目變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法將有助於建立高效能的自訂轉譯器，避免發生記憶體流失。

> [!IMPORTANT]
> 只有當 `e.NewElement` 不是 `null` 時，才應呼叫 `SetNativeControl`方法。

每個自訂轉譯器類別都會以註冊轉譯器的 `ExportRenderer` 屬性裝飾 Xamarin.Forms 。 屬性接受兩個參數：要轉譯之自訂控制項的型別名稱 Xamarin.Forms ，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

下列各節會討論每個平台特定自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例示範適用於 iOS 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, UICameraPreview>
    {
        UICameraPreview uiCameraPreview;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                if (Control == null) {
                  uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                  SetNativeControl (uiCameraPreview);
                }
                // Subscribe
                uiCameraPreview.Tapped += OnCameraPreviewTapped;
            }
        }

        void OnCameraPreviewTapped (object sender, EventArgs e)
        {
            if (uiCameraPreview.IsPreviewing) {
                uiCameraPreview.CaptureSession.StopRunning ();
                uiCameraPreview.IsPreviewing = false;
            } else {
                uiCameraPreview.CaptureSession.StartRunning ();
                uiCameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

若 `Control` 屬性為 `null`，則會呼叫 `SetNativeControl` 方法來具現化新的 `UICameraPreview` 控制項，並將其參考指派給 `Control` 屬性。 `UICameraPreview` 控制項是平台特定的自訂控制項，使用 `AVCapture` API 從相機提供預覽資料流。 它會公開由 `OnCameraPreviewTapped` 方法處理的 `Tapped` 事件，只要加以點選，就會停止和啟動視訊預覽。 `Tapped`當自訂轉譯器附加至新的專案 Xamarin.Forms ，並只在轉譯器附加到的專案變更時取消訂閱時，就會訂閱事件。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

下列程式碼範例顯示 Android 平臺的快速轉譯器：

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : FrameLayout, IVisualElementRenderer, IViewRenderer
    {
        // ...
        CameraPreview element;
        VisualElementTracker visualElementTracker;
        VisualElementRenderer visualElementRenderer;
        FragmentManager fragmentManager;
        CameraFragment cameraFragment;

        FragmentManager FragmentManager => fragmentManager ??= Context.GetFragmentManager();

        public event EventHandler<VisualElementChangedEventArgs> ElementChanged;
        public event EventHandler<PropertyChangedEventArgs> ElementPropertyChanged;

        CameraPreview Element
        {
            get => element;
            set
            {
                if (element == value)
                {
                    return;
                }

                var oldElement = element;
                element = value;
                OnElementChanged(new ElementChangedEventArgs<CameraPreview>(oldElement, element));
            }
        }

        public CameraPreviewRenderer(Context context) : base(context)
        {
            visualElementRenderer = new VisualElementRenderer(this);
        }

        void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            CameraFragment newFragment = null;

            if (e.OldElement != null)
            {
                e.OldElement.PropertyChanged -= OnElementPropertyChanged;
                cameraFragment.Dispose();
            }
            if (e.NewElement != null)
            {
                this.EnsureId();

                e.NewElement.PropertyChanged += OnElementPropertyChanged;

                ElevationHelper.SetElevation(this, e.NewElement);
                newFragment = new CameraFragment { Element = element };
            }

            FragmentManager.BeginTransaction()
                .Replace(Id, cameraFragment = newFragment, "camera")
                .Commit();
            ElementChanged?.Invoke(this, new VisualElementChangedEventArgs(e.OldElement, e.NewElement));
        }

        async void OnElementPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            ElementPropertyChanged?.Invoke(this, e);

            switch (e.PropertyName)
            {
                case "Width":
                    await cameraFragment.RetrieveCameraDevice();
                    break;
            }
        }       
        // ...
    }
}
```

在此範例中， `OnElementChanged` 方法會建立一個 `CameraFragment` 物件，前提是自訂轉譯器已附加至新的 Xamarin.Forms 元素。 此 `CameraFragment` 類型是自訂類別，它會使用 `Camera2` API 從相機提供預覽資料流程。 當轉譯器 `CameraFragment` 附加到的專案變更時，就會處置物件 Xamarin.Forms 。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上建立自訂轉譯器

下列程式碼範例示範適用於 UWP 的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        ...
        CaptureElement _captureElement;
        bool _isPreviewing;

        protected override void OnElementChanged(ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  ...
                  _captureElement = new CaptureElement();
                  _captureElement.Stretch = Stretch.UniformToFill;

                  SetupCamera();
                  SetNativeControl(_captureElement);
                }
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped(object sender, TappedRoutedEventArgs e)
        {
            if (_isPreviewing)
            {
                await StopPreviewAsync();
            }
            else
            {
                await StartPreviewAsync();
            }
        }
        ...
    }
}
```

若 `Control` 屬性為 `null`，則會具現化新的 `CaptureElement` 並呼叫 `SetupCamera` 方法，以使用 `MediaCapture` API 從相機提供預覽資料流。 然後會呼叫 `SetNativeControl` 方法，將 `CaptureElement` 執行個體的參考指派給 `Control` 屬性。 `CaptureElement` 控制項會公開由 `OnCameraPreviewTapped` 方法處理的 `Tapped` 事件，只要加以點選，就會停止和啟動視訊預覽。 `Tapped`當自訂轉譯器附加至新的專案 Xamarin.Forms ，並只在轉譯器附加到的專案變更時取消訂閱時，就會訂閱事件。

> [!NOTE]
> 請務必停止並處置提供 UWP 應用程式中相機存取權的物件。 若未這樣做，則可能會干擾嘗試存取裝置相機的其他應用程式。 如需詳細資訊，請參閱[顯示相機預覽](/windows/uwp/audio-video-camera/simple-camera-preview-access/)。

## <a name="summary"></a>總結

本文示範如何建立自訂控制項的自訂轉譯器 Xamarin.Forms ，以用來從裝置相機顯示預覽影片串流。 Xamarin.Forms 自訂使用者介面控制項應該衍生自 [`View`](xref:Xamarin.Forms.View) 類別，這是用來在螢幕上放置配置和控制項。

## <a name="related-links"></a>相關連結

- [View Custom Renderer (Samples)](/samples/xamarin/xamarin-forms-samples/customrenderers-view) (檢視自訂轉譯器 (範例))
---
title: 實作檢視
description: 本文說明如何建立自訂控制項的自訂轉譯器 Xamarin.Forms ，以用來從裝置相機顯示預覽影片串流。
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8215454f80614c0c7cca79af5cf51e2dd96453ae
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86929476"
---
# <a name="implementing-a-view"></a>實作檢視

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-view)

_Xamarin.Forms自訂使用者介面控制項應從 View 類別衍生，用來在螢幕上放置版面配置和控制項。本文示範如何建立自訂控制項的自訂轉譯器 Xamarin.Forms ，以用來從裝置相機顯示預覽影片串流。_

每 Xamarin.Forms 個視圖都會針對每個建立原生控制項實例的平臺，隨附一個轉譯器。 當 [`View`](xref:Xamarin.Forms.View) Xamarin.Forms 應用程式在 iOS 中呈現時， `ViewRenderer` 類別會具現化，然後再具現化原生 `UIView` 控制項。 在 Android 平台上，`ViewRenderer` 類別會具現化原生 `View` 控制項。 在通用 Windows 平台 (UWP) 上，`ViewRenderer` 類別會具現化原生 `FrameworkElement` 控制項。 如需控制項對應之轉譯器和原生控制項類別的詳細資訊 Xamarin.Forms ，請參閱轉譯器[基類和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明 [`View`](xref:Xamarin.Forms.View) 和執行它的對應原生控制項之間的關聯性：

![View 類別與其實作原生類別之間的關聯性](view-images/view-classes.png)

轉譯程式可以 [`View`](xref:Xamarin.Forms.View) 在每個平臺上建立的自訂轉譯器，用來執行平臺特定的自訂。 執行這項作業的流程如下：

1. [建立](#creating-the-custom-control) Xamarin.Forms 自訂控制項。
1. [使用](#consuming-the-custom-control)來自的自訂控制項 Xamarin.Forms 。
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

  public CameraOptions Camera {
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
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Camera Preview:" />
            <local:CameraPreview Camera="Rear"
              HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
        </StackLayout>
    </ContentPage.Content>
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
    Content = new StackLayout {
      Children = {
        new Label { Text = "Camera Preview:" },
        new CameraPreview {
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

建立自訂轉譯器類別的程序如下：

1. 建立轉譯自訂控制項之 `ViewRenderer<T1,T2>` 類別的子類別。 第一個型別引數應該是轉譯器所針對的自訂控制項，在本例中為 `CameraPreview`。 第二個型別引數應該是將實作自訂控制項的原生控制項。
1. 覆寫轉譯自訂控制項的 `OnElementChanged` 方法，並撰寫自訂方法的邏輯。 建立對應的控制項時，會呼叫這個方法 Xamarin.Forms 。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，以指定它將用來呈現 Xamarin.Forms 自訂控制項。 這個屬性是用來向註冊自訂轉譯器 Xamarin.Forms 。

> [!NOTE]
> 對於大部分的 Xamarin.Forms 元素而言，在每個平臺專案中提供自訂轉譯器是選擇性的。 如果自訂轉譯器尚未註冊，則會使用控制項基底類別的預設轉譯器。 不過，轉譯 [View](xref:Xamarin.Forms.View) 項目時，每個平台專案都必須要有自訂轉譯器。

下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![CameraPreview 自訂轉譯器專案責任](view-images/solution-structure.png)

`CameraPreview` 自訂控制項是由平台特定轉譯器類別轉譯，其全部衍生自各平台的 `ViewRenderer` 類別。 這會導致每個 `CameraPreview` 自訂控制項都使用平台特定控制項轉譯，如下列螢幕擷取畫面所示：

![每個平台上的 CameraPreview](view-images/screenshots.png)

`ViewRenderer`類別會公開 `OnElementChanged` 方法，這會在 Xamarin.Forms 建立自訂控制項以轉譯對應的原生控制項時呼叫。 此方法會接受 `ElementChangedEventArgs` 參數，其中包含 `OldElement` 和 `NewElement` 屬性。 這些屬性代表轉譯器 Xamarin.Forms 附加到的*was*專案，以及轉譯器 Xamarin.Forms 附加至的元素。 *is* 在範例應用程式中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `CameraPreview` 執行個體的參考。

在每個平台特定的轉譯器類別中，`OnElementChanged` 方法之覆寫版本是執行原生控制項具現化和自訂的位置。 您應該使用 `SetNativeControl` 方法來具現化原生控制項，而且此方法也會將控制項參考指派給 `Control` 屬性。 此外，您 Xamarin.Forms 可以透過屬性取得所要轉譯之控制項的參考 `Element` 。

在某些情況下，可能會一再呼叫 `OnElementChanged` 方法。 因此，為避免記憶體流失，在具現化新的原生控制項時，請務必謹慎。 下列程式碼範例顯示在自訂轉譯器中具現化新的原生控制項時所使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。 此外，您應該只在自訂轉譯器附加至新專案時，建立、設定及訂閱控制項的事件處理常式 Xamarin.Forms 。 同樣地，應該只在轉譯器附加到的項目變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法將有助於建立高效能的自訂轉譯器，避免發生記憶體流失。

> [!IMPORTANT]
> 只有當 `e.NewElement` 不是 `null` 時，才應呼叫 `SetNativeControl`方法。

每個自訂轉譯器類別都會以向註冊轉譯器的 `ExportRenderer` 屬性裝飾 Xamarin.Forms 。 屬性會採用兩個參數– Xamarin.Forms 所呈現之自訂控制項的類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

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

若 `Control` 屬性為 `null`，則會呼叫 `SetNativeControl` 方法來具現化新的 `UICameraPreview` 控制項，並將其參考指派給 `Control` 屬性。 `UICameraPreview` 控制項是平台特定的自訂控制項，使用 `AVCapture` API 從相機提供預覽資料流。 它會公開由 `OnCameraPreviewTapped` 方法處理的 `Tapped` 事件，只要加以點選，就會停止和啟動視訊預覽。 `Tapped`當自訂轉譯器附加至新專案 Xamarin.Forms ，且只有當轉譯器附加的專案變更時，才會訂閱事件。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

下列程式碼範例示範適用於 Android 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(CustomRenderer.CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPreviewRenderer : ViewRenderer<CustomRenderer.CameraPreview, CustomRenderer.Droid.CameraPreview>
    {
        CameraPreview cameraPreview;

        public CameraPreviewRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<CustomRenderer.CameraPreview> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                  cameraPreview = new CameraPreview(Context);
                  SetNativeControl(cameraPreview);
                }
                Control.Preview = Camera.Open((int)e.NewElement.Camera);

                // Subscribe
                cameraPreview.Click += OnCameraPreviewClicked;
            }
        }

        void OnCameraPreviewClicked(object sender, EventArgs e)
        {
            if (cameraPreview.IsPreviewing)
            {
                cameraPreview.Preview.StopPreview();
                cameraPreview.IsPreviewing = false;
            }
            else
            {
                cameraPreview.Preview.StartPreview();
                cameraPreview.IsPreviewing = true;
            }
        }
        ...
    }
}
```

若 `Control` 屬性為 `null`，則會呼叫 `SetNativeControl` 方法來具現化新的 `CameraPreview` 控制項，並將其參考指派給 `Control` 屬性。 `CameraPreview` 控制項是平台特定的自訂控制項，其使用 `Camera` API 從相機提供預覽資料流。 `CameraPreview`然後會設定控制項，前提是自訂轉譯器已附加至新的專案 Xamarin.Forms 。 此組態需要建立新的原生 `Camera` 物件來存取特定硬體相機，並註冊事件處理常式來處理 `Click` 事件。 之後，只要加以點選，此處理常式就會停止和啟動視訊預覽。 如果轉譯器附加的專案變更，則 `Click` 會取消訂閱事件 Xamarin.Forms 。

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

若 `Control` 屬性為 `null`，則會具現化新的 `CaptureElement` 並呼叫 `SetupCamera` 方法，以使用 `MediaCapture` API 從相機提供預覽資料流。 然後會呼叫 `SetNativeControl` 方法，將 `CaptureElement` 執行個體的參考指派給 `Control` 屬性。 `CaptureElement` 控制項會公開由 `OnCameraPreviewTapped` 方法處理的 `Tapped` 事件，只要加以點選，就會停止和啟動視訊預覽。 `Tapped`當自訂轉譯器附加至新專案 Xamarin.Forms ，且只有當轉譯器附加的專案變更時，才會訂閱事件。

> [!NOTE]
> 請務必停止並處置提供 UWP 應用程式中相機存取權的物件。 若未這樣做，則可能會干擾嘗試存取裝置相機的其他應用程式。 如需詳細資訊，請參閱[顯示相機預覽](/windows/uwp/audio-video-camera/simple-camera-preview-access/)。

## <a name="summary"></a>總結

本文已示範如何建立自訂控制項的自訂轉譯器 Xamarin.Forms ，以用來從裝置相機顯示預覽影片串流。 Xamarin.Forms自訂使用者介面控制項應該衍生自 [`View`](xref:Xamarin.Forms.View) 類別，用來在螢幕上放置版面配置和控制項。

## <a name="related-links"></a>相關連結

- [View Custom Renderer (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-view) (檢視自訂轉譯器 (範例))

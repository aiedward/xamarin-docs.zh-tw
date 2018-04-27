---
title: 實作檢視
description: Xamarin.Forms 自訂使用者介面控制項應衍生自檢視類別，用來放置版面配置和螢幕上的控制項。 本文示範如何建立自訂轉譯器用來顯示預覽視訊資料流裝置的相機中的 Xamarin.Forms 自訂控制項。
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: a8ab35b3ec13c76e1e00da6e3265e3e337e37b7e
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2018
---
# <a name="implementing-a-view"></a>實作檢視

_Xamarin.Forms 自訂使用者介面控制項應衍生自檢視類別，用來放置版面配置和螢幕上的控制項。本文示範如何建立自訂轉譯器用來顯示預覽視訊資料流裝置的相機中的 Xamarin.Forms 自訂控制項。_

Xamarin.Forms 中的每個檢視有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) Xamarin.Forms 應用程式在 iOS 中，呈現`ViewRenderer`類別具現化，這又會具現化原生`UIView`控制項。 Android 平台上，`ViewRenderer`類別具現化的原生`View`控制項。 在通用 Windows 平台 (UWP)，`ViewRenderer`類別具現化的原生`FrameworkElement`控制項。 如需有關轉譯器，而且 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱[轉譯器的基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)和對應的原生控制項實作它：

![](view-images/view-classes.png "檢視類別和實作的原生類別之間的關聯性")

轉譯程序可用來藉由建立自訂轉譯器的實作特定平台自訂[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)每個平台上。 執行此程序如下所示：

1. [建立](#Creating_the_Custom_Control)Xamarin.Forms 自訂控制項。
1. [取用](#Consuming_the_Custom_Control)Xamarin.Forms 中的，將自訂控制項。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)自訂轉譯器，針對每個平台上的控制項。

每個項目將現在依次討論實作`CameraPreview`轉譯器，會顯示裝置的相機中的預覽視訊資料流。 點選視訊資料流上的將會停止並啟動它。

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>建立自訂控制項

您可以建立自訂控制項子類別化[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)類別，如下列程式碼範例所示：

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

`CameraPreview`建立可攜式類別庫 (PCL) 專案中自訂控制項，並定義控制項的 API。 自訂控制項不僅會公開`Camera`屬性，可用於控制是否應顯示視訊資料流，從前端或後在裝置上的相機。 如果指定的值不是`Camera`屬性建立控制項時，它會預設為指定背面的相機。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自訂控制項

`CameraPreview`自訂控制項可以在 XAML 中參考 PCL 專案中，宣告的命名空間，做為其位置，並使用自訂控制項項目上的命名空間前置詞。 下列程式碼範例示範如何`CameraPreview`自訂控制項以供 XAML 頁面：

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

`local`命名空間前置詞可以命名為任何項目。 不過，`clr-namespace`和`assembly`值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，前置詞用來參考自訂控制項。

下列程式碼範例示範如何`CameraPreview`自訂控制項可供 C# 頁面：

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

執行個體`CameraPreview`自訂控制項將會用來顯示預覽視訊資料流從裝置的相機。 （選擇性） 指定的值除了`Camera`會實行屬性，自訂控制項的自訂轉譯器。

自訂轉譯器現在可以新增至每個應用程式專案，以建立特定平台相機預覽的控制項。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立的自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`ViewRenderer<T1,T2>`呈現自訂控制項的類別。 第一個型別引數應該是自訂控制項轉譯器，在此情況下`CameraPreview`。 第二個型別引數應該會實作自訂控制項的原生控制項。
1. 覆寫`OnElementChanged`呈現自訂它的自訂控制項和寫入邏輯方法。 建立對應的 Xamarin.Forms 控制項時，會呼叫這個方法。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 自訂控制項的自訂轉譯器類別。 此屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 大部分的 Xamarin.Forms 項目，則是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，將使用預設的產生器控制項的基底類別。 不過，自訂轉譯器所需每個平台專案中時呈現[檢視](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)項目。

下圖說明範例應用程式，以及它們之間的關聯性中的每一個專案的責任：

![](view-images/solution-structure.png "CameraPreview 自訂轉譯器專案責任")

`CameraPreview`自訂控制項的呈現由平台特定轉譯器類別，這些全都衍生自`ViewRenderer`每個平台的類別。 這會導致每個`CameraPreview`呈現使用平台專屬的控制項，如下列螢幕擷取畫面所示的自訂控制項：

![](view-images/screenshots.png "每個平台上 CameraPreview")

`ViewRenderer`類別會公開`OnElementChanged`Xamarin.Forms 自訂控制項是用來呈現對應的原生控制項時所呼叫的方法。 這個方法會採用`ElementChangedEventArgs`參數包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加和 Xamarin.Forms 的項目，轉譯器*是*附加至分別。 範例應用程式、`OldElement`屬性會是`null`和`NewElement`屬性會包含參考`CameraPreview`執行個體。

覆寫的版本`OnElementChanged`方法，在每個平台特定轉譯器類別，是要執行的原生控制項具現化及自訂的位置。 `SetNativeControl`方法應該使用原生控制項，具現化，而且這個方法也會指派的控制參考`Control`屬性。 此外，要呈現 Xamarin.Forms 控制項的參考可以透過取得`Element`屬性。

在某些情況下，`OnElementChanged`方法可以呼叫多次。 因此，若要避免記憶體流失，必須小心具現化新的原生控制項時。 下列程式碼範例顯示在自訂轉譯器中具現化新的原生控制項時所使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。 應該只在自訂控制項附加於新的 Xamarin.Forms 元素時，才設定控制項並訂閱事件處理常式。 同樣地，任何已訂閱的事件處理常式只能從轉譯器附加至的項目變更時取消訂閱。 採用這個方法可協助建立高效能的自訂轉譯器，不會發生記憶體流失。

每個自訂轉譯器類別以裝飾`ExportRenderer`xamarin.forms 註冊轉譯器的屬性。 屬性會採用兩個參數 – Xamarin.Forms 自訂控制項所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列章節會討論每個平台專屬的自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例會顯示 iOS 平台的自訂轉譯器：

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

            if (Control == null) {
                uiCameraPreview = new UICameraPreview (e.NewElement.Camera);
                SetNativeControl (uiCameraPreview);
            }
            if (e.OldElement != null) {
                // Unsubscribe
                uiCameraPreview.Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
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

前提是`Control`屬性是`null`、`SetNativeControl`呼叫方法來具現化新`UICameraPreview`控制項，並將指定的參考，以便`Control`屬性。 `UICameraPreview`控制項是平台專屬的自訂控制項，會使用`AVCapture`Api 用於提供從相機的預覽資料流。 它會公開`Tapped`由處理的事件`OnCameraPreviewTapped`來停止及啟動預覽視訊時它所點選的方法。 `Tapped`自訂轉譯器已連接到新的 Xamarin.Forms 項目，並取消訂閱只當轉譯器的項目附加至變更時要訂閱事件。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立的自訂轉譯器

下列程式碼範例會顯示 Android 平台的自訂轉譯器：

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

            if (Control == null)
            {
                cameraPreview = new CameraPreview(Context);
                SetNativeControl(cameraPreview);
            }

            if (e.OldElement != null)
            {
                // Unsubscribe
                cameraPreview.Click -= OnCameraPreviewClicked;
            }
            if (e.NewElement != null)
            {
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

前提是`Control`屬性是`null`、`SetNativeControl`呼叫方法來具現化新`CameraPreview`控制，並將該參考指派`Control`屬性。 `CameraPreview`控制項是平台專屬的自訂控制項，會使用`Camera`API 用於提供從相機的預覽資料流。 `CameraPreview`則設定控制項，前提是自訂轉譯器附加至新的 Xamarin.Forms 項目。 這項設定包括建立新的原生`Camera`存取特定硬體相機、 物件和註冊事件處理常式來處理`Click`事件。 接著此處理常式將會停止並啟動預覽視訊時它點選。 `Click` Xamarin.Forms 項目，轉譯器附加到變更，取消事件訂閱。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上建立的自訂轉譯器

下列程式碼範例示範 UWP 的自訂轉譯器：

```csharp
[assembly: ExportRenderer (typeof(CameraPreview), typeof(CameraPreviewRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPreviewRenderer : ViewRenderer<CameraPreview, Windows.UI.Xaml.Controls.CaptureElement>
    {
        MediaCapture mediaCapture;
        CaptureElement captureElement;
        CameraOptions cameraOptions;
        Application app;
        bool isPreviewing = false;

        protected override void OnElementChanged (ElementChangedEventArgs<CameraPreview> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                ...
                captureElement = new CaptureElement ();
                captureElement.Stretch = Stretch.UniformToFill;

                InitializeAsync ();
                SetNativeControl (captureElement);
            }
            if (e.OldElement != null) {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
            }
            if (e.NewElement != null) {
                // Subscribe
                Tapped += OnCameraPreviewTapped;
            }
        }

        async void OnCameraPreviewTapped (object sender, TappedRoutedEventArgs e)
        {
            if (isPreviewing) {
                await StopPreviewAsync ();
            } else {
                await StartPreviewAsync ();
            }
        }
        ...
    }
}
```

前提是`Control`屬性是`null`，新`CaptureElement`具現化和`InitializeAsync`呼叫方法時，它會使用`MediaCapture`API 用於提供從相機的預覽資料流。 `SetNativeControl`方法接著會呼叫指定的參考`CaptureElement`執行個體`Control`屬性。 `CaptureElement`控制公開`Tapped`由處理的事件`OnCameraPreviewTapped`來停止及啟動預覽視訊時它所點選的方法。 `Tapped`自訂轉譯器已連接到新的 Xamarin.Forms 項目，並取消訂閱只當轉譯器的項目附加至變更時要訂閱事件。

> [!NOTE]
> 請務必停止與處置的物件可提供存取的相機 UWP 應用程式中。 這樣可能會干擾其他應用程式嘗試存取裝置的相機。 如需詳細資訊，請參閱[顯示相機預覽](/windows/uwp/audio-video-camera/simple-camera-preview-access/)。

## <a name="summary"></a>總結

本文示範如何建立自訂轉譯器用來顯示預覽視訊資料流裝置的相機中的 Xamarin.Forms 自訂控制項。 Xamarin.Forms 自訂使用者介面控制項應衍生自[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)類別，用來放置版面配置和螢幕上的控制項。


## <a name="related-links"></a>相關連結

- [CustomRendererView （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)

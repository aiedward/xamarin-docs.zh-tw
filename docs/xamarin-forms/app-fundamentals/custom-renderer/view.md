---
title: 實作檢視
description: 這篇文章說明如何建立 Xamarin.Forms 自訂控制項是用來顯示裝置的相機中的預覽視訊資料流的自訂轉譯器。
ms.prod: xamarin
ms.assetid: 915E25E7-4A6B-4F34-B7B4-07D5F4B240F2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 8ee9926eb3b726673711141e7c75a68b607d02d3
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994698"
---
# <a name="implementing-a-view"></a>實作檢視

_Xamarin.Forms 自訂使用者介面控制項應該衍生自檢視類別，用來放置版面配置和螢幕上的控制項。這篇文章會示範如何建立 Xamarin.Forms 自訂控制項是用來顯示裝置的相機中的預覽視訊資料流的自訂轉譯器。_

Xamarin.Forms 中的每個檢視都有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `View` ](xref:Xamarin.Forms.View) Xamarin.Forms 應用程式在 iOS 中，呈現`ViewRenderer`類別具現化，以依序具現化原生`UIView`控制項。 Android 平台上，`ViewRenderer`類別會具現化原生`View`控制項。 在通用 Windows 平台 (UWP)，`ViewRenderer`類別會具現化原生`FrameworkElement`控制項。 如需有關轉譯器和 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱 <<c0> [ 轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `View` ](xref:Xamarin.Forms.View)和對應的原生控制項實作它：

![](view-images/view-classes.png "檢視類別與它實作的原生類別之間的關聯性")

轉譯程序可以用來建立自訂轉譯器來實作平台專屬的自訂[ `View` ](xref:Xamarin.Forms.View)每個平台。 執行此動作的程序如下所示：

1. [建立](#Creating_the_Custom_Control)Xamarin.Forms 自訂控制項。
1. [取用](#Consuming_the_Custom_Control)Xamarin.Forms 自訂控制項。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)自訂轉譯器，針對每個平台上的控制項。

每個項目會現在依次討論實作`CameraPreview`顯示裝置的相機中的預覽視訊資料流的轉譯器。 點選視訊資料流將會停止並啟動它。

<a name="Creating_the_Custom_Control" />

## <a name="creating-the-custom-control"></a>建立自訂控制項

可以建立自訂控制項的子類別化[ `View` ](xref:Xamarin.Forms.View)類別，如下列程式碼範例所示：

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

`CameraPreview`可攜式類別庫 (PCL) 專案中建立自訂控制項，並定義控制項的 API。 自訂控制項公開`Camera`用於控制是否應該顯示視訊資料流，從前端或後端相機的裝置上的屬性。 如果未指定值，如`Camera`屬性建立控制項時，它會預設為指定的後端相機。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自訂控制項

`CameraPreview`自訂控制項可以在 XAML 中參考 PCL 專案中，藉由宣告其位置的命名空間和自訂控制項的項目上使用的命名空間前置詞。 下列程式碼範例示範如何`CameraPreview`自訂控制項可供 XAML 頁面：

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

執行個體`CameraPreview`自訂控制項將會用來顯示預覽視訊資料流從裝置的相機。 除了可選擇性地指定的值`Camera`會實行屬性，自訂控制項的自訂轉譯器。

自訂轉譯器現在可以新增至每個應用程式專案，以建立特定平台相機預覽控制項。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`ViewRenderer<T1,T2>`呈現自訂控制項的類別。 第一個類型引數應該是自訂控制項轉譯器，在此情況下`CameraPreview`。 第二個類型引數應該是原生控制項，將實作自訂控制項。
1. 覆寫`OnElementChanged`呈現來自訂它的自訂控制及寫入邏輯的方法。 建立對應的 Xamarin.Forms 控制項時，會呼叫這個方法。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 自訂控制項的自訂轉譯器類別。 這個屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 大部分的 Xamarin.Forms 元素，則是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，則會使用預設的轉譯器控制項的基底類別。 不過，自訂轉譯器所需的每個平台專案中時轉譯[檢視](xref:Xamarin.Forms.View)項目。

下圖說明範例應用程式，以及其間的關聯性中的每個專案的責任：

![](view-images/solution-structure.png "CameraPreview 自訂轉譯器專案責任")

`CameraPreview`自訂控制項的呈現的平台特定的轉譯器類別，這些全都衍生自`ViewRenderer`每個平台的類別。 這會導致每個`CameraPreview`如下列螢幕擷取畫面所示，使用平台特定控制項所呈現的自訂控制項：

![](view-images/screenshots.png "每個平台的 CameraPreview")

`ViewRenderer`類別會公開`OnElementChanged`方法，這個方法會建立 Xamarin.Forms 自訂控制項以呈現對應的原生控制項時呼叫。 這個方法會採用`ElementChangedEventArgs`參數，其中包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加至，和 Xamarin.Forms 的項目，轉譯器*是*附加分別。 在範例應用程式`OldElement`屬性會是`null`並`NewElement`屬性會包含參考`CameraPreview`執行個體。

覆寫的新版`OnElementChanged`方法，在每個平台特定的轉譯器類別，是要執行的原生控制項具現化及自訂的位置。 `SetNativeControl`具現化原生控制項，應該使用方法，而且這個方法也會將控制項參考指派`Control`屬性。 此外，所呈現的 Xamarin.Forms 控制項的參考可以透過取得`Element`屬性。

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

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。 應該只在自訂控制項附加於新的 Xamarin.Forms 元素時，才設定控制項並訂閱事件處理常式。 同樣地，任何已訂閱的事件處理常式應該只能取消附加於項目變更時。 採用這個方法將有助於建立高效能的自訂轉譯器不會發生記憶體流失。

每個自訂轉譯器類別裝飾了`ExportRenderer`向 Xamarin.Forms 中的轉譯器的屬性。 屬性會採用兩個參數-Xamarin.Forms 自訂控制項所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列各節將討論每個平台專屬的自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例顯示適用於 iOS 平台的自訂轉譯器：

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

前提`Control`屬性是`null`，則`SetNativeControl`會呼叫方法來具現化新`UICameraPreview`控制項，並將指定的參考，以便`Control`屬性。 `UICameraPreview`控制項是平台專屬的自訂控制項，會使用`AVCapture`Api 用於提供來自相機的預覽資料流。 它會公開`Tapped`由處理的事件`OnCameraPreviewTapped`方法來停止並啟動影片預覽，當點選時。 `Tapped`時自訂轉譯器已連接到新的 Xamarin.Forms 元素，並只當您附加至變更的項目轉譯器時，才取消訂閱來訂閱事件。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

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

前提`Control`屬性是`null`，則`SetNativeControl`會呼叫方法來具現化新`CameraPreview`控制項，並指派它的參考`Control`屬性。 `CameraPreview`控制項是平台專屬的自訂控制項，會使用`Camera`API 提供來自相機的預覽資料流。 `CameraPreview`然後設定控制項，前提是自訂轉譯器會附加至新的 Xamarin.Forms 元素。 此組態需要建立新的原生`Camera`物件來存取特定的硬體網路攝影機和註冊事件處理常式來處理`Click`事件。 接著此處理常式會停止並啟動影片預覽，當點選時。 `Click`取消訂閱事件從 Xamarin.Forms 項目轉譯器附加到變更。

### <a name="creating-the-custom-renderer-on-uwp"></a>建立 UWP 上的自訂轉譯器

下列程式碼範例顯示適用於 UWP 的自訂轉譯器：

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

            if (Control == null)
            {
                ...
                _captureElement = new CaptureElement();
                _captureElement.Stretch = Stretch.UniformToFill;

                SetupCamera();
                SetNativeControl(_captureElement);
            }
            if (e.OldElement != null)
            {
                // Unsubscribe
                Tapped -= OnCameraPreviewTapped;
                ...
            }
            if (e.NewElement != null)
            {
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

前提`Control`屬性是`null`，新`CaptureElement`具現化並`SetupCamera`呼叫方法時，它會使用`MediaCapture`API 提供來自相機的預覽資料流。 `SetNativeControl`方法接著會呼叫指定的參考`CaptureElement`執行個體`Control`屬性。 `CaptureElement`控制項會公開`Tapped`由處理的事件`OnCameraPreviewTapped`方法來停止並啟動影片預覽，當點選時。 `Tapped`時自訂轉譯器已連接到新的 Xamarin.Forms 元素，並只當您附加至變更的項目轉譯器時，才取消訂閱來訂閱事件。

> [!NOTE]
> 請務必停止並處置的物件，提供存取權的 UWP 應用程式之觀景窗。 若要這樣做的失敗可能會干擾其他應用程式嘗試存取裝置的相機。 如需詳細資訊，請參閱 <<c0> [ 顯示相機預覽](/windows/uwp/audio-video-camera/simple-camera-preview-access/)。

## <a name="summary"></a>總結

這篇文章已示範如何建立 Xamarin.Forms 自訂控制項是用來顯示裝置的相機中的預覽視訊資料流的自訂轉譯器。 Xamarin.Forms 自訂使用者介面控制項應該衍生自[ `View` ](xref:Xamarin.Forms.View)類別，用來放置版面配置和螢幕上的控制項。


## <a name="related-links"></a>相關連結

- [CustomRendererView （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/view/)

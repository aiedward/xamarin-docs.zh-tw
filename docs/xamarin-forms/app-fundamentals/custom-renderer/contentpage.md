---
title: 自訂 ContentPage
description: ContentPage 是可見的項目，會顯示單一檢視，並佔用螢幕的大部分空間。 本文示範如何建立 ContentPage 頁面的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 2369b249681b926476cf3938c51c99745eba9098
ms.sourcegitcommit: 8888cb7d75f4469f2a1195b9a426a2e1fbf46bd8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "38995738"
---
# <a name="customizing-a-contentpage"></a>自訂 ContentPage

_ContentPage 是可見的項目，會顯示單一檢視，並佔用螢幕的大部分空間。本文示範如何建立 ContentPage 頁面的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。_

每個 Xamarin.Forms 控制項都隨附每個平台的轉譯器，這些平台可建立原生控制項的執行個體。 當 Xamarin.Forms 應用程式轉譯 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 時，在 iOS 中會先具現化 `PageRenderer` 類別，再由該類別具現化原生 `UIViewController` 控制項。 在 Android 平台上，`PageRenderer` 類別會具現化 `ViewGroup` 控制項。 在通用 Windows 平台 (UWP) 上，`PageRenderer` 類別會具現化 `FrameworkElement` 控制項。 如需 Xamarin.Forms 控制項對應之轉譯器和原生控制項類別的詳細資訊，請參閱[轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 和實作它之對應原生控制項間的關聯性：

![](contentpage-images/contentpage-classes.png "ContentPage 類別與實作原生控制項之間的關聯性")

您可在每個平台上建立 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 的自訂轉譯器，利用轉譯程序實作平台特定的自訂。 執行這項作業的程序如下：

1. [建立](#Creating_the_Xamarin.Forms_Page) Xamarin.Forms 頁面。
1. 從 Xamarin.Forms [使用](#Consuming_the_Xamarin.Forms_Page)頁面。
1. 在每個平台上[建立](#Creating_the_Page_Renderer_on_each_Platform)頁面的自訂轉譯器。

現在將依序討論每個項目，以實作 `CameraPage` 提供即時相機播放和拍照功能。

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>建立 Xamarin.Forms 頁面

您可以將 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 原封不動地新增至共用的 Xamarin.Forms 專案，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

同樣地，[`ContentPage`](xref:Xamarin.Forms.ContentPage) 的程式碼後置檔案也應該保持不變，如下列程式碼範例所示：

```csharp
public partial class CameraPage : ContentPage
{
    public CameraPage ()
    {
        // A custom renderer is used to display the camera UI
        InitializeComponent ();
    }
}
```

下列程式碼範例示範如何在 C# 中建立頁面：

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

每個平台上都會使用 `CameraPage` 執行個體來顯示即時相機播放。 控制項的自訂作業會在自訂轉譯器中完成，因此不需要在 `CameraPage` 類別中進行其他實作。

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>使用 Xamarin.Forms 頁面

Xamarin.Forms 應用程式必須顯示空白 `CameraPage`。 此動作發生於點選 `MainPage` 執行個體上的按鈕時，並會接著執行 `OnTakePhotoButtonClicked` 方法，如下列程式碼範例所示：

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

此程式碼只會巡覽至 `CameraPage`，自訂轉譯器會在上方自訂每個平台上的頁面外觀。

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>在每個平台上建立頁面轉譯器

建立自訂轉譯器類別的程序如下：

1. 建立 `PageRenderer` 類別的子類別。
1. 覆寫轉譯原生頁面的 `OnElementChanged` 方法，並撰寫自訂頁面的邏輯。 `OnElementChanged` 方法是在建立對應的 Xamarin.Forms 控制項時呼叫。
1. 將 `ExportRenderer` 屬性新增至頁面轉譯器類別，指定用它轉譯 Xamarin.Forms 頁面。 這個屬性用來向 Xamarin.Forms 註冊自訂轉譯器。

> [!NOTE]
> 您可以選擇每個平台專案是否提供頁面轉譯器。 如果頁面轉譯器未註冊，則會使用頁面的預設轉譯器。

下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](contentpage-images/solution-structure.png "CameraPage 自訂轉譯器專案責任")

`CameraPage` 執行個體是由平台特定 `CameraPageRenderer` 類別轉譯，其全部衍生自平台的 `PageRenderer` 類別。 這會導致每個 `CameraPage` 執行個體都會透過即時相機播放轉譯，如下列螢幕擷取畫面所示：

![](contentpage-images/screenshots.png "每個平台上的 CameraPage")

`PageRenderer` 類別會公開 `OnElementChanged` 方法，在建立 Xamarin.Forms 頁面以轉譯對應的原生控制項時，便會呼叫此方法。 這個方法採用 `ElementChangedEventArgs` 參數，其中包含 `OldElement` 和 `NewElement` 屬性。 這些屬性分別代表轉譯器「過去」所附加的 Xamarin.Forms 項目，以及「現在」所附加的 Xamarin.Forms 項目。 在範例應用程式中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `CameraPage` 執行個體的參考。

在 `CameraPageRenderer` 類別中，`OnElementChanged` 方法的覆寫版本是執行原生頁面自訂的位置。 所要轉譯 Xamarin.Forms 頁面執行個體的參考可透過 `Element` 屬性取得。

每個自訂轉譯器類別都裝飾了向 Xamarin.Forms 註冊轉譯器的 `ExportRenderer` 屬性。 此屬性採用兩個參數：正在轉譯的 Xamarin.Forms 頁面類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

下列各節會討論適用於每個平台的 `CameraPageRenderer` 自訂轉譯器實作。

### <a name="creating-the-page-renderer-on-ios"></a>在 iOS 上建立頁面轉譯器

下列程式碼範例示範適用於 iOS 平台的頁面轉譯器：

```csharp
[assembly:ExportRenderer (typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.iOS
{
    public class CameraPageRenderer : PageRenderer
    {
        ...

        protected override void OnElementChanged (VisualElementChangedEventArgs e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null || Element == null) {
                return;
            }

            try {
                SetupUserInterface ();
                SetupEventHandlers ();
                SetupLiveCameraStream ();
                AuthorizeCameraUse ();
            } catch (Exception ex) {
                System.Diagnostics.Debug.WriteLine (@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

基底類別的 `OnElementChanged` 方法呼叫會具現化 iOS `UIViewController` 控制項。 只有轉譯器尚未附加至任何現有的 Xamarin.Forms 項目，而且存在自訂轉譯器所要轉譯的頁面執行個體時，才會轉譯即時相機資料流。

該頁面會接著透過一連串使用 `AVCapture` API 的方法來自訂，以從相機提供即時資料流並提供拍照功能。

### <a name="creating-the-page-renderer-on-android"></a>在 Android 上建立頁面轉譯器

下列程式碼範例示範適用於 Android 平台的頁面轉譯器：

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
    {
        ...
        public CameraPageRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetupUserInterface();
                SetupEventHandlers();
                AddView(view);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(@"            ERROR: ", ex.Message);
            }
        }
        ...
    }
}
```

基底類別的 `OnElementChanged` 方法呼叫會具現化 Android `ViewGroup` 控制項，這會是一組檢視。 只有轉譯器尚未附加至任何現有的 Xamarin.Forms 項目，而且存在自訂轉譯器所要轉譯的頁面執行個體時，才會轉譯即時相機資料流。

該頁面會接著透過叫用一連串使用 `Camera` API 的方法自訂，以從相機提供即時資料流並提供拍照功能，再叫用 `AddView` 方法，將即時相機資料流 UI 新增至 `ViewGroup`。 請注意，在 Android 上，若要對檢視執行測量和配置作業，還必須覆寫 `OnLayout` 方法。 如需詳細資訊，請參閱 [ContentPage 轉譯器範例](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)。

### <a name="creating-the-page-renderer-on-uwp"></a>在 UWP 上建立頁面轉譯器

下列程式碼範例示範適用於 UWP 的頁面轉譯器：

```csharp
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace CustomRenderer.UWP
{
    public class CameraPageRenderer : PageRenderer
    {
        ...
        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                ...
                SetupUserInterface();
                SetupBasedOnStateAsync();

                this.Children.Add(page);
            }
            ...
        }

        protected override Size ArrangeOverride(Size finalSize)
        {
            page.Arrange(new Windows.Foundation.Rect(0, 0, finalSize.Width, finalSize.Height));
            return finalSize;
        }
        ...
    }
}

```

基底類別的 `OnElementChanged` 方法呼叫會具現化 iOS `FrameworkElement` 控制項，再於上方轉譯頁面。 只有轉譯器尚未附加至任何現有的 Xamarin.Forms 項目，而且存在自訂轉譯器所要轉譯的頁面執行個體時，才會轉譯即時相機資料流。 該頁面會接著透過叫用一連串使用 `MediaCapture` API 的方法自訂，以從相機提供即時資料流並提供拍照功能，再將自訂頁面新增至 `Children` 集合以便顯示。

在 UWP 上實作衍生自 `PageRenderer`的自訂轉譯器時，還應該實作 `ArrangeOverride` 方法來排列頁面控制項，因為基底轉譯器並不知道要如何處理。 否則會產生空白頁。 因此，在此範例中，`ArrangeOverride` 方法會在 `Page` 執行個體上呼叫 `Arrange` 方法。

> [!NOTE]
> 請務必停止並處置提供 UWP 應用程式中相機存取權的物件。 若未這樣做，則可能會干擾嘗試存取裝置相機的其他應用程式。 如需詳細資訊，請參閱[顯示相機預覽](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access)。

## <a name="summary"></a>總結

本文示範了如何建立 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 頁面的自訂轉譯器，讓開發人員以自己的平台特定自訂來覆寫預設原生轉譯。 `ContentPage` 是可見的項目，會顯示單一檢視，並佔用螢幕的大部分空間。


## <a name="related-links"></a>相關連結

- [CustomRenderer Content Page (Samples)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/) (CustomRenderer 內容頁面 (範例))

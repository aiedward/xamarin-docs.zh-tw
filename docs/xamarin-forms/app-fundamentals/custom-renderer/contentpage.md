---
title: 自訂 ContentPage
description: ContentPage 是螢幕的可見的項目，可顯示的單一檢視，並佔用大部分。 這篇文章會示範如何建立自訂轉譯器的 ContentPage 頁面上，讓開發人員覆寫預設原生呈現自己的平台特定自訂。
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 2369b249681b926476cf3938c51c99745eba9098
ms.sourcegitcommit: 8888cb7d75f4469f2a1195b9a426a2e1fbf46bd8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2018
ms.locfileid: "38995738"
---
# <a name="customizing-a-contentpage"></a>自訂 ContentPage

_ContentPage 是螢幕的可見的項目，可顯示的單一檢視，並佔用大部分。這篇文章會示範如何建立自訂轉譯器的 ContentPage 頁面上，讓開發人員覆寫預設原生呈現自己的平台特定自訂。_

每個 Xamarin.Forms 控制項已隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `ContentPage` ](xref:Xamarin.Forms.ContentPage) Xamarin.Forms 應用程式，在 iOS 中呈現`PageRenderer`類別具現化，以依序具現化原生`UIViewController`控制項。 Android 平台上，`PageRenderer`類別會具現化`ViewGroup`控制項。 在通用 Windows 平台 (UWP)，`PageRenderer`類別會具現化`FrameworkElement`控制項。 如需有關轉譯器和 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱 <<c0> [ 轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)和對應的原生控制項實作它：

![](contentpage-images/contentpage-classes.png "ContentPage 類別，並實作原生控制項之間的關聯性")

轉譯程序可以藉由建立自訂轉譯器的實作平台專屬的自訂採取善用[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)每個平台。 執行此動作的程序如下所示：

1. [建立](#Creating_the_Xamarin.Forms_Page)Xamarin.Forms 頁面。
1. [取用](#Consuming_the_Xamarin.Forms_Page)Xamarin.Forms 的頁面。
1. [建立](#Creating_the_Page_Renderer_on_each_Platform)在頁面上每個平台上的自訂轉譯器。

每個項目會現在依次討論實作`CameraPage`提供即時的相機摘要，並且能夠擷取相片。

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>建立 Xamarin.Forms 頁面

更改[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)可以共用的 Xamarin.Forms 專案中，加入下列 XAML 程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

同樣地，程式碼後置檔案[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)應該也維持不變，如下列程式碼範例所示：

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

下列程式碼範例顯示如何在 C# 中建立的頁面：

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

執行個體`CameraPage`用以顯示即時摘要每個平台上的相機。 自訂控制項的執行時，自訂轉譯器，因此沒有其他的實作需要用於`CameraPage`類別。

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>使用 Xamarin.Forms 頁面

空白`CameraPage`Xamarin.Forms 應用程式必須顯示。 上的按鈕時，發生這種的情況`MainPage`點選執行個體時，它會接著執行`OnTakePhotoButtonClicked`方法，如下列程式碼範例所示：

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

此程式碼只是瀏覽至`CameraPage`上的自訂轉譯器會自訂每個平台上的網頁的外觀。

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>每個平台上建立頁面轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`PageRenderer`類別。
1. 覆寫`OnElementChanged`呈現的原生的頁面並寫入邏輯自訂頁面的方法。 `OnElementChanged`建立相對應的 Xamarin.Forms 控制項時，會呼叫方法。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 頁面的頁面轉譯器類別。 這個屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 它是選擇性的以提供每個平台專案中的頁面轉譯器。 如果未登錄頁面轉譯器，則會使用預設的轉譯器的頁面。

下圖說明範例應用程式，以及它們之間的關聯性中的每個專案的責任：

![](contentpage-images/solution-structure.png "CameraPage 自訂轉譯器專案責任")

`CameraPage`執行個體由平台特定呈現`CameraPageRenderer`類別，這些全都衍生自`PageRenderer`該平台的類別。 這會導致每個`CameraPage`執行個體所呈現使用相機的即時摘要，如下列螢幕擷取畫面所示：

![](contentpage-images/screenshots.png "每個平台的 CameraPage")

`PageRenderer`類別會公開`OnElementChanged`Xamarin.Forms 頁面建立以呈現對應的原生控制項時呼叫的方法。 這個方法會採用`ElementChangedEventArgs`參數，其中包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加至，和 Xamarin.Forms 的項目，轉譯器*是*附加分別。 在範例應用程式`OldElement`屬性會是`null`並`NewElement`屬性會包含參考`CameraPage`執行個體。

覆寫的新版`OnElementChanged`方法中的`CameraPageRenderer`類別可供執行原生頁面自訂。 所呈現的 Xamarin.Forms 頁面執行個體的參考可以透過取得`Element`屬性。

每個自訂轉譯器類別裝飾了`ExportRenderer`向 Xamarin.Forms 中的轉譯器的屬性。 屬性會採用兩個參數-Xamarin.Forms 頁面所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列各節討論實作`CameraPageRenderer`每個平台的自訂轉譯器。

### <a name="creating-the-page-renderer-on-ios"></a>在 iOS 上建立頁面轉譯器

下列程式碼範例會顯示頁面轉譯器，適用於 iOS 平台：

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

呼叫基底類別的`OnElementChanged`方法具現化 iOS`UIViewController`控制項。 提供，轉譯器不已經附加到現有的 Xamarin.Forms 元素，前提是 page 執行個體存在，會呈現的自訂轉譯器，則只會呈現即時的相機串流。

然後由一系列的方法，使用自訂頁面`AVCapture`Api 用於提供來自相機，並且能夠擷取相片的即時資料流。

### <a name="creating-the-page-renderer-on-android"></a>在 Android 上建立頁面轉譯器

下列程式碼範例會顯示 Android 平台的頁面轉譯器：

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

呼叫基底類別的`OnElementChanged`方法具現化 Android`ViewGroup`控制項，也就是檢視的群組。 提供，轉譯器不已經附加到現有的 Xamarin.Forms 元素，前提是 page 執行個體存在，會呈現的自訂轉譯器，則只會呈現即時的相機串流。

然後藉由叫用一系列的方法，使用自訂頁面`Camera`API，以提供即時資料流從相機，並且能夠擷取相片，之前`AddView`方法會叫用來新增即時的相機串流處理至 UI `ViewGroup`。 請注意，在 Android 上也需要覆寫`OnLayout`方法執行量值和版面配置檢視上的作業。 如需詳細資訊，請參閱 < [ContentPage 轉譯器範例](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)。

### <a name="creating-the-page-renderer-on-uwp"></a>建立 UWP 上的頁面轉譯器

下列程式碼範例顯示適用於 UWP 的頁面轉譯器：

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

呼叫基底類別的`OnElementChanged`方法具現化`FrameworkElement`呈現網頁時的控制項。 提供，轉譯器不已經附加到現有的 Xamarin.Forms 元素，前提是 page 執行個體存在，會呈現的自訂轉譯器，則只會呈現即時的相機串流。 然後藉由叫用一系列的方法，使用自訂頁面`MediaCapture`API，以提供即時資料流，從數位相機，並且能夠擷取相片，加入自訂的頁面之前`Children`顯示的集合。

當實作自訂轉譯器衍生自`PageRenderer`UWP 上`ArrangeOverride`方法應該也實作，以排列網頁的控制項，因為基底的轉譯器不知道該如何處理它們。 否則，會產生空白頁面。 因此，在此範例中`ArrangeOverride`方法呼叫`Arrange`方法`Page`執行個體。

> [!NOTE]
> 請務必停止並處置的物件，提供存取權的 UWP 應用程式之觀景窗。 若要這樣做的失敗可能會干擾其他應用程式嘗試存取裝置的相機。 如需詳細資訊，請參閱 <<c0> [ 顯示相機預覽](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access)。

## <a name="summary"></a>總結

這篇文章已示範如何建立自訂轉譯器[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)頁面上，讓開發人員覆寫預設原生呈現自己的平台特定自訂。 A`ContentPage`是可見的項目，可顯示的單一檢視，並佔用大部分的螢幕。


## <a name="related-links"></a>相關連結

- [CustomRendererContentPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)

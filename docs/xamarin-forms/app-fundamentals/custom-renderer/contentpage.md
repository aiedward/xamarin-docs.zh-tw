---
title: 自訂 ContentPage
description: ContentPage 是螢幕的視覺化的項目會顯示在單一檢視，而且會佔用大部分。 本文示範如何建立自訂轉譯器的 ContentPage 頁面上，讓開發人員覆寫預設原生呈現使用他們自己平台專屬的自訂。
ms.prod: xamarin
ms.assetid: A4E61D93-73D9-4668-8D1C-DB6FC2491822
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 5fe7250b5b8fcea97d4fbe6846999be60e8e8673
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848170"
---
# <a name="customizing-a-contentpage"></a>自訂 ContentPage

_ContentPage 是螢幕的視覺化的項目會顯示在單一檢視，而且會佔用大部分。本文示範如何建立自訂轉譯器的 ContentPage 頁面上，讓開發人員覆寫預設原生呈現使用他們自己平台專屬的自訂。_

Xamarin.Forms 中的每個控制項都有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) Xamarin.Forms 應用程式，在 iOS 中呈現`PageRenderer`類別具現化，這又會具現化原生`UIViewController`控制項。 Android 平台上，`PageRenderer`類別具現化`ViewGroup`控制項。 在通用 Windows 平台 (UWP)，`PageRenderer`類別具現化`FrameworkElement`控制項。 如需有關轉譯器，而且 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱[轉譯器的基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)和對應的原生控制項實作它：

![](contentpage-images/contentpage-classes.png "ContentPage 類別和實作的原生控制項之間的關聯性")

轉譯程序可以採取利用平台專屬的自訂實作所建立的自訂轉譯器[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)每個平台上。 執行此程序如下所示：

1. [建立](#Creating_the_Xamarin.Forms_Page)Xamarin.Forms 頁面。
1. [取用](#Consuming_the_Xamarin.Forms_Page)Xamarin.Forms 頁面。
1. [建立](#Creating_the_Page_Renderer_on_each_Platform)在頁面上每個平台上的自訂轉譯器。

每個項目將現在依次討論實作`CameraPage`，提供摘要的即時相機與擷取相片的能力。

<a name="Creating_the_Xamarin.Forms_Page" />

## <a name="creating-the-xamarinforms-page"></a>建立 Xamarin.Forms 頁面

未改變[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)可以共用 Xamarin.Forms 專案中，加入下列 XAML 程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CustomRenderer.CameraPage">
    <ContentPage.Content>
    </ContentPage.Content>
</ContentPage>
```

同樣地，程式碼後置檔案[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)應該也都保持不變，如下列程式碼範例所示：

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

下列程式碼範例示範如何在 C# 中建立的頁面：

```csharp
public class CameraPageCS : ContentPage
{
    public CameraPageCS ()
    {
    }
}
```

執行個體`CameraPage`會用來顯示即時摘要每個平台上的相機。 自訂控制項的執行時，自訂轉譯器，因此沒有額外的實作需要用於`CameraPage`類別。

<a name="Consuming_the_Xamarin.Forms_Page" />

## <a name="consuming-the-xamarinforms-page"></a>使用 Xamarin.Forms 頁面

空白`CameraPage`Xamarin.Forms 應用程式必須顯示。 這發生在上的按鈕`MainPage`點選執行個體，它會接著執行`OnTakePhotoButtonClicked`方法，如下列程式碼範例所示：

```csharp
async void OnTakePhotoButtonClicked (object sender, EventArgs e)
{
    await Navigation.PushAsync (new CameraPage ());
}
```

此程式碼直接巡覽至`CameraPage`，對於哪些自訂轉譯器將會自訂每個平台上的網頁的外觀。

<a name="Creating_the_Page_Renderer_on_each_Platform" />

## <a name="creating-the-page-renderer-on-each-platform"></a>每個平台上建立頁面轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`PageRenderer`類別。
1. 覆寫`OnElementChanged`呈現的原生的頁面和寫入邏輯自訂頁面的方法。 `OnElementChanged`方法建立對應的 Xamarin.Forms 控制項時呼叫。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 頁面的頁面轉譯器類別。 此屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 它是選擇性的以提供每個平台專案中的頁面轉譯器。 如果未登錄頁面轉譯器，將使用預設的產生器頁面。

下圖說明範例應用程式，以及它們之間的關聯性中的每一個專案的責任：

![](contentpage-images/solution-structure.png "CameraPage 自訂轉譯器專案責任")

`CameraPage`執行個體由特定平台呈現`CameraPageRenderer`類別全都衍生自`PageRenderer`該平台的類別。 這會導致每個`CameraPage`執行個體所呈現使用即時相機摘要中的下列螢幕擷取畫面所示：

![](contentpage-images/screenshots.png "每個平台上 CameraPage")

`PageRenderer`類別會公開`OnElementChanged`Xamarin.Forms 頁面是用來呈現對應的原生控制項時所呼叫的方法。 這個方法會採用`ElementChangedEventArgs`參數包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加和 Xamarin.Forms 的項目，轉譯器*是*附加至分別。 範例應用程式中`OldElement`屬性會是`null`和`NewElement`屬性會包含參考`CameraPage`執行個體。

覆寫的版本`OnElementChanged`方法中的`CameraPageRenderer`類別會用於執行原生頁面自訂。 可以透過取得到所呈現的 Xamarin.Forms 頁面執行個體的參考`Element`屬性。

每個自訂轉譯器類別以裝飾`ExportRenderer`xamarin.forms 註冊轉譯器的屬性。 屬性會採用兩個參數 – Xamarin.Forms 頁面所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列章節會討論的實作`CameraPageRenderer`每個平台的自訂轉譯器。

### <a name="creating-the-page-renderer-on-ios"></a>在 iOS 上建立頁面轉譯器

下列程式碼範例會顯示 iOS 平台的頁面轉譯器：

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

基底類別呼叫`OnElementChanged`方法具現化 iOS`UIViewController`控制項。 轉譯器不已經附加到現有的 Xamarin.Forms 項目，並假設頁面執行個體存在，會呈現的自訂轉譯器，只會轉譯相機即時資料流。

然後由一系列的方法使用的自訂頁面`AVCapture`Api 用於提供相機，並且能夠擷取相片的即時資料流。

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

基底類別呼叫`OnElementChanged`方法具現化 Android`ViewGroup`控制項，這是檢視的群組。 轉譯器不已經附加到現有的 Xamarin.Forms 項目，並假設頁面執行個體存在，會呈現的自訂轉譯器，只會轉譯相機即時資料流。

然後叫用一系列的方法使用的自訂頁面`Camera`API 來提供即時資料流，從網路攝影機和之前擷取相片的能力`AddView`方法會叫用來新增即時相機串流 UI `ViewGroup`。

### <a name="creating-the-page-renderer-on-uwp"></a>在 UWP 上建立頁面轉譯器

下列程式碼範例示範 UWP 的頁面轉譯器：

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

基底類別呼叫`OnElementChanged`方法具現化`FrameworkElement`呈現網頁的控制項。 轉譯器不已經附加到現有的 Xamarin.Forms 項目，並假設頁面執行個體存在，會呈現的自訂轉譯器，只會轉譯相機即時資料流。 然後叫用一系列的方法使用的自訂頁面`MediaCapture`API 來提供即時資料流，從網路攝影機和自訂的頁面加入至之前擷取相片的能力`Children`顯示的集合。

實作衍生自的自訂轉譯器時`PageRenderer`上 UWP、`ArrangeOverride`方法應該也排列頁面控制項實作，因為基底的轉譯器不知道該如何處理它們。 否則，會產生空白頁面。 因此，在此範例`ArrangeOverride`方法呼叫`Arrange`方法`Page`執行個體。

> [!NOTE]
> 請務必停止與處置的物件可提供存取的相機 UWP 應用程式中。 這樣可能會干擾其他應用程式嘗試存取裝置的相機。 如需詳細資訊，請參閱[顯示相機預覽](https://msdn.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access)。

## <a name="summary"></a>總結

本文示範如何建立自訂轉譯器[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)頁面上，讓開發人員覆寫預設原生呈現使用他們自己平台專屬的自訂。 A`ContentPage`是視覺化的項目會顯示在單一檢視，而且會佔用大部分的螢幕。


## <a name="related-links"></a>相關連結

- [CustomRendererContentPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/contentpage/)

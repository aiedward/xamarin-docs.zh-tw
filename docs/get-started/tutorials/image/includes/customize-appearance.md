---
ms.openlocfilehash: 58ed33adbc25e026431609370075c24c3b6ba690
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574657"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`Image`](xref:Xamarin.Forms.Image) 宣告，以自訂其外觀：

    ```xaml
    <Image Source="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    此程式碼會將 [`Aspect`](xref:Xamarin.Forms.Image.Aspect) 屬性設定為 [`Fill`](xref:Xamarin.Forms.Aspect.Fill)，該屬性可定義影像的縮放模式。 `Fill` 成員定義於 [`Aspect`](xref:Xamarin.Forms.Aspect) 列舉中，可延展影像以完全填滿檢視 (不論影像是否扭曲)。 如需影像縮放的詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md#display-images)指南中的[顯示影像](~/xamarin-forms/user-interface/images.md)。

    `OnPlatform` 標記延伸可讓您自訂每個平台的 UI 外觀。 在此範例中，標記延伸用於將 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性設定為 300 個裝置獨立單位 (在 iOS 上) 和 250 個裝置獨立單位 (在 Android 上)。 如需 `OnPlatform` 標記延伸的詳細資訊，請參閱[取用 XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)指南中的 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md)。

    此外，[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性可指定將影像水平置中。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 以不同方式調整影像大小的螢幕擷取畫面](../images/customize-appearance.png "依平台調整影像的大小")](../images/customize-appearance-large.png#lightbox "依平台調整影像的大小")

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`Image`](xref:Xamarin.Forms.Image) 宣告，以自訂其外觀：

    ```xaml
    <Image Source="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    此程式碼會將 [`Aspect`](xref:Xamarin.Forms.Image.Aspect) 屬性設定為 [`Fill`](xref:Xamarin.Forms.Aspect.Fill)，該屬性可定義影像的縮放模式。 `Fill` 成員定義於 [`Aspect`](xref:Xamarin.Forms.Aspect) 列舉中，可延展影像以完全填滿檢視 (不論影像是否扭曲)。 如需影像縮放的詳細資訊，請參閱 [Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md#display-images)指南中的[顯示影像](~/xamarin-forms/user-interface/images.md)。

    `OnPlatform` 標記延伸可讓您自訂每個平台的 UI 外觀。 在此範例中，標記延伸用於將 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性設定為 300 (在 iOS 上) 和 250 (在 Android 上)。 如需 `OnPlatform` 標記延伸的詳細資訊，請參閱[取用 XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)指南中的 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md)。

    此外，[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性可指定將影像水平置中。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![iOS 和 Android 以不同方式調整影像大小的螢幕擷取畫面](../images/customize-appearance.png "依平台調整影像的大小")](../images/customize-appearance-large.png#lightbox "依平台調整影像的大小")

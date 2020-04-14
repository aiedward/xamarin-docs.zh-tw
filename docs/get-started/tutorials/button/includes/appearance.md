---
ms.openlocfilehash: 1d2bed830af97ce1ff329a5396a415247a43189d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "61372910"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`Button`](xref:Xamarin.Forms.Button) 宣告，以變更其視覺外觀：

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    此程式碼會設定可變更 [`Button`](xref:Xamarin.Forms.Button) 視覺外觀的屬性。 [`TextColor`](xref:Xamarin.Forms.Button.TextColor) 屬性會設定 `Button` 文字的色彩，[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 屬性則會設定文字背景的色彩。 [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 屬性會設定 `Button` 周圍區域的色彩，[`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 屬性則會設定框線的寬度。 根據預設，`Button` 是矩形的，但您可以藉由將 [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) 屬性設定為適當的值來賦予其圓角。 此外，`Button` 的大小可藉由設定其 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性來加以變更。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 請觀察一下，[`Button`](xref:Xamarin.Forms.Button) 外觀已變更：

    [![螢幕擷取畫面：在 iOS 和 Android 上，視覺外觀已變更的 Button](../images/change-button-appearance.png "外觀已變更的 Button")](../images/change-button-appearance-large.png#lightbox "外觀已變更的 Button")

    如需如何設定 [`Button`](xref:Xamarin.Forms.Button) 外觀的詳細資訊，請參閱 [Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md#button-appearance)指南中的[按鈕外觀](~/xamarin-forms/user-interface/button.md)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`Button`](xref:Xamarin.Forms.Button) 宣告，以變更其視覺外觀：

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    此程式碼會設定可變更 [`Button`](xref:Xamarin.Forms.Button) 視覺外觀的屬性。 [`TextColor`](xref:Xamarin.Forms.Button.TextColor) 屬性會設定 `Button` 文字的色彩，[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 屬性則會設定文字背景的色彩。 [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 屬性會設定 `Button` 周圍區域的色彩，[`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 屬性則會設定框線的寬度。 根據預設，`Button` 是矩形的，但您可以藉由將 [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) 屬性設定為適當的值來賦予其圓角。 此外，`Button` 的大小可藉由設定其 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性來加以變更。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式。 請觀察一下，[`Button`](xref:Xamarin.Forms.Button) 外觀已變更：

    [![螢幕擷取畫面：在 iOS 和 Android 上，視覺外觀已變更的 Button](../images/change-button-appearance.png "外觀已變更的 Button")](../images/change-button-appearance-large.png#lightbox "外觀已變更的 Button")

    如需如何設定 [`Button`](xref:Xamarin.Forms.Button) 外觀的詳細資訊，請參閱 [Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md#button-appearance)指南中的[按鈕外觀](~/xamarin-forms/user-interface/button.md)。

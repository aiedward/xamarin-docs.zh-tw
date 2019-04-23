---
ms.openlocfilehash: 4fc6c50b5aa2ce502b4157ca2b15f0d33a68ecd1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896654"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`Label`](xref:Xamarin.Forms.Label) 宣告，以變更其視覺外觀：

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    此程式碼會設定可變更 [`Label`](xref:Xamarin.Forms.Label) 視覺外觀的屬性。 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 屬性會設定 `Button` 文字的色彩。 [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) 屬性會將標籤的字型設定為斜體，[`FontSize`](xref:Xamarin.Forms.Label.FontSize) 屬性則會設定字型大小。 此外，若要對 `Label` 套用底線文字裝飾，可透過設定其 [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations) 屬性來完成，若要讓其水平置中，則可透過將 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設定為 [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 請觀察一下，[`Label`](xref:Xamarin.Forms.Label) 外觀已變更：

    [![螢幕擷取畫面：iOS 和 Android 上視覺外觀已變更的標籤](../images/change-label-appearance.png "外觀已變更的標籤")](../images/change-label-appearance-large.png#lightbox "外觀已變更的標籤")

    如需如何設定 [`Label`](xref:Xamarin.Forms.Label) 外觀的詳細資訊，請參閱 [Xamarin.Forms 標籤](~/xamarin-forms/user-interface/text/label.md)指南。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`Label`](xref:Xamarin.Forms.Label) 宣告，以變更其視覺外觀：

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    此程式碼會設定可變更 [`Label`](xref:Xamarin.Forms.Label) 視覺外觀的屬性。 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 屬性會設定 `Button` 文字的色彩。 [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) 屬性會將標籤的字型設定為斜體，[`FontSize`](xref:Xamarin.Forms.Label.FontSize) 屬性則會設定字型大小。 此外，若要對 `Label` 套用底線文字裝飾，可透過設定其 [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations) 屬性來完成，若要讓其水平置中，則可透過將 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設定為 [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式。 請觀察一下，[`Label`](xref:Xamarin.Forms.Label) 外觀已變更：

    [![螢幕擷取畫面：iOS 和 Android 上視覺外觀已變更的標籤](../images/change-label-appearance.png "外觀已變更的標籤")](../images/change-label-appearance-large.png#lightbox "外觀已變更的標籤")

    如需如何設定 [`Label`](xref:Xamarin.Forms.Label) 外觀的詳細資訊，請參閱 [Xamarin.Forms 標籤](~/xamarin-forms/user-interface/text/label.md)指南。

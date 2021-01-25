---
ms.openlocfilehash: 4352ff38e785a0a222cf8d2d64511ed14b3ae192
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689644"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中，修改 [`Label`](xref:Xamarin.Forms.Label) 宣告以在單一 `Label` 中呈現使用多種格式的文字。

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    此程式碼會在單一 [`Label`](xref:Xamarin.Forms.Label) 中顯示使用多種格式的文字。 第一個 [`Span`](xref:Xamarin.Forms.Span) 中的文字會使用 `Label` 上所設定的格式來顯示，第二個和第三個 `Span` 執行個體中的文字則會使用 `Label` 上所設定的格式以及每個 `Span` 上所設定的其他格式來顯示。

    > [!NOTE]
    > [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) 屬性的類型是 [`FormattedString`](xref:Xamarin.Forms.FormattedString)，其包含一或多個 [`Span`](xref:Xamarin.Forms.Span) 執行個體。

1. 如果應用程式仍在執行，請將變更儲存至檔案，應用程式使用者介面將會在您的模擬器中自動更新。 否則，請在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 請觀察一下，[`Label`](xref:Xamarin.Forms.Label) 外觀已變更：

    [![螢幕擷取畫面：iOS 和 Android 上顯示格式化文字的 Label](../images/label-formatted-text.png "具有格式化文字的 Label")](../images/label-formatted-text-large.png#lightbox "具有格式化文字的 Label")

    在 Visual Studio 中，停止應用程式。

    如需如何設定 [`Span`](xref:Xamarin.Forms.Span) 外觀的詳細資訊，請參閱 [Xamarin.Forms 標籤](~/xamarin-forms/user-interface/text/label.md)指南中的[格式化文字](~/xamarin-forms/user-interface/text/label.md#formatted-text)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中，修改 [`Label`](xref:Xamarin.Forms.Label) 宣告以在單一 `Label` 中呈現使用多種格式的文字。

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    此程式碼會在單一 [`Label`](xref:Xamarin.Forms.Label) 中顯示使用多種格式的文字。 第一個 [`Span`](xref:Xamarin.Forms.Span) 中的文字會使用 `Label` 上所設定的格式來顯示，第二個和第三個 `Span` 執行個體中的文字則會使用 `Label` 上所設定的格式以及每個 `Span` 上所設定的其他格式來顯示。

    > [!NOTE]
    > [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) 屬性的類型是 [`FormattedString`](xref:Xamarin.Forms.FormattedString)，其包含一或多個 [`Span`](xref:Xamarin.Forms.Span) 執行個體。

1. 如果應用程式仍在執行，請將變更儲存至檔案，應用程式使用者介面將會在您的模擬器中自動更新。 否則，請在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 請觀察一下，[`Label`](xref:Xamarin.Forms.Label) 外觀已變更：

    [![螢幕擷取畫面：iOS 和 Android 上顯示格式化文字的 Label](../images/label-formatted-text.png "具有格式化文字的 Label")](../images/label-formatted-text-large.png#lightbox "具有格式化文字的 Label")

    在 Visual Studio for Mac 中，停止應用程式。

    如需如何設定 [`Span`](xref:Xamarin.Forms.Span) 外觀的詳細資訊，請參閱 [Xamarin.Forms 標籤](~/xamarin-forms/user-interface/text/label.md)指南中的[格式化文字](~/xamarin-forms/user-interface/text/label.md#formatted-text)。

---
ms.openlocfilehash: 78c83b39107544b6b5358efcacc622a96d49e8b1
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98690206"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`Editor`](xref:Xamarin.Forms.Editor) 宣告，以自訂其行為：

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    此程式碼會設定屬性以自訂 [`Editor`](xref:Xamarin.Forms.Editor) 的行為。 [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) 屬性會設定為 [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)，這表示 `Editor` 的高度會在填滿文字時增加，移除文字時減少。 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 屬性會限制 `Editor` 可允許的輸入長度。 此外，[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性會設定為 `false` 以停用拼字檢查，`IsTextPredictionEnabled` 屬性則會設定為 `false` 以停用文字預測和自動文字預測。

1. 如果應用程式仍在執行，請將變更儲存至檔案，應用程式使用者介面將會在您的模擬器中自動更新。 否則，請在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 將文字輸入 [`Editor`](xref:Xamarin.Forms.Entry)，並觀察一下，`Editor` 的高度會在填滿文字時增加，移除文字時減少，而且可以輸入的字元數目上限為 200 個字元：

    [![螢幕擷取畫面：iOS 和 Android 上的自動調整大小 Editor](../images/customize-behavior.png "自動調整大小 Editor")](../images/customize-behavior-large.png#lightbox "自動調整大小 Editor")

    在 Visual Studio 中，停止應用程式。

    如需有關自訂 [`Editor`](xref:Xamarin.Forms.Editor) 行為的詳細資訊，請參閱 [Xamarin.Forms 編輯器](~/xamarin-forms/user-interface/text/editor.md)指南。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`Editor`](xref:Xamarin.Forms.Editor) 宣告，以自訂其行為：

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    此程式碼會設定屬性以自訂 [`Editor`](xref:Xamarin.Forms.Editor) 的行為。 [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) 屬性會設定為 [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)，這表示 `Editor` 的高度會在填滿文字時增加，移除文字時減少。 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 屬性會限制 `Editor` 可允許的輸入長度。 此外，[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性會設定為 `false` 以停用拼字檢查，`IsTextPredictionEnabled` 屬性則會設定為 `false` 以停用文字預測和自動文字預測。

1. 如果應用程式仍在執行，請將變更儲存至檔案，應用程式使用者介面將會在您的模擬器中自動更新。 否則，請在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 將文字輸入 [`Editor`](xref:Xamarin.Forms.Entry)，並觀察一下，`Editor` 的高度會在填滿文字時增加，移除文字時減少，而且可以輸入的字元數目上限為 200 個字元：

    [![螢幕擷取畫面：iOS 和 Android 上的自動調整大小 Editor](../images/customize-behavior.png "自動調整大小 Editor")](../images/customize-behavior-large.png#lightbox "自動調整大小 Editor")

    在 Visual Studio for Mac 中，停止應用程式。

    如需有關自訂 [`Editor`](xref:Xamarin.Forms.Editor) 行為的詳細資訊，請參閱 [Xamarin.Forms 編輯器](~/xamarin-forms/user-interface/text/editor.md)指南。

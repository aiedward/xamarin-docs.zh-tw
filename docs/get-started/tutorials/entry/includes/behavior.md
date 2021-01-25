---
ms.openlocfilehash: f4fccbfe7e00a353682b0dfe787c291b38e5cd76
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689868"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`Entry`](xref:Xamarin.Forms.Entry) 宣告，以自訂其行為：

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    此程式碼會設定屬性以自訂 [`Entry`](xref:Xamarin.Forms.Entry) 的行為。 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 屬性會限制 `Entry` 可允許的輸入長度，而 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性會設定為 `false` 以停用拼字檢查。 同樣地，[`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) 屬性會設定為 `false`，以停用文字預測和自動文字預測。 此外，[`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) 屬性可確保輸入的字元會以密碼字元遮罩 (黑色圓圈)。

    > [!NOTE]
    > 在某些文字輸入的案例中 (例如輸入密碼)，拼字檢查和文字預測上會出現負面的體驗，因此應該停用。

1. 如果應用程式仍在執行，請將變更儲存至檔案，應用程式使用者介面將會在您的模擬器中自動更新。 否則，請在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 將文字輸入 [`Entry`](xref:Xamarin.Forms.Entry)，您將看到密碼遮罩字元會取代每個字元，而可以輸入的字元數目上限為 15 個字元：

    [![螢幕擷取畫面：在 iOS 和 Android 上，文字由密碼字元遮罩的 Entry](../images/customize-behavior.png "使用密碼字元遮罩的 Entry")](../images/customize-behavior-large.png#lightbox "使用密碼字元遮罩的 Entry")

    在 Visual Studio 中，停止應用程式。

    如需有關自訂 [`Entry`](xref:Xamarin.Forms.Entry) 行為的詳細資訊，請參閱 [Xamarin.Forms 輸入](~/xamarin-forms/user-interface/text/entry.md)指南。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`Entry`](xref:Xamarin.Forms.Entry) 宣告，以自訂其行為：

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    此程式碼會設定屬性以自訂 [`Entry`](xref:Xamarin.Forms.Entry) 的行為。 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 屬性會限制 `Entry` 可允許的輸入長度，而 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 屬性會設定為 `false` 以停用拼字檢查。 同樣地，[`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) 屬性會設定為 `false`，以停用文字預測和自動文字預測。 此外，[`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) 屬性可確保輸入的字元會以密碼字元遮罩 (黑色圓圈)。

    > [!NOTE]
    > 在某些文字輸入的案例中 (例如輸入密碼)，拼字檢查和文字預測上會出現負面的體驗，因此應該停用。

1. 如果應用程式仍在執行，請將變更儲存至檔案，應用程式使用者介面將會在您的模擬器中自動更新。 否則，請在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 將文字輸入 [`Entry`](xref:Xamarin.Forms.Entry)，您將看到密碼遮罩字元會取代每個字元，而可以輸入的字元數目上限為 15 個字元：

    [![螢幕擷取畫面：在 iOS 和 Android 上，文字由密碼字元遮罩的 Entry](../images/customize-behavior.png "使用密碼字元遮罩的 Entry")](../images/customize-behavior-large.png#lightbox "使用密碼字元遮罩的 Entry")

    在 Visual Studio for Mac 中，停止應用程式。

    如需有關自訂 [`Entry`](xref:Xamarin.Forms.Entry) 行為的詳細資訊，請參閱 [Xamarin.Forms 輸入](~/xamarin-forms/user-interface/text/entry.md)指南。

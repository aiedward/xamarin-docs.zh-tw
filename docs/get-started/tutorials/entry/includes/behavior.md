---
ms.openlocfilehash: 48af50d31013f696879174a5cf108ab9fde92d0b
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "61343409"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

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

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 將文字輸入 [`Entry`](xref:Xamarin.Forms.Entry)，您將看到密碼遮罩字元會取代每個字元，而可以輸入的字元數目上限為 15 個字元：

    [![螢幕擷取畫面：在 iOS 和 Android 上，文字由密碼字元遮罩的 Entry](../images/customize-behavior.png "使用密碼字元遮罩的 Entry")](../images/customize-behavior-large.png#lightbox "使用密碼字元遮罩的 Entry")

    如需有關自訂 [`Entry`](xref:Xamarin.Forms.Entry) 行為的詳細資訊，請參閱 [Xamarin.Forms 輸入](~/xamarin-forms/user-interface/text/entry.md)指南。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

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

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式。 將文字輸入 [`Entry`](xref:Xamarin.Forms.Entry)，您將看到密碼遮罩字元會取代每個字元，而可以輸入的字元數目上限為 15 個字元：

    [![螢幕擷取畫面：在 iOS 和 Android 上，文字由密碼字元遮罩的 Entry](../images/customize-behavior.png "使用密碼字元遮罩的 Entry")](../images/customize-behavior-large.png#lightbox "使用密碼字元遮罩的 Entry")

    如需有關自訂 [`Entry`](xref:Xamarin.Forms.Entry) 行為的詳細資訊，請參閱 [Xamarin.Forms 輸入](~/xamarin-forms/user-interface/text/entry.md)指南。

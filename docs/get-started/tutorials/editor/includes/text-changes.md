---
ms.openlocfilehash: 653d3677f96d7da78af61531c535b1b7db684e7e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "77135110"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`Editor`](xref:Xamarin.Forms.Editor) 宣告，以便其設定 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Editor.Completed) 事件的處理常式：

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    此程式碼會將 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 事件設定成名為 `OnEditorTextChanged` 的事件處理常式，並將 [`Completed`](xref:Xamarin.Forms.Editor.Completed) 事件設定成名為 `OnEditorCompleted` 的事件處理常式。 下一個步驟將會建立這兩個事件處理常式。

1. 在 [方案總管]  的 **EditorTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnEditorTextChanged` 和 `OnEditorCompleted` 事件處理常式新增至類別：

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    `OnEditorTextChanged` 方法會在 [`Editor`](xref:Xamarin.Forms.Editor) 中的文字發生變更時執行。 `sender` 引數是負責引發 `TextChanged` 事件的 `Editor` 物件，並且可用來存取 `Editor` 物件。 [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) 引數會在文字變更之前和之後提供舊的和新的文字值。

    完成編輯之後，`OnEditorCompleted` 方法會隨即執行。 這可藉由從 [`Editor`](xref:Xamarin.Forms.Editor) 上移開焦點來完成，或另外按下 iOS 上的「完成」按鈕來完成。 `sender` 引數是負責引發 `TextChanged` 事件的 `Editor` 物件，並且可用來存取 `Editor` 物件。

    > [!IMPORTANT]
    > 輸入到 [`Editor`](xref:Xamarin.Forms.Editor) 的任何文字都會儲存到 [`Text`](xref:Xamarin.Forms.InputView.Text) 屬性。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上包含文字的 Editor](../images/text-changes.png "包含文字的 Editor")](../images/text-changes-large.png#lightbox "包含文字的 Editor")

    在兩個事件處理常式中設定中斷點、將文字輸入 [`Editor`](xref:Xamarin.Forms.Editor)，並觀察 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 事件的引發。 從 `Editor` 上移開焦點，即可看到 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件引發。

    如需有關 [`Editor`](xref:Xamarin.Forms.Editor) 事件的詳細資訊，請參閱 [Xamarin.Forms 編輯器](~/xamarin-forms/user-interface/text/editor.md)指南中的[互動功能](~/xamarin-forms/user-interface/text/editor.md#interactivity)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`Editor`](xref:Xamarin.Forms.Editor) 宣告，以便其設定 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Editor.Completed) 事件的處理常式：

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    此程式碼會將 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 事件設定成名為 `OnEditorTextChanged` 的事件處理常式，並將 [`Completed`](xref:Xamarin.Forms.Editor.Completed) 事件設定成名為 `OnEditorCompleted` 的事件處理常式。 下一個步驟將會建立這兩個事件處理常式。

1. 在 [Solution Pad]  的 **EditorTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnEditorTextChanged` 和 `OnEditorCompleted` 事件處理常式新增至類別：

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    `OnEditorTextChanged` 方法會在 [`Editor`](xref:Xamarin.Forms.Editor) 中的文字發生變更時執行。 `sender` 引數是負責引發 `TextChanged` 事件的 `Editor` 物件，並且可用來存取 `Editor` 物件。 [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) 引數會在文字變更之前和之後提供舊的和新的文字值。

    完成編輯之後，`OnEditorCompleted` 方法會隨即執行。 這可藉由從 [`Editor`](xref:Xamarin.Forms.Editor) 上移開焦點來完成，或另外按下 iOS 上的「完成」按鈕來完成。 `sender` 引數是負責引發 `TextChanged` 事件的 `Editor` 物件，並且可用來存取 `Editor` 物件。

    > [!IMPORTANT]
    > 輸入到 [`Editor`](xref:Xamarin.Forms.Editor) 的任何文字都會儲存到 [`Text`](xref:Xamarin.Forms.InputView.Text) 屬性。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上包含文字的 Editor](../images/text-changes.png "包含文字的 Editor")](../images/text-changes-large.png#lightbox "包含文字的 Editor")

    在兩個事件處理常式中設定中斷點、將文字輸入 [`Editor`](xref:Xamarin.Forms.Editor)，並觀察 [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) 事件的引發。 從 `Editor` 上移開焦點，即可看到 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件引發。

    如需有關 [`Editor`](xref:Xamarin.Forms.Editor) 事件的詳細資訊，請參閱 [Xamarin.Forms 編輯器](~/xamarin-forms/user-interface/text/editor.md)指南中的[互動功能](~/xamarin-forms/user-interface/text/editor.md#interactivity)。

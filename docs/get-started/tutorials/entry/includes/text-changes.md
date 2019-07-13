---
ms.openlocfilehash: 3130c20d39e0140695eed92ffa4941d6bafe796e
ms.sourcegitcommit: b4c9c574b771ae0265171ca5e938aed1c5e35028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2019
ms.locfileid: "67394541"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`Entry`](xref:Xamarin.Forms.Entry) 宣告，以便其設定 [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件的處理常式：

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    此程式碼會將 [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 事件設定成名為 `OnEntryTextChanged` 的事件處理常式，並將 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件設定成名為 `OnEntryCompleted` 的事件處理常式。 下一個步驟將會建立這兩個事件處理常式。

1. 在 [方案總管]  的 **EntryTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 加以開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnEntryTextChanged` 和 `OnEntryCompleted` 事件處理常式新增至類別：

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    `OnEntryTextChanged` 方法會在 [`Entry`](xref:Xamarin.Forms.Entry) 中的文字發生變更時執行。 `sender` 引數是負責引發 `TextChanged` 事件的 `Entry` 物件，並且可用來存取 `Entry` 物件。 [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) 引數會在文字變更之前和之後提供舊的和新的文字值。

    當您按下 Return 鍵來完成 [`Entry`](xref:Xamarin.Forms.Entry) 中的文字時，`OnEntryCompleted` 方法便會執行。 `sender` 引數是負責引發 `TextChanged` 事件的 `Entry` 物件，並且可用來存取 `Entry` 物件。

    > [!IMPORTANT]
    > 輸入到 [`Entry`](xref:Xamarin.Forms.Entry) 的任何文字都會儲存到 [`Text`](xref:Xamarin.Forms.Entry.Text) 屬性。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上包含文字的項目](../images/text-changes.png "包含文字的項目")](../images/text-changes-large.png#lightbox "包含文字的項目")

    在兩個事件處理常式中設定中斷點，並將文字輸入 [`Entry`](xref:Xamarin.Forms.Entry)，然後觀察 [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件的引發。

    如需有關 [`Entry`](xref:Xamarin.Forms.Entry) 事件的詳細資訊，請參閱 [Xamarin.Forms 項目](~/xamarin-forms/user-interface/text/entry.md)指南中的[事件和互動功能](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`Entry`](xref:Xamarin.Forms.Entry) 宣告，以便其設定 [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件的處理常式：

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    此程式碼會將 [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 事件設定成名為 `OnEntryTextChanged` 的事件處理常式，並將 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件設定成名為 `OnEntryCompleted` 的事件處理常式。 下一個步驟將會建立這兩個事件處理常式。

1. 在 [Solution Pad]  的 **EntryTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 加以開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnEntryTextChanged` 和 `OnEntryCompleted` 事件處理常式新增至類別：

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    `OnEntryTextChanged` 方法會在 [`Entry`](xref:Xamarin.Forms.Entry) 中的文字發生變更時執行。 `sender` 引數是負責引發 `TextChanged` 事件的 `Entry` 物件，並且可用來存取 `Entry` 物件。 [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) 引數會在文字變更之前和之後提供舊的和新的文字值。

    當您按下 Return 鍵來完成 [`Entry`](xref:Xamarin.Forms.Entry) 中的文字時，`OnEntryCompleted` 方法便會執行。 `sender` 引數是負責引發 `TextChanged` 事件的 `Entry` 物件，並且可用來存取 `Entry` 物件。

    > [!IMPORTANT]
    > 輸入到 [`Entry`](xref:Xamarin.Forms.Entry) 的任何文字都會儲存到 [`Text`](xref:Xamarin.Forms.Entry.Text) 屬性。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上包含文字的項目](../images/text-changes.png "包含文字的項目")](../images/text-changes-large.png#lightbox "包含文字的項目")

    在兩個事件處理常式中設定中斷點，並將文字輸入 [`Entry`](xref:Xamarin.Forms.Entry)，然後觀察 [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) 和 [`Completed`](xref:Xamarin.Forms.Entry.Completed) 事件的引發。

    如需有關 [`Entry`](xref:Xamarin.Forms.Entry) 事件的詳細資訊，請參閱 [Xamarin.Forms 項目](~/xamarin-forms/user-interface/text/entry.md)指南中的[事件和互動功能](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity)。

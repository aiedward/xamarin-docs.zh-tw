---
ms.openlocfilehash: d1f7d209eaaca62a55b768646f51024609057a63
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "61372911"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`Button`](xref:Xamarin.Forms.Button) 宣告，以便其設定 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件的處理常式：

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    此程式碼會將 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件設定為名為 `OnButtonClicked` 的事件處理常式 (將在下一個步驟中建立)。

1. 在 [方案總管]  的 **ButtonTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnButtonClicked` 事件處理常式新增至類別：

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    [方法會在點選 `Button`](xref:Xamarin.Forms.Button)`OnButtonClicked` 時執行。 `sender` 引數是負責引發 `Button` 事件的 `Clicked` 物件，並且可用來存取 `Button` 物件。 此事件處理常式可藉由 `Button` 來更新所顯示的文字。

    > [!NOTE]
    > 除了 `Clicked` 事件，`Button` 也會定義 [`Pressed`](xref:Xamarin.Forms.Button.Pressed) 和 [`Released`](xref:Xamarin.Forms.Button.Released) 事件。 如需詳細資訊，請參閱 [Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button)指南中的[按下與放開按鈕](~/xamarin-forms/user-interface/button.md)。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 按一下 [`Button`](xref:Xamarin.Forms.Button)，可看到顯示的文字發生變更：

    [![螢幕擷取畫面：在 iOS 和 Android 上，Button 的文字接收到點選後變更](../images/handle-button-click.png "處理按鈕點選")](../images/handle-button-click-large.png#lightbox "處理按鈕點選")

    如需有關處理按鈕點按的詳細資訊，請參閱 [Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md#handling-button-clicks)指南中的[處理按鈕點按](~/xamarin-forms/user-interface/button.md)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`Button`](xref:Xamarin.Forms.Button) 宣告，以便其設定 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件的處理常式：

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    此程式碼會將 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件設定為名為 `OnButtonClicked` 的事件處理常式 (將在下一個步驟中建立)。

1. 在 [Solution Pad]  的 **ButtonTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnButtonClicked` 事件處理常式新增至類別：

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    [方法會在點選 `Button`](xref:Xamarin.Forms.Button)`OnButtonClicked` 時執行。 `sender` 引數是負責引發 `Button` 事件的 `Clicked` 物件，並且可用來存取 `Button` 物件。 此事件處理常式可藉由 `Button` 來更新所顯示的文字。

    > [!NOTE]
    > 除了 `Clicked` 事件，`Button` 也會定義 [`Pressed`](xref:Xamarin.Forms.Button.Pressed) 和 [`Released`](xref:Xamarin.Forms.Button.Released) 事件。 如需詳細資訊，請參閱 [Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button)指南中的[按下與放開按鈕](~/xamarin-forms/user-interface/button.md)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式。 按一下 [`Button`](xref:Xamarin.Forms.Button)，可看到顯示的文字發生變更：

    [![螢幕擷取畫面：在 iOS 和 Android 上，Button 的文字接收到點選後變更](../images/handle-button-click.png "處理按鈕點選")](../images/handle-button-click-large.png#lightbox "處理按鈕點選")

    如需有關處理按鈕點按的詳細資訊，請參閱 [Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md#handling-button-clicks)指南中的[處理按鈕點按](~/xamarin-forms/user-interface/button.md)。

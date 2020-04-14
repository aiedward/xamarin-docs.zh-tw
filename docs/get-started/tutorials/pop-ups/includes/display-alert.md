---
ms.openlocfilehash: 875f00b379879aa131d37018f89e475170e5320e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "67277160"
---
Xamarin.Forms 具有強制回應快顯 (稱為警示)，可警示使用者或是詢問簡易問題。 在本練習中，您會使用 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法 (來自 [`Page`](xref:Xamarin.Forms.Page) 類別) 向使用者顯示警示，以及詢問簡易問題。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

若要完成此教學課程，您應該有 Visual Studio 2019 (最新版本)，並已安裝 [使用 .NET 進行行動開發]  工作負載。 此外，您還需要配對的 Mac 才能在 iOS 上建置教學課程應用程式。 如需安裝 Xamarin 平台的相關資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。 如需有關將 Visual Studio 2019 連線至 Mac 建置主機的相關資訊，請參閱[為 Xamarin.iOS 開發與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 啟動 Visual Studio，然後建立名為 **PopupsTutorial** 的新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **PopupsTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [方案總管]  的 **PopupsTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`Button`](xref:Xamarin.Forms.Button) 中的兩個 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 物件。 [`Button.Text`](xref:Xamarin.Forms.Button.Text) 屬性會指定出現在每個 `Button` 中的文字，而且 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件會設定為會在下一個步驟中建立的事件處理常式。

1. 在 [方案總管]  的 **PopupsTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnDisplayAlertButtonClicked` 和 `OnDisplayAlertQuestionButtonClicked` 事件處理常式新增至類別：

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    點選了 [`Button`](xref:Xamarin.Forms.Button) 時，便會執行各自的事件處理常式方法。 `OnDisplayAlertButtonClicked` 方法會呼叫 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法，以顯示具有單一取消按鈕的強制回應警示。 在關閉警示之後，使用者便可繼續與應用程式互動。

    `OnDisplayAlertQuestionButtonClicked` 方法會呼叫 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法的多載，以顯示具有接受按鈕和取消按鈕的強制回應警示。 使用者選取其中一個按鈕後，系統便會以 `boolean` 傳回選取項目。

    > [!IMPORTANT]
    > [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 是非同步的方法，因此請一律使用 `await` 關鍵字來等候。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 然後，點選第一個 [`Button`](xref:Xamarin.Forms.Button)：

    [![螢幕擷取畫面：iOS 和 Android 上的警示](../images/alert.png "警示")](../images/alert-large.png#lightbox "警示")

    關閉警示之後，請點選第二個 [`Button`](xref:Xamarin.Forms.Button)：

    [![螢幕擷取畫面：iOS 和 Android 上詢問問題的警示](../images/alert-question.png "詢問問題的警示")](../images/alert-question-large.png#lightbox "詢問問題的警示")

    請觀察一下，在選取問題的回應之後，回應便會輸出到 Visual Studio 的 [輸出]  視窗。

    如需如何顯示警示的詳細資訊，請參閱[顯示快顯視窗](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert)指南中的[顯示警示](~/xamarin-forms/user-interface/pop-ups.md)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

若要完成本教學課程，您應該具備 Visual Studio for Mac (最新版本)，並已安裝 iOS 和 Android 平台支援。 此外，您還需要 Xcode (最新版本)。 如需安裝 Xamarin 平台的詳細資訊，請參閱[安裝 Xamarin](~/get-started/installation/index.md)。

1. 啟動 Visual Studio for Mac，然後建立名為 **PopupsTutorial** 的新空白 Xamarin.Forms 應用程式。 確定該應用程式使用 .NET Standard 作為共用程式碼機制。

    > [!IMPORTANT]
    > 本教學課程中的 C# 和 XAML 程式碼片段，要求將解決方案命名為 **PopupsTutorial**。 當您從本教學課程將程式碼複製到解決方案時，使用不同的名稱會導致建置錯誤。

    如需有關所建立 .NET Standard 程式庫的詳細資訊，請參閱 [Xamarin.Forms 快速入門深度剖析](~/get-started/first-app/index.md)中的 [Xamarin.Forms 應用程式的結構](~/get-started/first-app/index.md)。

1. 在 [Solution Pad]  的 **PopupsTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`Button`](xref:Xamarin.Forms.Button) 中的兩個 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 物件。 [`Button.Text`](xref:Xamarin.Forms.Button.Text) 屬性會指定出現在每個 `Button` 中的文字，而且 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件會設定為會在下一個步驟中建立的事件處理常式。

1. 在 [Solution Pad]  的 **PopupsTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後，在 **MainPage.xaml.cs** 中，將 `OnDisplayAlertButtonClicked` 和 `OnDisplayAlertQuestionButtonClicked` 事件處理常式新增至類別：

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    點選了 [`Button`](xref:Xamarin.Forms.Button) 時，便會執行各自的事件處理常式方法。 `OnDisplayAlertButtonClicked` 方法會呼叫 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法，以顯示具有單一取消按鈕的強制回應警示。 在關閉警示之後，使用者便可繼續與應用程式互動。

    `OnDisplayAlertQuestionButtonClicked` 方法會呼叫 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法的多載，以顯示具有接受按鈕和取消按鈕的強制回應警示。 使用者選取其中一個按鈕後，系統便會以 `boolean` 傳回選取項目。

    > [!IMPORTANT]
    > [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 是非同步的方法，因此請一律使用 `await` 關鍵字來等候。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式。 然後，點選第一個 [`Button`](xref:Xamarin.Forms.Button)：

    [![螢幕擷取畫面：iOS 和 Android 上的警示](../images/alert.png "警示")](../images/alert-large.png#lightbox "警示")

    關閉警示之後，請點選第二個 [`Button`](xref:Xamarin.Forms.Button)：

    [![螢幕擷取畫面：iOS 和 Android 上詢問問題的警示](../images/alert-question.png "詢問問題的警示")](../images/alert-question-large.png#lightbox "詢問問題的警示")

    請觀察一下，在選取問題的回應之後，回應便會輸出到 Visual Studio for Mac 的 [應用程式輸出]  視窗。

    如需如何顯示警示的詳細資訊，請參閱[顯示快顯視窗](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert)指南中的[顯示警示](~/xamarin-forms/user-interface/pop-ups.md)。

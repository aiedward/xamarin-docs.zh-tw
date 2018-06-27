---
title: Xamarin.Forms 多重畫面快速入門
description: 本文會說明如何新增另一個畫面來延伸 Phoneword 應用程式，以追蹤應用程式的通話記錄。
ms.prod: quickstart
ms.assetid: 255d93b9-518c-4e5d-a9cd-4dd8a7945a7f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/13/2018
ms.openlocfilehash: 538ca831e51069f345a8a7a53aa844352c207faa
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268832"
---
# <a name="xamarinforms-multiscreen-quickstart"></a>Xamarin.Forms 多重畫面快速入門

本快速入門會示範如何新增另一個畫面來延伸 Phoneword 應用程式，以追蹤應用程式的通話記錄。 最終的應用程式如下所示：

[![](quickstart-images/intro-app-examples-sml.png "Phoneword 應用程式")](quickstart-images/intro-app-examples.png#lightbox "Phoneword 應用程式")

延伸 Phoneword 應用程式，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 啟動 Visual Studio。 在起始頁面上，按一下 [開啟專案]，然後在 [開啟專案] 對話方塊中，選取適用於 Phoneword 專案的方案檔：

    ![](quickstart-images/vs/open-solution.png "開啟專案")

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword** 專案，然後選取 [加入] > [新項目]：

    ![](quickstart-images/vs/add-new-item.png "加入新項目")

3. 在 [加入新項目] 對話方塊中，選取 [Visual C# 項目] > [Xamarin.Forms] > [內容頁面]、將新檔案命名為 **CallHistoryPage**，然後按一下 [新增] 按鈕。 這會將名為 **CallHistoryPage** 的頁面新增至專案：

    ![](quickstart-images/vs/add-callhistorypage-class.png "Xamarin.Forms 專案範本")

4. 在 **CallHistoryPage.xaml** 中，移除所有範本程式碼，並以下列程式碼取代它。 此程式碼會以宣告的方式定義頁面的使用者介面：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.CallHistoryPage"
                       Title="Call History">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
        </StackLayout>
    </ContentPage>
    ```

    按下 **CTRL+S** 以將變更儲存到 **CallHistoryPage.xaml**，然後關閉檔案。

5. 在 [方案總管] 中，連按兩下共用 **Phoneword** 專案的 **App.xaml.cs** 檔案，開啟它：

    ![](quickstart-images/vs/open-app-class.png "開啟 App.xaml.cs")

6. 在 **App.xaml.cs** 中，匯入 `System.Collections.Generic` 命名空間、新增 `PhoneNumbers` 屬性的宣告、初始化 `App` 建構函式中的屬性，然後將 [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) 屬性初始化為 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)。 `PhoneNumbers` 集合將用來儲存應用程式所撥打之轉譯電話號碼的清單：

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public static IList<string> PhoneNumbers { get; set; }

            public App()
            {
                InitializeComponent();
                PhoneNumbers = new List<string>();
                MainPage = new NavigationPage(new MainPage());
            }
            ...
        }
    }
    ```

    按下 **CTRL+S** 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

7. 在 [方案總管] 中，連按兩下共用 **Phoneword** 專案的 **MainPage.xaml** 檔案，開啟它：

    ![](quickstart-images/vs/open-mainpage-xaml.png "開啟 MainPage.xaml")

8. 在 **MainPage.xaml** 中，將 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 控制項新增到 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 控制項的結尾。 此按鈕將用來瀏覽至 [通話記錄] 頁面：

    ```xaml
    <StackLayout VerticalOptions="FillAndExpand"
                 HorizontalOptions="FillAndExpand"
                 Orientation="Vertical"
                 Spacing="15">
      ...
      <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
      <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
              Clicked="OnCallHistory" />
    </StackLayout>
    ```

    按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml**，然後關閉檔案。

9. 在 [方案總管] 中，按兩下 **MainPage.xaml.cs** 以將它開啟：

    ![](quickstart-images/vs/open-mainpage-codebehind.png "開啟 MainPage.xaml.cs")

10. 在 **MainPage.xaml.cs** 中，新增 `OnCallHistory` 事件處理常式方法，並修改 `OnCall` 事件處理常式方法，以便將轉譯的電話號碼新增至 `App.PhoneNumbers` 集合並啟用 `callHistoryButton`，前提是 `dialer` 變數不是 `null`：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            ...

            async void OnCall(object sender, EventArgs e)
            {
                ...
                if (dialer != null) {
                    App.PhoneNumbers.Add (translatedNumber);
                    callHistoryButton.IsEnabled = true;
                    dialer.Dial (translatedNumber);
                }
                ...
            }

            async void OnCallHistory(object sender, EventArgs e)
            {
                await Navigation.PushAsync (new CallHistoryPage ());
            }
        }
    }
    ```

    按下 **CTRL+S** 以將變更儲存到 **MainPage.xaml.cs**，然後關閉檔案。

11. 在 Visual Studio 中，選取 [建置] > [建置方案] 功能表項目 (或按下 **CTRL+SHIFT+B**)。 系統將建置應用程式，且 Visual Studio 狀態列中將會出現成功訊息：

    ![](quickstart-images/vs/build-successful.png "建置成功")

    如果發生錯誤，請重複上述步驟並更正任何錯誤，直到應用程式建置成功為止。

12. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動應用程式：

    ![](quickstart-images/vs/start.png "Visual Studio 工具列")
    ![](quickstart-images/vs/phone-result-uwp.png "Phoneword 應用程式 UWP")

13. 在 [方案總管] 中，以滑鼠右鍵按一下 **Phoneword.Droid** 專案，然後選取 [設定為啟始專案]。
14. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動 Android 模擬器內的應用程式。
15. 如果您有 iOS 裝置，而且符合 Xamarin.Forms 開發的 Mac 系統需求，請使用類似的技術將應用程式部署至 iOS 裝置。 或者，您也可以將應用程式部署至 [iOS 遠端模擬器](~/tools/ios-simulator.md)。

    注意：所有模擬器都不支援通話功能。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 啟動 Visual Studio for Mac。 在起始頁面上，按一下 [開啟]，然後在對話方塊中，選取適用於 Phoneword 專案的方案檔：

    ![](quickstart-images/xs/open-solution.png "開啟方案")

2. 在 [Solution Pad] 中，選取 **Phoneword** 專案、按一下滑鼠右鍵，然後選取 [新增] > [新增檔案]：

    ![](quickstart-images/xs/add-new-file.png "加入新檔案")

3. 在 [新增檔案] 對話方塊中，選取 [表單] > [Forms ContentPage XAML]、將新檔案命名為 **CallHistoryPage**，然後按一下 [新增] 按鈕。 這會將名為 **CallHistoryPage** 的頁面新增至專案：

    ![](quickstart-images/xs/add-callhistorypage-class.png "新增 Forms ContentPage")

4. 在 [Solution Pad] 中，按兩下 **CallHistoryPage.xaml** 以將其開啟：

    ![](quickstart-images/xs/open-callhistorypage-xaml.png "開啟 CallHistoryPage.xaml")

5. 在 **CallHistoryPage.xaml** 中，移除所有範本程式碼，並以下列程式碼取代它。 此程式碼會以宣告的方式定義頁面的使用者介面：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:local="clr-namespace:Phoneword;assembly=Phoneword"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.CallHistoryPage"
                       Title="Call History">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <ListView ItemsSource="{x:Static local:App.PhoneNumbers}" />
        </StackLayout>
    </ContentPage>      
    ```

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **CallHistoryPage.xaml**，然後關閉檔案。

6. 在 [Solution Pad] 中，按兩下 **App.xaml.cs** 以將其開啟：

    ![](quickstart-images/xs/open-app-class.png "開啟 App.xaml.cs")

7. 在 **App.xaml.cs** 中，匯入 `System.Collections.Generic` 命名空間、新增 `PhoneNumbers` 屬性的宣告、初始化 `App` 建構函式中的屬性，然後將 [`MainPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) 屬性初始化為 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)。 `PhoneNumbers` 集合將用來儲存應用程式所撥打之轉譯電話號碼的清單：

    ```csharp
    using System.Collections.Generic;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public static IList<string> PhoneNumbers { get; set; }

            public App()
            {
                InitializeComponent();
                PhoneNumbers = new List<string>();
                MainPage = new NavigationPage(new MainPage());
            }
            ...
        }
    }
    ```

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **App.xaml.cs**，然後關閉檔案。

8. 在 [Solution Pad] 中，按兩下 **MainPage.xaml** 以將其開啟：

    ![](quickstart-images/xs/open-mainpage-xaml.png "開啟 MainPage.xaml")

9. 在 **MainPage.xaml** 中，將 [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) 控制項新增到 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 控制項的結尾。 此按鈕將用來瀏覽至 [通話記錄] 頁面：

    ```xaml
    <StackLayout VerticalOptions="FillAndExpand"
                 HorizontalOptions="FillAndExpand"
                 Orientation="Vertical"
                 Spacing="15">
      ...
      <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
      <Button x:Name="callHistoryButton" Text="Call History" IsEnabled="false"
              Clicked="OnCallHistory" />
    </StackLayout>
    ```

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **MainPage.xaml**，然後關閉檔案。

10. 在 [Solution Pad] 中，按兩下 **MainPage.xaml.cs** 以將其開啟：

    ![](quickstart-images/xs/open-mainpage-codebehind.png "開啟 MainPage.xaml.cs")

11. 在 **MainPage.xaml.cs** 中，新增 `OnCallHistory` 事件處理常式方法，並修改 `OnCall` 事件處理常式方法，以便將轉譯的電話號碼新增至 `App.PhoneNumbers` 集合並啟用 `callHistoryButton`，前提是 `dialer` 變數不是 `null`：

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            ...

            async void OnCall(object sender, EventArgs e)
            {
                ...
                if (dialer != null) {
                    App.PhoneNumbers.Add (translatedNumber);
                    callHistoryButton.IsEnabled = true;
                    dialer.Dial (translatedNumber);
                }
                ...
            }

            async void OnCallHistory(object sender, EventArgs e)
            {
                await Navigation.PushAsync (new CallHistoryPage ());
            }
        }
    }
    ```

    選擇 [檔案] > [儲存] (或按下 **&#8984; + S**) 以將變更儲存到 **MainPage.xaml.cs**，然後關閉檔案。

12. 在 Visual Studio for Mac 中，選取 [建置] > [全部建置] 功能表項目 (或按下 **&#8984; + B**)。 系統將建置應用程式，且 Visual Studio for Mac 工具列中將會出現成功訊息：

    ![](quickstart-images/xs/build-successful.png "建置成功")

    如果發生錯誤，請重複上述步驟並更正任何錯誤，直到應用程式建置成功為止。

13. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動 iOS 模擬器內的應用程式：

    ![](quickstart-images/xs/start.png "Visual Studio for Mac 工具列")
    ![](quickstart-images/xs/phone-result-ios.png "iOS 模擬器")

    注意：iOS 模擬器不支援通話功能。

14. 在 [Solution Pad] 中，選取 **Phoneword.Droid** 專案、按一下滑鼠右鍵，然後選取 [設定為啟始專案]：

    ![](quickstart-images/xs/set-startup-project.png "設定為啟始專案")

15. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕) 以啟動 Android 模擬器內的應用程式：

    ![](quickstart-images/xs/phone-result-android.png "Android 模擬器")

    注意：Android 模擬器不支援通話功能。

-----

恭喜您完成多重畫面的 Xamarin.Forms 應用程式。 本指南中的[下一個主題](~/xamarin-forms/get-started/hello-xamarin-forms-multiscreen/deepdive.md)會檢閱此逐步解說中所採取的步驟，以了解如何使用 Xamarin.Forms 的頁面瀏覽和資料繫結。


## <a name="related-links"></a>相關連結

- [Phoneword (範例)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
- [PhonewordMultiscreen (範例)](https://developer.xamarin.com/samples/xamarin-forms/PhonewordMultiscreen/)

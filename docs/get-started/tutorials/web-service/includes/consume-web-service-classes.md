---
ms.openlocfilehash: bf46d8ab4ca124bc36dd971513a78147e71a0039
ms.sourcegitcommit: 4d260b655cb52b990dda79c239a9721f2e964625
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570872"
---
在此練習中，您會建立使用者介面來取用 `RestService` 類別，其會從 GitHub Web API 擷取 .NET 存放庫資料。 擷取的資料會由 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 顯示。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 [方案總管] 的 **WebServiceTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Get Repository Data"
                    Clicked="OnButtonClicked" />
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Description}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                            <Label Text="{Binding GitHubHomeUrl}"
                                   TextColor="Gray"
                                   FontSize="Caption" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告方式定義頁面的使用者介面，其包含 [`Button`](xref:Xamarin.Forms.Button)，以及 [`CollectionView`](xref:Xamarin.Forms.CollectionView)。 `Button` 會將其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件設定為名為 `OnButtonClicked` 的事件處理常式 (將在下一個步驟中建立)。 `CollectionView` 會將 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性設為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，以定義 `CollectionView` 中每個項目的外觀。 `DataTemplate` 的子系是 [`StackLayout`](xref:Xamarin.Forms.StackLayout)，其中包含三個 [`Label`](xref:Xamarin.Forms.Label) 物件。 `Label` 物件會將其 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性繫結到每個 `Repository` 物件的 `Name`、`Description` 和 `GitHubHomeUrl` 屬性。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

    此外，[`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有以 `x:Name` 屬性指定的名稱。 這可讓程式碼後置檔案使用指派的名稱來存取物件。

1. 在 [方案總管] 的 **WebServiceTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                List<Repository> repositories = await _restService.GetRepositoriesAsync(Constants.GitHubReposEndpoint);
                collectionView.ItemsSource = repositories;
            }
        }
    }
    ```

    `OnButtonClicked` 方法 (在點選 [`Button`](xref:Xamarin.Forms.Button) 時執行) 會叫用 `RestService.GetRepositoriesAsync` 方法，以從 GitHub Web API 擷取 .NET 存放庫資料。 `GetRepositoriesAsync` 方法需要 `string` 引數 (代表所叫用 Web API 的 URI)，而這是由 `Constants.GitHubReposEndpoint` 欄位傳回。

    擷取要求的資料後，[`CollectionView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性即會設為擷取的資料。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 點選 [`Button`](xref:Xamarin.Forms.Button)，以從 GitHub 擷取 .NET 存放庫資料：

    [![iOS 和 Android 上 GitHub .NET 存放庫的螢幕擷取畫面](../images/consume-web-service.png)](../images/consume-web-service-large.png#lightbox)

    如需有關如何取用 Xamarin.Forms 中 REST 架構 Web 服務的詳細資訊，請參閱[取用 RESTful Web 服務 (指南)](~/xamarin-forms/data-cloud/web-services/rest.md)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 [Solution Pad] 的 **WebServiceTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Get Repository Data"
                    Clicked="OnButtonClicked" />
            <CollectionView x:Name="collectionView">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <StackLayout>
                            <Label Text="{Binding Name}"
                                   FontSize="Medium" />
                            <Label Text="{Binding Description}"
                                   TextColor="Silver"
                                   FontSize="Small" />
                            <Label Text="{Binding GitHubHomeUrl}"
                                   TextColor="Gray"
                                   FontSize="Caption" />
                        </StackLayout>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </StackLayout>
    </ContentPage>
    ```

    此程式碼會以宣告方式定義頁面的使用者介面，其包含 [`Button`](xref:Xamarin.Forms.Button)，以及 [`CollectionView`](xref:Xamarin.Forms.CollectionView)。 `Button` 會將其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件設定為名為 `OnButtonClicked` 的事件處理常式 (將在下一個步驟中建立)。 `CollectionView` 會將 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性設為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，以定義 `CollectionView` 中每個項目的外觀。 `DataTemplate` 的子系是 [`StackLayout`](xref:Xamarin.Forms.StackLayout)，其中包含三個 [`Label`](xref:Xamarin.Forms.Label) 物件。 `Label` 物件會將其 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性繫結到每個 `Repository` 物件的 `Name`、`Description` 和 `GitHubHomeUrl` 屬性。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

    此外，[`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有以 `x:Name` 屬性指定的名稱。 這可讓程式碼後置檔案使用指派的名稱來存取物件。

1. 在 [Solution Pad] 的 **WebServiceTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Collections.Generic;
    using Xamarin.Forms;

    namespace WebServiceTutorial
    {
        public partial class MainPage : ContentPage
        {
            RestService _restService;

            public MainPage()
            {
                InitializeComponent();
                _restService = new RestService();
            }

            async void OnButtonClicked(object sender, EventArgs e)
            {
                List<Repository> repositories = await _restService.GetRepositoriesAsync(Constants.GitHubReposEndpoint);
                collectionView.ItemsSource = repositories;
            }
        }
    }
    ```

    `OnButtonClicked` 方法 (在點選 [`Button`](xref:Xamarin.Forms.Button) 時執行) 會叫用 `RestService.GetRepositoriesAsync` 方法，以從 GitHub Web API 擷取 .NET 存放庫資料。 `GetRepositoriesAsync` 方法需要 `string` 引數 (代表所叫用 Web API 的 URI)，而這是由 `Constants.GitHubReposEndpoint` 欄位傳回。

    擷取要求的資料後，[`CollectionView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性即會設為擷取的資料。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式。 點選 [`Button`](xref:Xamarin.Forms.Button)，以從 GitHub 擷取 .NET 存放庫資料：

    [![iOS 和 Android 上 GitHub .NET 存放庫的螢幕擷取畫面](../images/consume-web-service.png)](../images/consume-web-service-large.png#lightbox)

    如需有關如何取用 Xamarin.Forms 中 REST 架構 Web 服務的詳細資訊，請參閱[取用 RESTful Web 服務 (指南)](~/xamarin-forms/data-cloud/web-services/rest.md)。

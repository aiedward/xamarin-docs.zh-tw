---
ms.openlocfilehash: c92a97b336e89214bbd95021ad8fb9a56f64cc8c
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "67659766"
---
在此練習中，您會建立使用者介面來取用 `RestService` 類別，接著從 [OpenWeatherMap](https://openweathermap.org/) Web API 擷取資料。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 [方案總管]  的 **WebServiceTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`Entry`](xref:Xamarin.Forms.Entry)、[`Button`](xref:Xamarin.Forms.Button)，以及 [`Grid`](xref:Xamarin.Forms.Grid) 中一系列的 [`Label`](xref:Xamarin.Forms.Label) 執行個體。 `Entry` 已藉由設定其 [`Text`](xref:Xamarin.Forms.Entry.Text) 屬性預先填入 "Seattle"。 `Button` 會將其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件設定為名為 `OnButtonClicked` 的事件處理常式 (將在下一個步驟中建立)。 其中一半的 `Label` 執行個體會顯示靜態文字，剩餘的執行個體資料繫結至 `WeatherData` 屬性。 在執行階段，使用資料繫結的 `Label` 執行個體會針對要使用於其繫結運算式的 `WeatherData` 物件，查看其各自的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

    此外，[`Entry`](xref:Xamarin.Forms.Entry) 具有以 `x:Name` 屬性指定的名稱。 這可讓程式碼後置檔案使用指派的名稱來存取物件。

1. 在 [方案總管]  的 **WebServiceTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
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
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    在點選 [`Button`](xref:Xamarin.Forms.Button) 時執行的 `OnButtonClicked` 方法會叫用 `RestService.GetWeatherDataAsync` 方法，以擷取其名稱已輸入在 [`Entry`](xref:Xamarin.Forms.Entry) 中的城市天氣資料。 `GetWeatherDataAsync` 方法需要 `string` 引數 (代表所叫用 Web API 的 URI)，而這是由 `GenerateRequestUri` 方法產生。 此方法會採用儲存在 `OpenWeatherMapEndpoint` 常數中的端點位址，並將查詢參數新增至位址以指定：

    - 要求天氣資料的城市。
    - 用以傳回天氣資料的單位。
    - 您個人的 API 金鑰。

    擷取要求的天氣資料之後，頁面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 會設定為 `WeatherData` 物件。 如需 `BindingContext` 屬性的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)指南中[具有繫結內容的繫結](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context)一節。

    > [!IMPORTANT]
    > [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性會透過視覺化樹狀結構繼承。 因此，因為它已設定於 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件、`ContentPage` 的子物件會繼承其值，包括 [`Label`](xref:Xamarin.Forms.Label) 執行個體。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式。 點選 [`Button`](xref:Xamarin.Forms.Button) 以擷取西雅圖目前的天氣資料：

    [![螢幕擷取畫面：iOS 和 Android 上的西雅圖天氣資料](../images/consume-web-service.png "西雅圖天氣資料")](../images/consume-web-service-large.png#lightbox "西雅圖天氣資料")

    > [!IMPORTANT]
    > 您個人的 OpenWeatherMap API 金鑰必須設定為 `Constants` 類別中 `OpenWeatherMapAPIKey` 常數的值。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 [Solution Pad]  的 **WebServiceTutorial** 專案中，按兩下 **MainPage.xaml** 將其開啟。 然後在 **MainPage.xaml** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="WebServiceTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="0.4*" />
                <ColumnDefinition Width="0.6*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
                <RowDefinition Height="40" />
            </Grid.RowDefinitions>
            <Entry x:Name="cityEntry"
                   Grid.ColumnSpan="2"
                   Text="Seattle" />
            <Button Grid.ColumnSpan="2"
                    Grid.Row="1"
                    Text="Get Weather"
                    Clicked="OnButtonClicked" />                
            <Label Grid.Row="2"
                   Text="Location:" />
            <Label Grid.Row="2"
                   Grid.Column="1"
                   Text="{Binding Title}" />            
            <Label Grid.Row="3"
                   Text="Temperature:" />
            <Label Grid.Row="3"
                   Grid.Column="1"
                   Text="{Binding Main.Temperature}" />            
            <Label Grid.Row="4"
                   Text="Wind Speed:" />
            <Label Grid.Row="4"
                   Grid.Column="1"
                   Text="{Binding Wind.Speed}" />            
            <Label Grid.Row="5"
                   Text="Humidity:" />
            <Label Grid.Row="5"
                   Grid.Column="1"
                   Text="{Binding Main.Humidity}" />            
            <Label Grid.Row="6"
                   Text="Visibility:" />
            <Label Grid.Row="6"
                   Grid.Column="1"
                   Text="{Binding Weather[0].Visibility}" />
        </Grid>
    </ContentPage>
    ```

    此程式碼會以宣告的方式定義頁面的使用者介面，其包含 [`Entry`](xref:Xamarin.Forms.Entry)、[`Button`](xref:Xamarin.Forms.Button)，以及 [`Grid`](xref:Xamarin.Forms.Grid) 中一系列的 [`Label`](xref:Xamarin.Forms.Label) 執行個體。 `Entry` 已藉由設定其 [`Text`](xref:Xamarin.Forms.Entry.Text) 屬性預先填入 "Seattle"。 `Button` 會將其 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件設定為名為 `OnButtonClicked` 的事件處理常式 (將在下一個步驟中建立)。 其中一半的 `Label` 執行個體會顯示靜態文字，剩餘的執行個體資料繫結至 `WeatherData` 屬性。 在執行階段，使用資料繫結的 `Label` 執行個體會針對要使用於其繫結運算式的 `WeatherData` 物件，查看其各自的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性。 如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

    此外，[`Entry`](xref:Xamarin.Forms.Entry) 具有以 `x:Name` 屬性指定的名稱。 這可讓程式碼後置檔案使用指派的名稱來存取物件。

    如需有關如何取用 Xamarin.Forms 中 REST 架構 Web 服務的詳細資訊，請參閱[取用 RESTful Web 服務 (指南)](~/xamarin-forms/data-cloud/web-services/rest.md)。

1. 在 [Solution Pad]  的 **WebServiceTutorial** 專案中展開 **MainPage.xaml**，然後按兩下 **MainPage.xaml.cs** 將其開啟。 然後在 **MainPage.xaml.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
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
                if (!string.IsNullOrWhiteSpace(cityEntry.Text))
                {
                    WeatherData weatherData = await _restService.GetWeatherDataAsync(GenerateRequestUri(Constants.OpenWeatherMapEndpoint));
                    BindingContext = weatherData;
                }
            }

            string GenerateRequestUri(string endpoint)
            {
                string requestUri = endpoint;
                requestUri += $"?q={cityEntry.Text}";
                requestUri += "&units=imperial"; // or units=metric
                requestUri += $"&APPID={Constants.OpenWeatherMapAPIKey}";
                return requestUri;
            }
        }
    }
    ```

    在點選 [`Button`](xref:Xamarin.Forms.Button) 時執行的 `OnButtonClicked` 方法會叫用 `RestService.GetWeatherDataAsync` 方法，以擷取其名稱已輸入在 [`Entry`](xref:Xamarin.Forms.Entry) 中的城市天氣資料。 `GetWeatherDataAsync` 方法需要 `string` 引數 (代表所叫用 Web API 的 URI)，而這是由 `GenerateRequestUri` 方法產生。 此方法會採用儲存在 `OpenWeatherMapEndpoint` 常數中的端點位址，並將查詢參數新增至位址以指定：

    - 要求天氣資料的城市。
    - 用以傳回天氣資料的單位。
    - 您個人的 API 金鑰。

    擷取要求的天氣資料之後，頁面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 會設定為 `WeatherData` 物件。 如需 `BindingContext` 屬性的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)指南中[具有繫結內容的繫結](~/xamarin-forms/app-fundamentals/data-binding/basic-bindings.md#bindings-with-a-binding-context)一節。

    > [!IMPORTANT]
    > [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性會透過視覺化樹狀結構繼承。 因此，因為它已設定於 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件、`ContentPage` 的子物件會繼承其值，包括 [`Label`](xref:Xamarin.Forms.Label) 執行個體。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式。 點選 [`Button`](xref:Xamarin.Forms.Button) 以擷取西雅圖目前的天氣資料：

    [![螢幕擷取畫面：iOS 和 Android 上的西雅圖天氣資料](../images/consume-web-service.png "西雅圖天氣資料")](../images/consume-web-service-large.png#lightbox "西雅圖天氣資料")

    > [!IMPORTANT]
    > 您個人的 OpenWeatherMap API 金鑰必須設定為 `Constants` 類別中 `OpenWeatherMapAPIKey` 常數的值。

    如需有關如何取用 Xamarin.Forms 中 REST 架構 Web 服務的詳細資訊，請參閱[取用 RESTful Web 服務 (指南)](~/xamarin-forms/data-cloud/web-services/rest.md)。

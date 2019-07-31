---
ms.openlocfilehash: 3b1603b6af5ebb5558c3cd764f41fdbe24351b9b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669572"
---
REST 要求是使用網頁瀏覽器用來擷取頁面以及將資料傳送到伺服器的相同 HTTP 指令動詞，並透過 HTTP 提出。 在此練習中，您會建立一個類別，該類別會使用 GET 指令動詞從 [OpenWeatherMap](https://openweathermap.org/) Web API 擷取資料。 此 Web API 可用來擷取指定位置的天氣預報資料。 您需要註冊 API 金鑰，才能使用此 Web API。

> [!div class="nextstepaction"]
> [註冊 API 金鑰](https://home.openweathermap.org/users/sign_up)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 [方案總管]  的 **WebServiceTutorial** 專案中，將名為 `Constants` 的新類別新增到此專案。 然後在 **Constants.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public const string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    此程式碼會定義兩個常數。 `OpenWeatherMapEndpoint` 常數可定義將其提出 Web 要求的端點，而 `OpenWeatherMapAPIKey` 常數則針對 [OpenWeatherMap](https://openweathermap.org/) 服務定義您個人的 API 金鑰。

    > [!IMPORTANT]
    > 您必須將個人的 OpenWeatherMap API 金鑰設定為 `OpenWeatherMapAPIKey` 常數的值。

1. 在 [方案總管]  的 **WebServicesTutorial** 專案中，將名為 `WeatherData` 的新類別新增到此專案。 然後在 **WeatherData.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    此程式碼會定義四個類別，用於定型從 Web 服務擷取的 JSON 資料。 每個屬性 (property) 都會以 `JsonProperty` 屬性 (attribute) 裝飾，其中包含 JSON 欄位名稱。 Newtonsoft.Json 在將 JSON 資料還原序列化為模型物件時，會使用 JSON 欄位名稱與 CLR 屬性的這項對應。

    > [!NOTE]
    > 上述類別定義已經簡化，並不會完全定型從 Web 服務擷取的 JSON 資料。 如需完整的資料模型範例，請參閱[天氣應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/weather/)範例。

1. 在 [方案總管]  的 **WebServiceTutorial** 專案中，將名為 `RestService` 的新類別新增到此專案。 然後在 **RestService.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    此程式碼會定義單一方法 `GetWeatherDataAsync`，該方法可從 [OpenWeatherMap](https://openweathermap.org/) Web API 擷取指定位置的天氣資料。 這個方法會使用 `HttpClient.GetAsync` 方法，將 GET 要求傳送至 `uri` 引數所指定的 Web API。 Web API 會傳送儲存在 `HttpResponseMessage` 物件中的回應。 此回應包括 HTTP 狀態碼，這表示 HTTP 要求成功或失敗。 假設要求成功，Web API 就會回應 HTTP 狀態碼 200 (確定) 和 JSON 回應 (其位於 `HttpResponseMessage.Content` 屬性中)。 此 JSON 資料會先讀取到使用 `HttpContent.ReadAsStringAsync` 方法的 `string`，再使用 `JsonConvert.DeserializeObject` 方法還原序列化為 `WeatherData` 物件。 此方法會使用 JSON 欄位名稱與 CLR 屬性之間的對應 (定義於 `WeatherData` 類別中) 來執行還原序列化。

1. 建置解決方案以確定沒有任何錯誤。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 [Solution Pad]  的 **WebServiceTutorial** 專案中，將名為 `Constants` 的新類別新增到此專案。 然後在 **Constants.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public static string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public static string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    此程式碼會定義兩個常數。 `OpenWeatherMapEndpoint` 常數可定義將其提出 Web 要求的端點，而 `OpenWeatherMapAPIKey` 常數則針對 [OpenWeatherMap](https://openweathermap.org/) 服務定義您個人的 API 金鑰。

    > [!IMPORTANT]
    > 您必須將個人的 OpenWeatherMap API 金鑰設定為 `OpenWeatherMapAPIKey` 常數的值。

1. 在 [Solution Pad]  的 **WebServicesTutorial** 專案中，將名為 `WeatherData` 的新類別新增到此專案。 然後在 **WeatherData.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    此程式碼會定義四個類別，用於定型從 Web 服務擷取的 JSON 資料。 每個屬性 (property) 都會以 `JsonProperty` 屬性 (attribute) 裝飾，其中包含 JSON 欄位名稱。 Newtonsoft.Json 在將 JSON 資料還原序列化為模型物件時，會使用 JSON 欄位名稱與 CLR 屬性的這項對應。

    > [!NOTE]
    > 上述類別定義已經簡化，並不會完全定型從 Web 服務擷取的 JSON 資料。 如需完整的資料模型範例，請參閱[天氣應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/weather/)範例。

1. 在 [Solution Pad]  的 **WebServiceTutorial** 專案中，將名為 `RestService` 的新類別新增到此專案。 然後在 **RestService.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    此程式碼會定義單一方法 `GetWeatherDataAsync`，該方法可從 [OpenWeatherMap](https://openweathermap.org/) Web API 擷取指定位置的天氣資料。 這個方法會使用 `HttpClient.GetAsync` 方法，將 GET 要求傳送至 `uri` 引數所指定的 Web API。 Web API 會傳送儲存在 `HttpResponseMessage` 物件中的回應。 此回應包括 HTTP 狀態碼，這表示 HTTP 要求成功或失敗。 假設要求成功，Web API 就會回應 HTTP 狀態碼 200 (確定) 和 JSON 回應 (其位於 `HttpResponseMessage.Content` 屬性中)。 此 JSON 資料會先讀取到使用 `HttpContent.ReadAsStringAsync` 方法的 `string`，再使用 `JsonConvert.DeserializeObject` 方法還原序列化為 `WeatherData` 物件。 此方法會使用 JSON 欄位名稱與 CLR 屬性之間的對應 (定義於 `WeatherData` 類別中) 來執行還原序列化。

1. 建置解決方案以確定沒有任何錯誤。

---
ms.openlocfilehash: 7a5acca4169b1f763e178e0a05b01548765ff45d
ms.sourcegitcommit: 4d260b655cb52b990dda79c239a9721f2e964625
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570867"
---
REST 要求是使用網頁瀏覽器用來擷取頁面以及將資料傳送到伺服器的相同 HTTP 指令動詞，並透過 HTTP 提出。 在此練習中，您會建立使用 GET 指令動詞從 GitHub Web API 擷取 .NET 存放庫資料的類別。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 [方案總管] 的 **WebServiceTutorial** 專案中，將名為 `Constants` 的新類別新增到此專案。 然後在 **Constants.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string GitHubReposEndpoint = "https://api.github.com/orgs/dotnet/repos";
        }
    }
    ```

    此程式碼會定義單一常數，也就是將對其提出 Web 要求的端點。

1. 在 [方案總管] 的 **WebServicesTutorial** 專案中，將名為 `Repository` 的新類別新增到此專案。 然後在 **Repository.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class Repository
        {
            [JsonProperty("name")]
            public string Name { get; set; }

            [JsonProperty("description")]
            public string Description { get; set; }

            [JsonProperty("html_url")]
            public Uri GitHubHomeUrl { get; set; }

            [JsonProperty("homepage")]
            public Uri Homepage { get; set; }

            [JsonProperty("watchers")]
            public int Watchers { get; set; }
        }
    }
    ```

    此程式碼會定義 `Repository` 類別，用於為從 Web 服務擷取的 JSON 資料建立模型。 每個屬性 (property) 都會以 `JsonProperty` 屬性 (attribute) 裝飾，其中包含 JSON 欄位名稱。 Newtonsoft.Json 在將 JSON 資料還原序列化為模型物件時，會使用 JSON 欄位名稱與 CLR 屬性的這項對應。

    > [!NOTE]
    > 上述類別定義已經簡化，並不會將從 Web 服務擷取的 JSON 資料完全用於建立模型。

1. 在 [方案總管] 的 **WebServiceTutorial** 專案中，將名為 `RestService` 的新類別新增到此專案。 然後在 **RestService.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Collections.Generic;
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

            public async Task<List<Repository>> GetRepositoriesAsync(string uri)
            {
                List<Repository> repositories = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        repositories = JsonConvert.DeserializeObject<List<Repository>>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return repositories;
            }
        }
    }
    ```

    此程式碼會定義從 GitHub Web API 擷取 .NET 存放庫資料的單一方法 `GetRepositoriesAsync`。 這個方法會使用 `HttpClient.GetAsync` 方法，將 GET 要求傳送至 `uri` 引數所指定的 Web API。 Web API 會傳送儲存在 `HttpResponseMessage` 物件中的回應。 此回應包括 HTTP 狀態碼，這表示 HTTP 要求成功或失敗。 假設要求成功，Web API 就會回應 HTTP 狀態碼 200 (確定) 和 JSON 回應 (其位於 `HttpResponseMessage.Content` 屬性中)。 此 JSON 資料會先讀取到使用 `HttpContent.ReadAsStringAsync` 方法的 `string`，再使用 `JsonConvert.DeserializeObject` 方法還原序列化為 `List<Repository>` 物件。 此方法會使用 JSON 欄位名稱與 CLR 屬性之間的對應 (定義於 `Repository` 類別中) 來執行還原序列化。

1. 建置解決方案以確定沒有任何錯誤。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 [Solution Pad] 的 **WebServiceTutorial** 專案中，將名為 `Constants` 的新類別新增到此專案。 然後在 **Constants.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string GitHubReposEndpoint = "https://api.github.com/orgs/dotnet/repos";
        }
    }
    ```

    此程式碼會定義單一常數，也就是將對其提出 Web 要求的端點。

1. 在 [Solution Pad] 的 **WebServicesTutorial** 專案中，將名為 `Repository` 的新類別新增到此專案。 然後在 **Repository.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class Repository
        {
            [JsonProperty("name")]
            public string Name { get; set; }

            [JsonProperty("description")]
            public string Description { get; set; }

            [JsonProperty("html_url")]
            public Uri GitHubHomeUrl { get; set; }

            [JsonProperty("homepage")]
            public Uri Homepage { get; set; }

            [JsonProperty("watchers")]
            public int Watchers { get; set; }
        }
    }
    ```

    此程式碼會定義 `Repository` 類別，用於為從 Web 服務擷取的 JSON 資料建立模型。 每個屬性 (property) 都會以 `JsonProperty` 屬性 (attribute) 裝飾，其中包含 JSON 欄位名稱。 Newtonsoft.Json 在將 JSON 資料還原序列化為模型物件時，會使用 JSON 欄位名稱與 CLR 屬性的這項對應。

    > [!NOTE]
    > 上述類別定義已經簡化，並不會將從 Web 服務擷取的 JSON 資料完全用於建立模型。

1. 在 [Solution Pad] 的 **WebServiceTutorial** 專案中，將名為 `RestService` 的新類別新增到此專案。 然後在 **RestService.cs** 中，移除所有範本程式碼，並取代為下列程式碼：

    ```csharp
    using System;
    using System.Collections.Generic;
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

            public async Task<List<Repository>> GetRepositoriesAsync(string uri)
            {
                List<Repository> repositories = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        repositories = JsonConvert.DeserializeObject<List<Repository>>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return repositories;
            }
        }
    }
    ```

    此程式碼會定義從 GitHub Web API 擷取 .NET 存放庫資料的單一方法 `GetRepositoriesAsync`。 這個方法會使用 `HttpClient.GetAsync` 方法，將 GET 要求傳送至 `uri` 引數所指定的 Web API。 Web API 會傳送儲存在 `HttpResponseMessage` 物件中的回應。 此回應包括 HTTP 狀態碼，這表示 HTTP 要求成功或失敗。 假設要求成功，Web API 就會回應 HTTP 狀態碼 200 (確定) 和 JSON 回應 (其位於 `HttpResponseMessage.Content` 屬性中)。 此 JSON 資料會先讀取到使用 `HttpContent.ReadAsStringAsync` 方法的 `string`，再使用 `JsonConvert.DeserializeObject` 方法還原序列化為 `List<Repository>` 物件。 此方法會使用 JSON 欄位名稱與 CLR 屬性之間的對應 (定義於 `Repository` 類別中) 來執行還原序列化。

1. 建置解決方案以確定沒有任何錯誤。

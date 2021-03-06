---
title: 組態管理
description: 本章說明 eShopOnContainers 行動應用程式如何執行設定管理，以提供應用程式設定和使用者設定。
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 618b3ee23698ef2d5e74d0fc4997f5f2e215d47a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374234"
---
# <a name="configuration-management"></a>組態管理

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

設定允許將設定應用程式行為的資料區隔，而不需要重建應用程式。 設定有兩種類型：應用程式設定和使用者設定。

應用程式設定是應用程式所建立和管理的資料。 它可以包含固定的 web 服務端點、API 金鑰和執行時間狀態等資料。 應用程式設定會系結至應用程式的存在，而且只對該應用程式有意義。

使用者設定是可自訂的應用程式設定，其會影響應用程式的行為，而不需要經常重新調整。 例如，應用程式可能會讓使用者指定從中取出資料的位置，以及如何將它顯示在螢幕上。

Xamarin.Forms 包含可用於儲存設定資料的持續性字典。 您可以使用屬性來存取此字典 [`Application.Current.Properties`](xref:Xamarin.Forms.Application.Properties) ，並在應用程式進入睡眠狀態時，儲存放入其中的任何資料，並在應用程式繼續或重新開機時還原。 此外，此 [`Application`](xref:Xamarin.Forms.Application) 類別也有一種 [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) 方法，可讓應用程式在需要時儲存其設定。 如需此字典的詳細資訊，請參閱 [屬性字典](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary)。

使用持續性字典儲存資料的缺點 Xamarin.Forms 是不容易系結資料。 因此，eShopOnContainers 行動應用程式會使用可從 [NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/)取得的 [Xam] 設定程式庫。 此程式庫提供一致、型別安全、跨平臺的方法來保存和取得應用程式和使用者設定，同時使用每個平臺所提供的原生設定管理。 此外，您也可以直接使用資料系結來存取程式庫所公開的設定資料。

> [!NOTE]
> 雖然 [設定] 程式庫可以儲存應用程式和使用者設定，但這兩者之間並沒有任何差異。

## <a name="creating-a-settings-class"></a>建立設定類別

當您使用的是設定程式庫時，應該建立單一靜態類別，以包含應用程式所需的應用程式和使用者設定。 下列程式碼範例顯示 eShopOnContainers 行動裝置應用程式中的 Settings 類別：

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

您可以透過「Xam. 設定」程式庫所提供的 API 來讀取和寫入設定 `ISettings` 。 此程式庫提供可以用來存取 API 的 singleton， `CrossSettings.Current` 而應用程式的設定類別應該透過屬性公開這個 singleton `ISettings` 。

> [!NOTE]
> 使用外掛程式的指示詞。設定和外掛程式。抽象命名空間應該加入至需要存取的類別，而該類別需要存取的是設定程式庫類型。

## <a name="adding-a-setting"></a>新增設定

每個設定都是由索引鍵、預設值和屬性所組成。 下列程式碼範例會顯示使用者設定的三個專案，代表 eShopOnContainers 行動應用程式連接之線上服務的基底 URL：

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

索引鍵一律是定義索引鍵名稱的 const 字串，而設定的預設值是必要型別的靜態唯讀值。 提供預設值，可確保在抓取未設定的設定時，可以使用有效的值。

`UrlBase`靜態屬性會使用 API 的兩個方法 `ISettings` 來讀取或寫入設定值。 `ISettings.GetValueOrDefault`方法是用來從平臺特定的儲存體抓取設定的值。 如果未定義設定的值，則會改為取出其預設值。 同樣地， `ISettings.AddOrUpdateValue` 方法是用來將設定的值保存到平臺特定的儲存體。

它不會在類別內定義預設值，而是 `Settings` `UrlBaseDefault` 從類別取得其值 `GlobalSetting` 。 下列程式碼範例顯示 `BaseEndpoint` `UpdateEndpoint` 此類別中的屬性和方法：

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

每次 `BaseEndpoint` 設定屬性時， `UpdateEndpoint` 就會呼叫方法。 這個方法會更新一系列的屬性，這些屬性全都以類別所提供的使用者設定為基礎，而該 `UrlBase` `Settings` 類別代表 eShopOnContainers 行動應用程式所連接的不同端點。

## <a name="data-binding-to-user-settings"></a>資料系結至使用者設定

在 eShopOnContainers 行動裝置應用程式中，會 `SettingsView` 公開兩個使用者設定。 這些設定可讓您設定應用程式是否應該從部署為 Docker 容器的微服務中取出資料，或應用程式是否應該從不需要網際網路連線的 mock 服務中取出資料。 選擇從容器化微服務取得資料時，必須指定微服務的基底端點 URL。 [圖 7-1] 顯示 `SettingsView` 使用者已選擇從容器化微服務取出資料的時機。

![EShopOnContainers 行動應用程式公開的使用者設定](configuration-management-images/settings-endpoint.png)

**圖 7-1** ： eShopOnContainers 行動應用程式公開的使用者設定

您可以使用資料系結來取得和設定類別所公開的設定 `Settings` 。 這是由 view 系結上的控制項來達成，以依序存取類別中的屬性 `Settings` ，以及在設定值變更時引發屬性變更通知的模型屬性。 如需 eShopOnContainers 行動應用程式如何建立模型並將其與視圖產生關聯的詳細資訊，請參閱 [使用 View Model 定位器自動建立視圖模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator)。

下列程式碼範例顯示的 [`Entry`](xref:Xamarin.Forms.Entry) 控制項，可 `SettingsView` 讓使用者輸入容器化微服務的基底端點 URL：

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

這個 [`Entry`](xref:Xamarin.Forms.Entry) 控制項會使用雙向系結，系結至 `Endpoint` 類別的屬性 `SettingsViewModel` 。 下列程式碼範例顯示端點屬性：

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

當 `Endpoint` 設定屬性時，如果 `UpdateEndpoint` 提供的值有效，則會呼叫方法，並引發屬性變更通知。 下列程式碼範例示範 `UpdateEndpoint` 方法：

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

這個方法 `UrlBase` `Settings` 會使用使用者輸入的基底端點 URL 值來更新類別中的屬性，這樣會將它保存到平臺特定的儲存體。

當 `SettingsView` 流覽至時， `InitializeAsync` `SettingsViewModel` 會執行類別中的方法。 下列程式碼範例示範此方法：

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

方法會將 `Endpoint` 屬性設定為 `UrlBase` 類別中的屬性值 `Settings` 。 存取 `UrlBase` 屬性會導致「Xam. 設定程式庫」從平臺特定的儲存體取出設定值。 如需如何叫用方法的詳細資訊 `InitializeAsync` ，請參閱 [導覽期間傳遞參數](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing-parameters-during-navigation)。

這種機制可確保每當使用者流覽至 SettingsView 時，就會從平臺特定的儲存體取出使用者設定，並透過資料系結來呈現。 然後，如果使用者變更設定值，資料系結可確保它們立即保存回平臺特定的儲存體。

## <a name="summary"></a>總結

設定允許將設定應用程式行為的資料區隔，而不需要重建應用程式。 應用程式設定是應用程式所建立和管理的資料，而使用者設定是可自訂的應用程式設定，其會影響應用程式的行為，而不需要經常重新調整。

Xam. 設定程式庫提供一致、型別安全、跨平臺的方法來保存和取得應用程式和使用者設定，而資料系結可用來存取以程式庫建立的設定。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)

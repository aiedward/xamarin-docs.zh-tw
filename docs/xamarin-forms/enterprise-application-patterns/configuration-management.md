---
title: 設定管理
description: 本章說明 eShopOnContainers mobile 應用程式如何執行設定管理，以提供應用程式設定和使用者設定。
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: cc83a18cd8c391c6228cf9d813ecf8bca795caba
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760286"
---
# <a name="configuration-management"></a>設定管理

設定可讓您從程式碼中區隔的資料，來設定應用程式的行為，以在不重建應用程式的情況下變更行為。 設定有兩種類型： [應用程式設定] 和 [使用者設定]。

應用程式設定是應用程式所建立及管理的資料。 它可以包含固定的 web 服務端點、API 金鑰和執行時間狀態之類的資料。 應用程式設定會系結至應用程式的存在，而且只對該應用程式有意義。

使用者設定是應用程式的可自訂設定，會影響應用程式的行為，而不需要經常重新調整。 例如，應用程式可能會讓使用者指定要從何處取得資料，以及如何在螢幕上顯示資料。

[Xamarin] 包含可用於儲存設定資料的持續性字典。 這個字典可以使用[`Application.Current.Properties`](xref:Xamarin.Forms.Application.Properties)屬性來存取，而放入其中的任何資料都會在應用程式進入睡眠狀態時儲存，並在應用程式繼續或重新開機時還原。 此外， [`Application`](xref:Xamarin.Forms.Application)類別也有一[`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync)種方法，可讓應用程式在需要時儲存其設定。 如需此字典的詳細資訊，請參閱[屬性字典](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary)。

使用 Xamarin 的持久性字典來儲存資料的缺點是，它不容易與資料系結在一起。 因此，eShopOnContainers 行動應用程式會使用[NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/)的 [設定] 程式庫。 此程式庫提供一致、型別安全、跨平臺的方式來保存和取得應用程式和使用者設定，同時使用每個平臺提供的原生設定管理。 此外，您也可以直接使用資料系結來存取程式庫所公開的設定資料。

> [!NOTE]
> 雖然 [Xam] 設定程式庫可以儲存應用程式和使用者設定，但這兩者並不會區分兩者。

## <a name="creating-a-settings-class"></a>建立設定類別

使用 [Xam] 設定程式庫時，應該建立單一靜態類別，其中將包含應用程式所需的應用程式和使用者設定。 下列程式碼範例顯示 eShopOnContainers 行動裝置應用程式中的 Settings 類別：

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

您可以透過`ISettings`應用程式開發介面來讀取和寫入設定。 此程式庫提供一個可用於存取 API `CrossSettings.Current`的 singleton，而應用程式的設定類別應該透過`ISettings`屬性公開此 singleton。

> [!NOTE]
> 針對外掛程式使用指示詞。設定和外掛程式命名空間應新增至需要存取的類別，以進行已設定的應用程式庫類型。

## <a name="adding-a-setting"></a>新增設定

每個設定都是由一個索引鍵、一個預設值和一個屬性所組成。 下列程式碼範例顯示使用者設定的全部三個專案，代表 eShopOnContainers 的行動應用程式連接之線上服務的基底 URL：

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

金鑰一律是定義索引鍵名稱的 const 字串，而設定的預設值則是所需類型的靜態唯讀值。 提供預設值，可確保在抓取未設定的設定時，可以使用有效的值。

靜態屬性會使用`ISettings` API 中的兩個方法來讀取或寫入設定值。 `UrlBase` `ISettings.GetValueOrDefault`方法是用來從平臺特定的儲存體中抓取設定的值。 如果設定未定義任何值，則會改為抓取其預設值。 同樣地， `ISettings.AddOrUpdateValue`方法是用來將設定的值保存到平臺特定的儲存體。

`GlobalSetting`此`Settings` 字串會在類別中定義預設值，而不會從類別取得其值。`UrlBaseDefault` 下列程式碼範例顯示此`BaseEndpoint`類別中`UpdateEndpoint`的屬性和方法：

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

每次`BaseEndpoint`設定屬性時`UpdateEndpoint` ，就會呼叫方法。 這個方法會更新一系列的屬性，這些都是以`UrlBase` `Settings`類別所提供的使用者設定為基礎，而其代表 eShopOnContainers 的行動應用程式所連接的不同端點。

## <a name="data-binding-to-user-settings"></a>資料系結至使用者設定

在 eShopOnContainers 行動裝置應用程式中`SettingsView` ，會公開兩個使用者設定。 這些設定可讓您設定應用程式是否應從部署為 Docker 容器的微服務中抓取資料，或應用程式是否應從不需要網際網路連線的模擬服務中抓取資料。 選擇從容器化微服務中取出資料時，必須指定微服務的基底端點 URL。 [圖 7-1] `SettingsView`顯示使用者選擇從容器化微服務取得資料的時機。

![](configuration-management-images/settings-endpoint.png "EShopOnContainers 行動應用程式所公開的使用者設定")

**圖 7-1**：EShopOnContainers 行動應用程式所公開的使用者設定

資料系結可用於抓取和設定類別所`Settings`公開的設定。 這是由 view 系結上的控制項所達成，以查看模型屬性（後者會接著`Settings`存取類別中的屬性），並在設定值變更時引發屬性變更通知。 如需 eShopOnContainers 行動應用程式如何建立視圖模型並將其與視圖產生關聯的詳細資訊，請參閱[使用視圖模型定位器自動建立視圖模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

下列[`Entry`](xref:Xamarin.Forms.Entry) `SettingsView`程式碼範例顯示的控制項，可讓使用者輸入容器化微服務的基底端點 URL：

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

這個[`Entry`](xref:Xamarin.Forms.Entry)控制項會使用雙向系`Endpoint`結系結`SettingsViewModel`至類別的屬性。 下列程式碼範例顯示端點屬性：

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

當設定`UpdateEndpoint`屬性時，會呼叫方法，前提是提供的值是有效的，而且會引發屬性變更的通知。 `Endpoint` 下列程式碼範例示範 `UpdateEndpoint` 方法：

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

這個方法會將`UrlBase` `Settings`類別中的屬性更新為使用者所輸入的基底端點 URL 值，這會讓它保存到平臺特定的儲存區。

當流覽至時，會執行`InitializeAsync` `SettingsViewModel`類別中的方法。 `SettingsView` 下列程式碼範例示範此方法：

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

方法會將`Endpoint`屬性設定為`Settings`類別中的`UrlBase`屬性值。 `UrlBase`存取屬性會使 [，設定程式庫] 從平臺特定的儲存區抓取設定值。 如需如何`InitializeAsync`叫用方法的詳細資訊，請參閱[導覽期間傳遞參數](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

此機制可確保每當使用者流覽至 SettingsView 時，就會從平臺特定的儲存體抓取使用者設定，並透過資料系結來呈現。 然後，如果使用者變更設定值，資料系結可確保它們會立即保存回平臺特定的儲存體。

## <a name="summary"></a>總結

設定可讓您從程式碼中區隔的資料，來設定應用程式的行為，以在不重建應用程式的情況下變更行為。 應用程式設定是應用程式所建立及管理的資料，而使用者設定則是應用程式的可自訂設定，會影響應用程式的行為，而不需要經常重新調整。

此 [設定] 程式庫提供一致、型別安全、跨平臺的方法來保存和取得應用程式和使用者設定，而且可以使用資料系結來存取以程式庫建立的設定。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)

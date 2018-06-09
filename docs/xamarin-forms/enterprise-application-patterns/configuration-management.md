---
title: 設定管理
description: 本章說明 eShopOnContainers 行動裝置應用程式如何實作 configuration management 以提供應用程式設定和使用者設定。
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d6cd9771760bc2932345fec24887842ce1c47376
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243947"
---
# <a name="configuration-management"></a>設定管理

設定可讓設定程式碼中，從應用程式的行為的資料分離允許變更而不需重建應用程式的行為。 有兩種類型的設定： 應用程式設定和使用者設定。

應用程式設定是應用程式建立及管理的資料。 它可以包含資料，例如固定的 web 服務端點、 API 金鑰，以及執行階段狀態。 應用程式設定會繫結至應用程式存在，而且該應用程式有意義。

使用者設定為可自訂的應用程式設定會影響應用程式的行為，不需要頻繁的重新調整。 例如，應用程式可能會讓使用者指定要從何處擷取資料的來源，以及如何在螢幕上顯示它。

Xamarin.Forms 可包括持續性的字典，其中可以用來儲存設定資料。 可以使用來存取此字典[ `Application.Current.Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/)屬性，並放入它的任何資料時應用程式會進入睡眠狀態，以及應用程式繼續或重新啟動時還原儲存。 此外， [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)類別也有[ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/)方法，可讓應用程式必須儲存時所需的設定。 如需有關此字典的詳細資訊，請參閱[屬性字典](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary)。

使用 Xamarin.Forms 永續性字典來儲存資料的缺點是它不是輕鬆地繫結至資料。 因此，eShopOnContainers 行動裝置應用程式會使用 Xam.Plugins.Settings 程式庫，可從[NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/)。 此程式庫提供一致、 類型安全的跨平台的方法，來保存及擷取應用程式和使用者設定，同時使用每個平台所提供的原生設定管理。 此外，它是直接使用資料繫結來存取程式庫所公開的設定資料。

> [!NOTE]
> 雖然 Xam.Plugin.Settings 程式庫可以儲存應用程式和使用者的設定，但更能兩者之間沒有差別。

## <a name="creating-a-settings-class"></a>建立設定類別

使用 Xam.Plugins.Settings 庫時，單一靜態類別，您應該建立包含應用程式所需的應用程式和使用者設定。 下列程式碼範例顯示 eShopOnContainers 行動應用程式中的設定類別：

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

可以讀取和寫入設定`ISettings`Xam.Plugins.Settings 程式庫所提供的 API。 此程式庫提供可用於存取 API，為單一子句`CrossSettings.Current`，和應用程式的設定類別應該公開透過此 singleton`ISettings`屬性。

> [!NOTE]
> 應加入的 Plugin.Settings 和 Plugin.Settings.Abstractions 命名空間的 using 指示詞，需要存取 Xam.Plugins.Settings 程式庫類型的類別。

## <a name="adding-a-setting"></a>新增設定

每個設定組成索引鍵、 預設值和屬性。 下列程式碼範例顯示三個的所有項目代表 eShopOnContainers 行動裝置應用程式連接到線上服務的基底 URL 的使用者設定：

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

索引鍵一定是定義索引鍵名稱的常數字串，設定的預設值是必要的類型的靜態唯讀值。 提供預設值，可確保時如果未設定的設定會擷取可用的有效值。

`UrlBase`靜態屬性會使用兩種方法從`ISettings`API 來讀取或寫入的設定值。 `ISettings.GetValueOrDefault`方法用來擷取平台特定的儲存體設定的值。 如果沒有值定義的設定，就會改為擷取其預設值。 同樣地，`ISettings.AddOrUpdateValue`方法用來保存特定平台儲存體設定的值。

而是可定義內的預設值`Settings`類別`UrlBaseDefault`字串取得其值從`GlobalSetting`類別。 下列程式碼範例示範`BaseEndpoint`屬性和`UpdateEndpoint`此類別中的方法：

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

每次`BaseEndpoint`設定屬性，則`UpdateEndpoint`方法呼叫。 這個方法會更新一系列的屬性，全部都是根據`UrlBase`所提供的使用者設定`Settings`類別代表 eShopOnContainers 行動裝置應用程式連接到不同的端點。

## <a name="data-binding-to-user-settings"></a>資料繫結至使用者設定

EShopOnContainers 行動應用程式，`SettingsView`會公開兩個使用者設定。 這些設定可讓應用程式部署為 Docker 容器的 microservices 從擷取資料的是否或是否應用程式應該擷取的資料不需要網際網路連線的模擬服務的組態。 在選擇從容器化 microservices 擷取資料時，就必須指定 microservices 的基底的端點 URL。 圖 7-1 顯示`SettingsView`當使用者已選擇從容器化 microservices 擷取資料。

![](configuration-management-images/settings-endpoint.png "EShopOnContainers 行動裝置應用程式所公開的使用者設定")

**圖 7-1**: eShopOnContainers 行動裝置應用程式所公開的使用者設定

資料繫結可用來擷取和設定由`Settings`類別。 這藉由檢視模型屬性，依序存取中的屬性將檢視繫結上的控制項`Settings`類別，以及引發屬性變更通知的設定值已變更。 資訊 eShopOnContainers 行動裝置應用程式如何建構檢視模型，並將這些檢視，請參閱[自動建立檢視模型的檢視模型定位器](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

下列程式碼範例示範[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項從`SettingsView`，可讓使用者輸入的容器化 microservices 基底的端點 URL:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

這[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項繫結至`Endpoint`屬性`SettingsViewModel`類別，使用雙向繫結。 下列程式碼範例示範端點屬性：

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

當`Endpoint`屬性設定`UpdateEndpoint`方法呼叫，前提是所提供的值是否有效，而且屬性變更引發通知。 下列程式碼範例示範`UpdateEndpoint`方法：

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

這個方法會更新`UrlBase`屬性`Settings`使用者，使它保存至特定平台儲存體所輸入的類別，而基底的端點 URL 值。

當`SettingsView`巡覽，`InitializeAsync`方法中的`SettingsViewModel`類別執行。 下列程式碼範例會示範這個方法：

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

方法會設定`Endpoint`屬性設為值的`UrlBase`屬性`Settings`類別。 存取`UrlBase`屬性造成 Xam.Plugins.Settings 程式庫來擷取平台特定的儲存體中的設定值。 如需有關資訊`InitializeAsync`叫用方法時，請參閱[期間瀏覽傳遞參數](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

此機制可確保，每當使用者巡覽至 SettingsView，使用者設定會擷取從平台特定的儲存體，而且透過資料繫結顯示。 然後，如果使用者變更設定值，資料繫結可確保它們會立即保存回特定平台儲存體。

## <a name="summary"></a>總結

設定可讓設定程式碼中，從應用程式的行為的資料分離允許變更而不需重建應用程式的行為。 應用程式設定應用程式建立及管理的資料，而使用者設定應用程式的可自訂設定會影響應用程式的行為，不需要頻繁的重新調整。

Xam.Plugins.Settings 程式庫會提供一致的類型安全，跨平台的方法，來保存及擷取應用程式和使用者設定和資料繫結可用於存取建立與該媒體櫃的設定。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

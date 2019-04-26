---
title: 設定管理
description: 本章將說明如何在 eShopOnContainers 的行動應用程式實作組態管理，必須提供應用程式設定和使用者設定。
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 6f32d8f328232bdfc644da57bdb3201c60010063
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61381893"
---
# <a name="configuration-management"></a>設定管理

設定可讓設定程式碼中，從應用程式行為的資料分離，允許變更而不需重建應用程式的行為。 有兩種設定類型： 應用程式設定和使用者設定。

應用程式設定是應用程式建立和管理的資料。 它可以包含固定的 web 服務端點、 API 金鑰等執行階段狀態資料。 應用程式設定會繫結至應用程式存在和才有意義，該應用程式。

使用者設定是可自訂的應用程式設定會影響應用程式的行為，不需要經常重新調整。 例如，應用程式可能會讓使用者指定要從何處擷取資料，以及如何顯示在螢幕上。

Xamarin.Forms 包含持續性的字典，其可用來儲存設定資料。 可以使用來存取此字典[ `Application.Current.Properties` ](xref:Xamarin.Forms.Application.Properties)時應用程式會進入睡眠狀態，並還原應用程式會繼續或重新啟動時，則便會儲存屬性，並放入它的任何資料。 颾魤 ㄛ [ `Application` ](xref:Xamarin.Forms.Application)類別也有[ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync)讓應用程式有需要時儲存其設定的方法。 如需有關這個字典的詳細資訊，請參閱 < [Properties 字典](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary)。

使用 Xamarin.Forms 的永續性字典儲存資料缺點是它不是輕鬆地繫結至資料。 因此，eShopOnContainers 的行動裝置應用程式會使用 Xam.Plugins.Settings 程式庫，可從[NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/)。 此程式庫提供一致、 型別安全、 跨平台的方法，用於保存資料及擷取應用程式和使用者設定，同時使用每個平台所提供的原生的設定管理。 此外，它可以直接使用資料繫結來存取程式庫所公開的設定資料。

> [!NOTE]
> 雖然 Xam.Plugin.Settings 程式庫可以存放應用程式和使用者的設定，它會使兩者之間沒有差別。

## <a name="creating-a-settings-class"></a>建立設定類別

使用 Xam.Plugins.Settings 庫時，單一靜態類別，您應該建立將包含應用程式所需的應用程式和使用者設定。 下列程式碼範例會顯示在 eShopOnContainers 的行動裝置應用程式設定類別：

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

設定可讀取 / 寫入透過`ISettings`Xam.Plugins.Settings 程式庫所提供的 API。 此程式庫提供可用來存取 API，singleton `CrossSettings.Current`，和應用程式的設定類別應該公開透過此 singleton`ISettings`屬性。

> [!NOTE]
> Plugin.Settings 和 Plugin.Settings.Abstractions 命名空間的 using 指示詞應該新增至需要 Xam.Plugins.Settings 程式庫類型的存取權的類別。

## <a name="adding-a-setting"></a>新增設定

每個設定是由索引鍵、 預設值和屬性所組成。 下列程式碼範例顯示的使用者設定值，表示在 eShopOnContainers 的行動應用程式連接到線上服務的基底 URL 的所有三個項目：

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

索引鍵一定是 const 字串，可定義索引鍵的名稱，與設定的預設值所需要的類型的靜態唯讀值。 提供預設值，可確保有效的值會使用擷取的未設定的設定。

`UrlBase`靜態屬性會使用兩種方法從`ISettings`API 來讀取或寫入的設定值。 `ISettings.GetValueOrDefault`方法用來從特定平台儲存體擷取設定的值。 如果未定義值的設定，就會改為擷取其預設值。 同樣地，`ISettings.AddOrUpdateValue`方法用來保存到平台特定的儲存體設定的值。

相反地，其定義內的預設值`Settings`類別，`UrlBaseDefault`字串取得其值，從`GlobalSetting`類別。 下列程式碼範例所示`BaseEndpoint`屬性和`UpdateEndpoint`這個類別中的方法：

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

每次`BaseEndpoint`屬性設定，`UpdateEndpoint`呼叫方法。 這個方法會更新一系列的屬性，其中都根據`UrlBase`所提供的使用者設定`Settings`代表不同的端點，eShopOnContainers 的行動裝置應用程式連接到的類別。

## <a name="data-binding-to-user-settings"></a>資料繫結至使用者設定

在 eShopOnContainers 的行動裝置應用程式，`SettingsView`會公開兩個使用者設定。 這些設定可讓您不論應用程式時，應該從已部署為 Docker 容器，微服務擷取資料，或是否應用程式時，應該不需要網際網路連線的模擬 （mock） 服務擷取資料的設定。 選擇以擷取容器化微服務中的資料時，就必須指定微服務的基底的端點 URL。 圖 7-1 顯示`SettingsView`當使用者選擇來擷取容器化微服務中的資料。

![](configuration-management-images/settings-endpoint.png "EShopOnContainers 的行動裝置應用程式所公開的使用者設定")

**圖 7-1**:EShopOnContainers 的行動裝置應用程式所公開的使用者設定

資料繫結可用來擷取和設定由`Settings`類別。 這檢視模型屬性，依序存取中的內容將檢視繫結上的控制項來達成`Settings`類別，以及引發屬性變更通知的設定值已變更。 如 eShopOnContainers 的行動裝置應用程式如何建構檢視的相關資訊的模型，並將它們關聯至檢視，請參閱 <<c0> [ 自動建立檢視模型的檢視模型定位器](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

下列程式碼範例所示[ `Entry` ](xref:Xamarin.Forms.Entry)控制項從`SettingsView`，可讓使用者輸入的容器化微服務的基底的端點 URL:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

這[ `Entry` ](xref:Xamarin.Forms.Entry)控制項繫結至`Endpoint`屬性`SettingsViewModel`類別，使用雙向繫結。 下列程式碼範例會顯示端點屬性：

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

當`Endpoint`屬性設定`UpdateEndpoint`呼叫方法，前提是所提供的值是否有效，而且屬性變更引發通知。 下列程式碼範例示範 `UpdateEndpoint` 方法：

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

這個方法會更新`UrlBase`屬性中的`Settings`使用者，這會導致它保存到平台特定儲存體輸入類別的基底的端點 URL 值。

當`SettingsView`，巡覽`InitializeAsync`方法中的`SettingsViewModel`類別會執行。 下列程式碼範例示範此方法：

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

方法會設定`Endpoint`屬性的值`UrlBase`屬性中的`Settings`類別。 存取`UrlBase`屬性造成 Xam.Plugins.Settings 程式庫，來擷取特定平台儲存體中的設定值。 如需有關的資訊`InitializeAsync`叫用方法時，請參閱 <<c2> [ 期間瀏覽並傳遞參數](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

這項機制可確保，每當使用者導覽到 SettingsView，使用者設定是從平台特定的儲存體擷取並顯示透過資料繫結。 然後，如果使用者變更設定值，資料繫結會確保它們會立即保存到平台特定的儲存體。

## <a name="summary"></a>總結

設定可讓設定程式碼中，從應用程式行為的資料分離，允許變更而不需重建應用程式的行為。 應用程式設定資料的應用程式建立和管理，而使用者設定可自訂的應用程式設定會影響應用程式的行為，不需要經常重新調整。

Xam.Plugins.Settings 程式庫提供一致的型別安全、 跨平台來保存和擷取應用程式和使用者設定和資料繫結的方法可以用來存取與程式庫所建立的設定。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

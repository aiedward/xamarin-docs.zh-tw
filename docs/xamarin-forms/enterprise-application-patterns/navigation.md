---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9b2ee9fb02a8fd18d69e93424dc76bfd54fafc86
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130893"
---
# <a name="enterprise-app-navigation"></a>企業應用程式流覽

Xamarin.Forms包含頁面導覽的支援，這通常是因為內部邏輯驅動狀態變更而導致使用者與 UI 互動或從應用程式本身進行互動。 不過，在使用 ViewModel （MVVM）模式的應用程式中執行流覽可能會很複雜，因為必須符合下列挑戰：

- 如何識別要導覽的視圖，並使用不會在 views 之間引入緊密結合和相依性的方法。
- 如何在具現化和初始化的情況之下，協調要導覽的目標進程。 使用 MVVM 時，view 和 view 模型需要具現化，並透過視圖的系結內容彼此關聯。 當應用程式使用相依性插入容器時，views 和 view 模型的具現化可能需要特定的結構機制。
- 是否要執行視圖優先導覽，或視圖模型優先導覽。 使用 [視圖優先導覽]，流覽至的頁面會參考檢視類型的名稱。 在導覽期間，指定的視圖會具現化，連同其對應的視圖模型和其他相依服務。 另一個方法是使用 [視圖模型優先導覽]，其中流覽至的頁面會參考視圖模型類型的名稱。
- 如何將應用程式的導覽行為明確地區隔在 views 和 view 模型上。 MVVM 模式可讓應用程式的 UI 和其簡報與商務邏輯分開。 不過，應用程式的流覽行為通常會跨越應用程式的 UI 和簡報元件。 使用者通常會從視圖起始導覽，而此視圖會被導覽的結果取代。 不過，導覽通常也可能需要從視圖模型中起始或協調。
- 如何在導覽期間傳遞參數以供初始化之用。 例如，如果使用者流覽至要更新訂單詳細資料的視圖，訂單資料就必須傳遞至視圖，才能顯示正確的資料。
- 如何共同座標導覽，以確保遵守特定的商務規則。 例如，使用者可能會在離開視圖之前出現提示，讓他們可以更正任何不正確資料，或提示您提交或捨棄在此視圖內所做的任何資料變更。

本章藉由呈現 `NavigationService` 用來執行視圖模型第一頁導覽的類別，來解決這些挑戰。

> [!NOTE]
> `NavigationService`應用程式所使用的是設計來執行 ContentPage 實例之間的階層式導覽。 使用服務在其他頁面類型之間流覽，可能會導致非預期的行為。

## <a name="navigating-between-pages"></a>在頁面之間流覽

導覽邏輯可以位於視圖的程式碼後置中，或在資料系結視圖模型中。 雖然將導覽邏輯放在視圖中可能是最簡單的方法，但無法透過單元測試輕鬆進行測試。 將導覽邏輯放在視圖模型類別中，表示邏輯可以透過單元測試來執行。 此外，視圖模型接著可以執行邏輯來控制導覽，以確保強制執行特定商務規則。 例如，應用程式可能不會允許使用者在不先確定輸入的資料是否有效的情況下，離開頁面。

`NavigationService`通常會從視圖模型叫用類別，以提升可測試性。 不過，從視圖模型流覽至 views 時，需要 view 模型來參考 views，特別是使用中視圖模型未與相關聯的視圖，這不是建議的做法。 因此，此處顯示的會將 `NavigationService` 視圖模型類型指定為要導覽的目標。

EShopOnContainers 行動應用程式會使用 `NavigationService` 類別來提供視圖模型優先導覽。 這個類別會實作為 `INavigationService` 介面，如下列程式碼範例所示：

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

這個介面會指定實作為類別必須提供下列方法：

|方法|目的|
|--- |--- |
|`InitializeAsync`|啟動應用程式時，執行兩個頁面的其中一個。|
|`NavigateToAsync`|執行指定頁面的階層式導覽。|
|`NavigateToAsync(parameter)`|執行指定頁面的階層式導覽，傳遞參數。|
|`RemoveLastFromBackStackAsync`|從導覽堆疊中移除前一頁。|
|`RemoveBackStackAsync`|從導覽堆疊中移除所有先前的頁面。|

此外， `INavigationService` 介面會指定實作為類別必須提供 `PreviousPageViewModel` 屬性。 這個屬性會傳回與導覽堆疊中的上一頁相關聯的視圖模型類型。

> [!NOTE]
> `INavigationService`介面通常也會指定 `GoBackAsync` 方法，用來以程式設計方式回到導覽堆疊中的上一頁。 不過，eShopOnContainers 行動應用程式中遺漏了這個方法，因為它不是必要的。

### <a name="creating-the-navigationservice-instance"></a>建立 NavigationService 實例

`NavigationService` `INavigationService` 執行介面的類別會註冊為具有 Autofac 相依性插入容器的單一物件，如下列程式碼範例所示：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

`INavigationService`介面會在類別的函式中解析 `ViewModelBase` ，如下列程式碼範例所示：

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

這 `NavigationService` 會傳回儲存在 Autofac 相依性插入容器（由類別中的方法所建立）之物件的參考 `InitNavigation` `App` 。 如需詳細資訊，請參閱在[應用程式啟動時進行流覽](#navigating_when_the_app_is_launched)。

類別會將 `ViewModelBase` `NavigationService` 實例儲存在 `NavigationService` 類型的屬性中 `INavigationService` 。 因此，所有衍生自類別的視圖模型類別，都 `ViewModelBase` 可以使用 `NavigationService` 屬性來存取介面所指定的方法 `INavigationService` 。 這可避免將 `NavigationService` 物件從 Autofac 相依性插入容器插入每個 view 模型類別的額外負荷。

### <a name="handling-navigation-requests"></a>處理導覽要求

Xamarin.Forms提供 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別，它會執行階層式導覽體驗，讓使用者能夠視需要向前和向後流覽頁面。 如需有關階層式導覽的詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

EShopOnContainers 應用程式不會直接使用類別，而是在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) `NavigationPage` 類別中包裝 `CustomNavigationView` 類別，如下列程式碼範例所示：

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

此包裝的目的是為了方便在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別的 XAML 檔案中設定實例的樣式。

導覽是在視圖模型類別內部執行，方法是叫用其中一個 `NavigateToAsync` 方法，指定所流覽之頁面的視圖模型類型，如下列程式碼範例所示：

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

下列程式碼範例顯示 `NavigateToAsync` 類別所提供的方法 `NavigationService` ：

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

每個方法都可讓衍生自類別的任何 view 模型類別，藉 `ViewModelBase` 由叫用方法來執行階層式導覽 `InternalNavigateToAsync` 。 此外，第二種 `NavigateToAsync` 方法可將導覽資料指定為傳遞至所流覽之視圖模型的引數，通常用來執行初始化。 如需詳細資訊，請參閱[導覽期間傳遞參數](#passing_parameters_during_navigation)。

`InternalNavigateToAsync`方法會執行流覽要求，如下列程式碼範例所示：

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

`InternalNavigateToAsync`方法會先呼叫方法，以執行導覽至視圖模型 `CreatePage` 。 這個方法會尋找對應至指定之視圖模型類型的視圖，並建立並傳回此檢視類型的實例。 尋找對應至視圖模型類型的視圖會使用以慣例為基礎的方法，其假設：

- Views 與 view 模型類型位於相同的元件中。
- Views 位於。Views 子命名空間。
- 視圖模型位於中。Viewmodel 子命名空間。
- 視圖名稱會對應至視圖模型名稱，並移除「模型」。

當視圖具現化時，它會與其對應的視圖模型相關聯。 如需如何發生這種情況的詳細資訊，請參閱[使用視圖模型定位器自動建立視圖模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

如果要建立的視圖為 `LoginView` ，則會將它包裝在類別的新實例中， `CustomNavigationView` 並指派給 [`Application.Current.MainPage`](xref:Xamarin.Forms.Application.MainPage) 屬性。 否則， `CustomNavigationView` 系統會抓取實例，並假設它不是 null，則會叫用方法，將 [`PushAsync`](xref:Xamarin.Forms.NavigationPage) 建立的視圖推送到導覽堆疊上。 不過，如果所抓取的 `CustomNavigationView` 實例是 `null` ，所建立的視圖就會包裝在類別的新實例中 `CustomNavigationView` ，並指派給 `Application.Current.MainPage` 屬性。 這項機制可確保在流覽期間，如果頁面是空的，且包含資料，則會在導覽堆疊中正確新增分頁。

> [!TIP]
> 考慮快取頁面。 頁面快取會導致目前未顯示之視圖的記憶體耗用量。 不過，在沒有頁面快取的情況下，這表示每次流覽新頁面時，都會發生 XAML 剖析和頁面的結構，而且它的視圖模型會對複雜頁面造成效能影響。 針對不使用過多控制項的設計良好頁面，效能應該就已足夠。 不過，如果遇到頁面載入速度緩慢的情況，頁面快取可能會有説明。

在建立並流覽至視圖之後， `InitializeAsync` 會執行視圖相關聯視圖模型的方法。 如需詳細資訊，請參閱[導覽期間傳遞參數](#passing_parameters_during_navigation)。

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>在應用程式啟動時進行流覽

啟動應用程式時，會叫 `InitNavigation` 用類別中的方法 `App` 。 下列程式碼範例示範此方法：

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

方法 `NavigationService` 會在 Autofac 相依性插入容器中建立新的物件，並傳回其參考，然後再叫用其 `InitializeAsync` 方法。

> [!NOTE]
> 當 `INavigationService` 類別解析介面時，容器會傳回叫用 `ViewModelBase` `NavigationService` InitNavigation 方法時所建立之物件的參考。

下列程式碼範例示範 `NavigationService` `InitializeAsync` 方法：

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

`MainView`如果應用程式具有快取存取權杖（用於驗證），則會流覽至。 否則， `LoginView` 會流覽至。

如需有關 Autofac 相依性插入容器的詳細資訊，請參閱相依性[插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>導覽期間傳遞參數

介面所指定的其中一個 `NavigateToAsync` 方法 `INavigationService` ，可讓導覽資料指定為傳遞至所流覽之視圖模型的引數，通常用來執行初始化。

例如， `ProfileViewModel` 類別包含 `OrderDetailCommand` 當使用者在頁面上選取訂單時所執行的 `ProfileView` 。 接著，這 `OrderDetailAsync` 會執行方法，如下列程式碼範例所示：

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

這個方法會叫用導覽 `OrderDetailViewModel` ，傳遞 `Order` 代表使用者在頁面上選取之順序的實例 `ProfileView` 。 當 `NavigationService` 類別建立時 `OrderDetailView` ，會具 `OrderDetailViewModel` 現化類別，並將其指派給視圖的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 導覽至之後 `OrderDetailView` ，方法會 `InternalNavigateToAsync` 執行 `InitializeAsync` 視圖相關聯視圖模型的方法。

`InitializeAsync`方法是在類別中定義 `ViewModelBase` 為可覆寫的方法。 這個方法會指定 `object` 引數，表示在導覽作業期間要傳遞至視圖模型的資料。 因此，要從導覽作業接收資料的視圖模型類別，會提供自己的 `InitializeAsync` 方法實作為執行必要的初始化。 下列程式碼範例顯示 `InitializeAsync` 類別中的方法 `OrderDetailViewModel` ：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

這個方法會在 `Order` 導覽作業期間，抓取傳入視圖模型的實例，並使用它來抓取實例的完整訂單詳細資料 `OrderService` 。

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>使用行為叫用導覽

流覽通常是由使用者互動從視圖觸發。 例如，會在 `LoginView` 驗證成功後執行導覽。 下列程式碼範例示範如何叫用導覽的行為：

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

在執行時間， `EventToCommandBehavior` 會回應與的互動 [`WebView`](xref:Xamarin.Forms.WebView) 。 當 `WebView` 流覽至網頁時， [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) 將會引發事件，這會 `NavigateCommand` 在中執行 `LoginViewModel` 。 根據預設，事件的事件引數會傳遞至命令。 這項資料會轉換，因為它是在來源和目標之間傳遞，並由屬性中指定的轉換器 `EventArgsConverter` （從傳回） [`Url`](xref:Xamarin.Forms.WebNavigationEventArgs.Url) [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) 。 因此，當 `NavigationCommand` 執行時，網頁的 Url 會當做參數傳遞至已註冊的 `Action` 。

接著， `NavigationCommand` `NavigateAsync` 會執行方法，如下列程式碼範例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

這個方法會叫用導覽 `MainViewModel` ，並在流覽之後， `LoginView` 從導覽堆疊中移除頁面。

### <a name="confirming-or-cancelling-navigation"></a>確認或取消導覽

在導覽作業期間，應用程式可能需要與使用者互動，讓使用者可以確認或取消導覽。 這可能是必要的，例如，當使用者嘗試在完全完成資料輸入頁面之前進行流覽。 在此情況下，應用程式應該會提供通知，讓使用者可以從頁面流覽，或在流覽作業發生前取消該作業。 使用來自通知的回應來控制是否叫用導覽，即可在視圖模型類別中達成此目的。

## <a name="summary"></a>摘要

Xamarin.Forms包含頁面導覽的支援，這通常是因使用者與 UI 的互動，或來自應用程式本身的結果，因為內部邏輯驅動狀態變更的結果。 不過，在使用 MVVM 模式的應用程式中執行流覽可能會很複雜。

這一章顯示的 `NavigationService` 類別，可用來執行視圖模型的第一次導覽。 將導覽邏輯放在視圖模型類別中，表示邏輯可以透過自動化測試來執行。 此外，視圖模型接著可以執行邏輯來控制導覽，以確保強制執行特定商務規則。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)

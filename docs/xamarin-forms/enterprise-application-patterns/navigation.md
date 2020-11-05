---
title: 企業應用程式流覽
description: 本章說明 eShopOnContainers 行動裝置應用程式如何從 view 模型執行 view model first 導覽。
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f35a213d279f756b1242fbac2a82ad6aeb928d2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375300"
---
# <a name="enterprise-app-navigation"></a>企業應用程式流覽

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

Xamarin.Forms 包含頁面導覽的支援，通常是因為內部邏輯驅動狀態變更而導致使用者與 UI 互動或應用程式本身的互動。 不過，在使用模型視圖 ViewModel (MVVM) 模式的應用程式中執行流覽可能很複雜，因為必須符合下列挑戰：

- 如何使用未在視圖之間引入緊密結合和相依性的方法，來識別要流覽的視圖。
- 如何協調要導覽的目標進程，並將其初始化。 使用 MVVM 時，必須透過視圖的系結內容，將 view 和 view 模型具現化，並與彼此產生關聯。 當應用程式使用相依性插入容器時，可具現化的視圖和視圖模型可能需要特定的結構機制。
- 是否要執行 view first 導覽，或查看模型優先導覽。 使用 view first 導覽時，流覽至的頁面會參考檢視類型的名稱。 在流覽期間，會將指定的視圖具現化，以及其對應的視圖模型和其他相依的服務。 替代方法是使用 view model first 導覽，其中要導覽的頁面會參考視圖模型類型的名稱。
- 如何將應用程式的導覽行為完全分開到各個視圖和視圖模型。 MVVM 模式會提供應用程式的 UI 與其展示與商務邏輯之間的分隔。 不過，應用程式的流覽行為通常會跨越 UI 和應用程式的簡報部分。 使用者通常會從 view 開始導覽，而此視圖將被取代為導覽的結果。 不過，導覽通常也可能需要從視圖模型內起始或協調。
- 如何在流覽期間傳遞參數以供初始化之用。 例如，如果使用者流覽至更新訂單詳細資料的視圖，則必須將訂單資料傳遞給視圖，才能顯示正確的資料。
- 如何共同座標導覽，以確保遵守特定商務規則。 例如，在離開視圖之前可能會提示使用者，讓他們可以更正任何不正確資料，或提示您提交或捨棄在視圖內進行的任何資料變更。

本章藉由呈現 `NavigationService` 用來執行視圖模型優先頁面導覽的類別，來解決這些挑戰。

> [!NOTE]
> `NavigationService`應用程式所使用的只是為了執行 ContentPage 實例之間的階層式導覽。 使用服務在其他頁面類型之間流覽可能會導致非預期的行為。

## <a name="navigating-between-pages"></a>在頁面之間流覽

導覽邏輯可以位於視圖的程式碼後置或資料系結視圖模型中。 雖然將導覽邏輯放在視圖中可能是最簡單的方法，但無法透過單元測試輕鬆進行測試。 將導覽邏輯放在 view 模型類別中，表示邏輯可透過單元測試進行。 此外，view 模型還可以執行邏輯來控制導覽，以確保強制執行某些商務規則。 例如，應用程式可能不會先確認輸入的資料是否有效，而無法允許使用者離開頁面。

`NavigationService`類別通常會從視圖模型中叫用，以提升可測試性。 不過，流覽至視圖模型的視圖需要視圖模型參考視圖，而且特別是現用視圖模型沒有關聯的視圖，這不是建議的做法。 因此，此處顯示的會將 `NavigationService` 視圖模型類型指定為要流覽的目標。

EShopOnContainers 行動裝置應用程式會使用 `NavigationService` 類別來提供視圖模型優先導覽。 這個類別 `INavigationService` 會執行介面，如下列程式碼範例所示：

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

這個介面會指定執行類別必須提供下列方法：

|方法|目的|
|--- |--- |
|`InitializeAsync`|當應用程式啟動時，執行流覽至兩個頁面的其中一個。|
|`NavigateToAsync`|執行指定頁面的階層式導覽。|
|`NavigateToAsync(parameter)`|執行指定頁面的階層式導覽，並傳遞參數。|
|`RemoveLastFromBackStackAsync`|從導覽堆疊中移除前一個頁面。|
|`RemoveBackStackAsync`|從導覽堆疊中移除所有先前的頁面。|

此外， `INavigationService` 介面會指定執行類別必須提供 `PreviousPageViewModel` 屬性。 這個屬性會傳回與導覽堆疊中上一頁相關聯的視圖模型類型。

> [!NOTE]
> `INavigationService`介面通常也會指定 `GoBackAsync` 方法，這個方法可用來以程式設計的方式返回導覽堆疊中的上一個頁面。 不過，eShopOnContainers 行動應用程式中遺漏了此方法，因為它不是必要的。

### <a name="creating-the-navigationservice-instance"></a>建立 NavigationService 實例

實 `NavigationService` 介面的類別會以 `INavigationService` Autofac 相依性插入容器的 singleton 來註冊，如下列程式碼範例所示：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

`INavigationService`介面會在類別的函式中解析 `ViewModelBase` ，如下列程式碼範例所示：

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

這會傳回 Autofac 相依性 `NavigationService` 插入容器中所儲存之物件的參考，該容器是由 `InitNavigation` 類別中的方法所建立 `App` 。 如需詳細資訊，請參閱在 [應用程式啟動時流覽](#navigating-when-the-app-is-launched)。

類別會將 `ViewModelBase` `NavigationService` 實例儲存在 `NavigationService` 類型的屬性中 `INavigationService` 。 因此，所有衍生自類別的視圖模型類別都 `ViewModelBase` 可以使用 `NavigationService` 屬性來存取介面所指定的方法 `INavigationService` 。 這可避免將 `NavigationService` 物件從 Autofac 相依性插入容器插入至每個 view 模型類別的額外負荷。

### <a name="handling-navigation-requests"></a>處理導覽要求

Xamarin.Forms 提供 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別，它會執行階層式導覽體驗，讓使用者能夠視需要在頁面上向前及向後流覽頁面。 如需有關階層式導覽的詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)EShopOnContainers 應用程式會將類別包裝在類別中，而不是直接使用類別 `NavigationPage` `CustomNavigationView` ，如下列程式碼範例所示：

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

這種包裝的目的是為了方便在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別的 XAML 檔案中設定實例樣式。

藉由叫用其中一個 `NavigateToAsync` 方法，並指定所要流覽之頁面的視圖模型類型（如下列程式碼範例所示），在 view 模型類別內部執行導覽：

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

下列程式碼範例顯示 `NavigateToAsync` 類別所提供的方法 `NavigationService` ：

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

每個方法都可讓衍生自類別的任何 view 模型類別，藉 `ViewModelBase` 由叫用方法來執行階層式導覽 `InternalNavigateToAsync` 。 此外，第二個 `NavigateToAsync` 方法可讓您將導覽資料指定為傳遞給要導覽之視圖模型的引數，通常用來執行初始化。 如需詳細資訊，請參閱 [在導覽期間傳遞參數](#passing-parameters-during-navigation)。

`InternalNavigateToAsync`方法會執行流覽要求，如下列程式碼範例所示：

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

`InternalNavigateToAsync`方法會先呼叫方法，以執行視圖模型的導覽 `CreatePage` 。 這個方法會找出對應于指定視圖模型類型的視圖，並建立並傳回此檢視類型的實例。 找出對應于視圖模型類型的視圖會使用以慣例為基礎的方法，其假設：

- 視圖與視圖模型類型位於相同的元件中。
- 視圖位於中。Views 子命名空間。
- 視圖模型在中。Viewmodel 子命名空間。
- 視圖名稱會對應至視圖模型名稱，並已移除「模型」。

當視圖具現化時，它會與其對應的視圖模型相關聯。 如需有關如何發生這種情況的詳細資訊，請參閱 [使用 View Model 定位器自動建立視圖模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator)。

如果要建立的視圖是，則會將 `LoginView` 它包裝在類別的新實例中， `CustomNavigationView` 並指派給 [`Application.Current.MainPage`](xref:Xamarin.Forms.Application.MainPage) 屬性。 否則， `CustomNavigationView` 會抓取實例，並假設它不是 null，則會叫用方法，將 [`PushAsync`](xref:Xamarin.Forms.NavigationPage) 建立的視圖推送至導覽堆疊上。 但是，如果已取出的 `CustomNavigationView` 實例為 `null` ，則建立的視圖會包裝在類別的新實例內 `CustomNavigationView` ，並指派給 `Application.Current.MainPage` 屬性。 這項機制可確保在流覽期間，頁面會在其為空白時，以及包含資料時，正確地新增至導覽堆疊。

> [!TIP]
> 考慮快取頁面。 頁面快取會導致目前未顯示之視圖的記憶體耗用量。 但是，如果沒有頁面快取，就表示每次流覽新頁面時，都會發生 XAML 剖析和建立頁面及其視圖模型，這樣可能會對複雜頁面產生效能影響。 針對未使用過多控制項的設計完善頁面，效能應該就已足夠。 不過，頁面快取可能有助於遇到頁面載入速度緩慢的情況。

建立視圖並導覽至之後， `InitializeAsync` 會執行視圖相關聯的視圖模型的方法。 如需詳細資訊，請參閱 [在導覽期間傳遞參數](#passing-parameters-during-navigation)。

### <a name="navigating-when-the-app-is-launched"></a>在應用程式啟動時流覽

當應用程式啟動時， `InitNavigation` 會叫用類別中的方法 `App` 。 下列程式碼範例示範此方法：

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

方法 `NavigationService` 會在 Autofac 相依性插入容器中建立新的物件，並在叫用其方法之前傳回它的參考 `InitializeAsync` 。

> [!NOTE]
> 當 `INavigationService` 類別解析介面時，容器會傳回在叫用 `ViewModelBase` `NavigationService` InitNavigation 方法時所建立之物件的參考。

下列程式碼範例示範 `NavigationService` `InitializeAsync` 方法：

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

`MainView`如果應用程式具有快取存取權杖（用於驗證），則會流覽至。 否則 `LoginView` 會流覽至。

如需有關 Autofac 相依性插入容器的詳細資訊，請參閱相依性 [插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction-to-dependency-injection)。

### <a name="passing-parameters-during-navigation"></a>在導覽期間傳遞參數

介面所指定的其中一個方法，可 `NavigateToAsync` `INavigationService` 讓您將導覽資料指定為傳遞給要導覽之視圖模型的引數，通常用來執行初始化。

例如， `ProfileViewModel` 類別包含在 `OrderDetailCommand` 使用者選取頁面上的訂單時所執行的 `ProfileView` 。 接著，這 `OrderDetailAsync` 會執行方法，如下列程式碼範例所示：

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

這個方法會叫用導覽 `OrderDetailViewModel` ，並傳遞 `Order` 代表使用者在頁面上選取之順序的實例 `ProfileView` 。 當 `NavigationService` 類別建立時 `OrderDetailView` ，會具 `OrderDetailViewModel` 現化類別，並將它指派給視圖的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 導覽至之後 `OrderDetailView` ，方法會 `InternalNavigateToAsync` 執行 `InitializeAsync` 視圖相關聯視圖模型的方法。

`InitializeAsync`方法會在類別中定義 `ViewModelBase` 為可覆寫的方法。 這個方法會指定 `object` 引數，表示在導覽作業期間傳遞給視圖模型的資料。 因此，要從導覽作業接收資料的視圖模型類別，會提供自己的方法實作為 `InitializeAsync` 執行所需的初始化。 下列程式碼範例顯示 `InitializeAsync` 類別中的方法 `OrderDetailViewModel` ：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

這個方法會抓取 `Order` 導覽作業期間傳遞至視圖模型的實例，並使用它來從實例中取出完整的訂單詳細資料 `OrderService` 。

### <a name="invoking-navigation-using-behaviors"></a>使用行為叫用導覽

流覽通常是由使用者互動的視圖所觸發。 例如，在 `LoginView` 成功驗證之後執行導覽。 下列程式碼範例示範如何叫用導覽：

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

在執行時間， `EventToCommandBehavior` 會回應與的互動 [`WebView`](xref:Xamarin.Forms.WebView) 。 當 `WebView` 流覽至網頁時， [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) 會引發此事件，這會 `NavigateCommand` 在中執行 `LoginViewModel` 。 根據預設，事件的事件引數會傳遞至命令。 這項資料會在由屬性中指定的轉換器于來源與目標之間傳遞時進行轉換 `EventArgsConverter` ，而這會 [`Url`](xref:Xamarin.Forms.WebNavigationEventArgs.Url) 從傳回 [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) 。 因此，當 `NavigationCommand` 執行時，會將網頁的 Url 做為參數傳遞給已註冊的 `Action` 。

接著， `NavigationCommand` `NavigateAsync` 會執行方法，如下列程式碼範例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

這個方法會叫用導覽 `MainViewModel` ，並在流覽之後， `LoginView` 從導覽堆疊中移除頁面。

### <a name="confirming-or-cancelling-navigation"></a>確認或取消導覽

在流覽作業期間，應用程式可能需要與使用者互動，讓使用者可以確認或取消導覽。 這可能是必要的，例如，當使用者嘗試在完全完成資料輸入頁面之前進行流覽時。 在此情況下，應用程式應該提供通知，讓使用者離開頁面，或在發生之前取消流覽作業。 您可以使用通知的回應來控制是否叫用導覽，以在 view 模型類別中達成此目的。

## <a name="summary"></a>總結

Xamarin.Forms 包含對頁面導覽的支援，通常是因為使用者與 UI 的互動，或來自應用程式本身，而導致內部邏輯導向狀態變更。 不過，在使用 MVVM 模式的應用程式中執行流覽可能很複雜。

本章提供的 `NavigationService` 類別，可用來執行視圖模型的第一次導覽。 將導覽邏輯放在 view 模型類別中，表示邏輯可透過自動化測試來執行。 此外，view 模型還可以執行邏輯來控制導覽，以確保強制執行某些商務規則。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)

---
title: 企業應用程式瀏覽
description: 本章說明 eShopOnContainers 行動裝置應用程式中檢視模型所執行的檢視模型優先 （contract-first） 瀏覽。
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9ac9f3200440001752c07ad45fdaaf2b1d9ba6a5
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243677"
---
# <a name="enterprise-app-navigation"></a>企業應用程式瀏覽

Xamarin.Forms 可支援的頁面巡覽，通常是從使用者的互動與 UI 或應用程式本身內部邏輯驅動的狀態變更的結果。 但是，瀏覽很複雜，所以無法使用模型-檢視-ViewModel (MVVM) 模式中，應用程式中實作，因為必須符合下列挑戰：

-   如何找出檢視來瀏覽至，使用緊密結合和檢視之間的相依性不會產生的方法。
-   如何協調處理序用來瀏覽至檢視具現化並初始化。 使用時 MVVM，檢視和檢視模型需要具現化，並透過檢視表的繫結內容與彼此相關聯。 當應用程式正在使用相依性插入容器時，具現化的檢視和檢視模型可能需要的特定建構機制。
-   是否要執行檢視第一個瀏覽，或檢視模型優先 （contract-first） 瀏覽。 使用檢視第一個瀏覽，瀏覽至該頁面提及了檢視類型的名稱。 在導覽中，指定的檢視具現化，以及其對應的檢視模型和其他相依的服務。 另一個方法是使用檢視模型優先 （contract-first） 巡覽，其中的頁面，即可瀏覽至指的是檢視的模型型別名稱。
-   如何以完全分隔 瀏覽應用程式的行為之間的檢視和檢視模型。 MVVM 模式可提供應用程式的 UI 與簡報和商務邏輯之間的分隔。 不過，應用程式的導覽行為將通常會跨越應用程式的 UI 和簡報組件。 使用者通常會起始從檢視中，瀏覽和檢視瀏覽的結果將會被取代。 不過，瀏覽可能通常也需要啟動或從協調檢視模型中。
-   如何進行初始化巡覽期間傳遞的參數。 例如，如果使用者巡覽至檢視，以更新訂單詳細資料，訂單資料必須傳遞到檢視，讓它可以顯示正確的資料。
-   如何共同縱座標巡覽，以確保特定的商務規則所規範。 例如，不用離開檢視，以便它們可以更正任何無效的資料，或提示您送出或捨棄檢視內所做的任何資料變更之前可能會提示使用者。

本章節來解決這些挑戰出示`NavigationService`用來執行檢視模型第一個頁面巡覽的類別。

> [!NOTE]
> `NavigationService`所使用的應用程式設計為只執行 ContentPage 執行個體之間的階層式導覽。 使用服務來瀏覽其他網頁型別之間可能會導致非預期的行為。

## <a name="navigating-between-pages"></a>頁面之間瀏覽

瀏覽邏輯可以位於檢視的程式碼後置，或在資料繫結的檢視模型。 將巡覽邏輯放在檢視中，可能是最簡單的方法，但並非透過單元測試輕鬆地測試。 將巡覽邏輯放在檢視模型類別，表示邏輯，可以運用透過單元測試。 此外，檢視模型然後實作邏輯，以確保特定的商務規則會強制執行控制項巡覽。 例如，應用程式可能不允許使用者離開頁面，但未先確保輸入的資料無效。

A`NavigationService`類別通常叫用從檢視模型，以升級的測試能力。 但是，巡覽至檢視中檢視模型需要參考檢視，並特別檢視作用中的檢視模型沒有關聯，不建議檢視模型。 因此，`NavigationService`顯示以下的檢視模型類型指定為要瀏覽至的目標。

EShopOnContainers 行動裝置應用程式會使用`NavigationService`類別，以提供檢視模型優先 （contract-first） 瀏覽。 這個類別會實作`INavigationService`介面，下列程式碼範例所示：

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

這個介面會指定實作的類別必須提供下列方法：

|方法|用途|
|--- |--- |
|`InitializeAsync`|應用程式啟動時，會執行兩個頁面的其中一個瀏覽。|
|`NavigateToAsync`|執行階層式導覽至指定的頁面。|
|`NavigateToAsync(parameter)`|執行至指定的頁面，將參數傳遞的階層式導覽。|
|`RemoveLastFromBackStackAsync`|從瀏覽堆疊移除前一個頁面。|
|`RemoveBackStackAsync`|在巡覽堆疊中移除所有先前的步驟。|

此外，`INavigationService`介面會指定實作的類別必須提供`PreviousPageViewModel`屬性。 這個屬性會傳回與前一個頁面巡覽堆疊中相關聯的檢視模型類型。

> [!NOTE]
> `INavigationService`介面通常會同時指定`GoBackAsync`方法，用來以程式設計的方式傳回至在巡覽堆疊中的上一頁。 不過，這個方法因遺漏的 eShopOnContainers 行動裝置應用程式，您不需要。

### <a name="creating-the-navigationservice-instance"></a>建立 NavigationService 執行個體

`NavigationService`類別，它會實作`INavigationService`介面並已登錄，為單一與 Autofac 相依性插入容器，如下列程式碼範例所示：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

`INavigationService`介面中已經解決`ViewModelBase`類別建構函式，如下列程式碼範例所示：

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

這會傳回參考`NavigationService`物件儲存在 Autofac 相依性插入容器，它由`InitNavigation`方法中的`App`類別。 如需詳細資訊，請參閱[巡覽時應用程式啟動](#navigating_when_the_app_is_launched)。

`ViewModelBase`類別存放區`NavigationService`執行個體中`NavigationService`型別的屬性`INavigationService`。 因此，所有檢視模型類別，衍生自`ViewModelBase`類別，可以使用`NavigationService`屬性來存取所指定的方法`INavigationService`介面。 這可避免插入的額外負荷`NavigationService`從 Autofac 相依性插入容器，為每個檢視模型類別的物件。

### <a name="handling-navigation-requests"></a>處理巡覽要求

Xamarin.Forms 提供[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)類別，實作中的使用者也可向前及向後，視需要執行頁面，瀏覽的階層式導覽體驗。 如需有關階層式導覽的詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

而不使用[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)類別直接 eShopOnContainers 應用程式包裝`NavigationPage`類別`CustomNavigationView`類別，如下列程式碼範例所示：

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

此文繞圖的目的是為了方便樣式[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)內類別的 XAML 檔案的執行個體。

檢視模型類別內叫用的其中一個，會執行瀏覽`NavigateToAsync`指定成，如下列程式碼範例所示瀏覽網頁的檢視模型類型的方法：

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

下列程式碼範例示範`NavigateToAsync`所提供的方法`NavigationService`類別：

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

每個方法可讓任何檢視模型類別衍生自`ViewModelBase`類別叫用執行階層式導覽`InternalNavigateToAsync`方法。 此外，第二個`NavigateToAsync`方法可讓瀏覽資料，以指定做為引數傳遞至正要巡覽，其中它通常用來執行初始設定的檢視模型。 如需詳細資訊，請參閱[期間瀏覽傳遞參數](#passing_parameters_during_navigation)。

`InternalNavigateToAsync`方法執行巡覽要求，並在下列程式碼範例所示：

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

`InternalNavigateToAsync`方法會為檢視模型的瀏覽執行第一個呼叫`CreatePage`方法。 這個方法會找出對應到指定的檢視模型型別，以及建立並傳回此檢視類型的執行個體的檢視。 尋找對應到檢視的模型型別 檢視會使用以慣例為基礎的方法，就假設：

-   檢視位於相同的組件的檢視模型類型。
-   檢視表位於。檢視子命名空間。
-   位於 檢視模型。ViewModels 子命名空間。
-   若要檢視模型名稱，使用 「 模型 」 中移除對應檢視名稱。

檢視具現化時，它具有其對應的檢視模型與相關聯。 如需有關如何發生這種情況的詳細資訊，請參閱[自動建立檢視模型的檢視模型定位器](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

如果正在建立的檢視是`LoginView`，它會包裝內的新執行個體`CustomNavigationView`類別，並指派給[ `Application.Current.MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/)屬性。 否則，`CustomNavigationView`執行個體擷取，並提供它不是 null， [ `PushAsync` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)方法被叫用的推播至瀏覽堆疊所建立的檢視。 不過，如果擷取`CustomNavigationView`執行個體是`null`，正在建立的檢視會包裝內的新執行個體`CustomNavigationView`類別，並指派給`Application.Current.MainPage`屬性。 這項機制可確保在巡覽期間，頁面會加入正確的巡覽堆疊，它是空的以及其包含的資料時。

> [!TIP]
> 請考慮快取頁面。 快取的頁面會導致目前沒有顯示的檢視表的記憶體耗用量。 不過，不含網頁快取它意思 XAML 剖析，而且頁面和其檢視模型的建構，會發生每次新的頁面瀏覽至時，這可能會影響效能的複雜的頁面。 設計良好的頁面上未使用的控制項數目過多，效能應該很足夠。 不過，如果遇到變慢的頁面載入時間，也許網頁快取。

建立檢視並瀏覽至之後,`InitializeAsync`執行檢視的相關聯的檢視模型的方法。 如需詳細資訊，請參閱[期間瀏覽傳遞參數](#passing_parameters_during_navigation)。

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>瀏覽時的應用程式啟動

啟動應用程式時，`InitNavigation`方法中的`App`叫用類別。 下列程式碼範例會示範這個方法：

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

此方法會建立新`NavigationService`Autofac 相依性插入容器中的物件，並傳回它的參考，再叫用其`InitializeAsync`方法。

> [!NOTE]
> 當`INavigationService`介面的解決方式`ViewModelBase`類別，容器將參考傳回給`NavigationService`InitNavigation 方法叫用時所建立的物件。

下列程式碼範例示範`NavigationService``InitializeAsync`方法：

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

`MainView`如果快取的存取語彙基元，用來驗證的應用程式瀏覽至。 否則，`LoginView`瀏覽至。

如需 Autofac 相依性插入容器的詳細資訊，請參閱[簡介相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>在巡覽期間傳遞參數

其中一個`NavigateToAsync`所指定的方法`INavigationService`介面，可讓瀏覽資料指定為引數傳遞至正要巡覽，其中它通常用來執行初始設定的檢視模型。

例如，`ProfileViewModel`類別包含`OrderDetailCommand`當使用者選取訂單上執行`ProfileView`頁面。 接著，這會執行`OrderDetailAsync`方法，下列程式碼範例所示：

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

這個方法會叫用巡覽至`OrderDetailViewModel`，並傳遞`Order`執行個體，表示使用者所選取的順序`ProfileView`頁面。 當`NavigationService`類別會建立`OrderDetailView`、`OrderDetailViewModel`類別具現化，且指派到檢視[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)。 之後瀏覽至`OrderDetailView`、`InternalNavigateToAsync`方法執行`InitializeAsync`檢視的方法中的相關聯的檢視模型。

`InitializeAsync`方法定義於`ViewModelBase`類別會覆寫的方法。 這個方法會指定`object`引數代表要傳遞給檢視模型 」 瀏覽作業期間的資料。 因此，想要從巡覽作業接收資料的檢視模型類別會提供自己的實作`InitializeAsync`方法，以執行必要的初始化。 下列程式碼範例示範`InitializeAsync`方法從`OrderDetailViewModel`類別：

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

這個方法會擷取`Order`傳入瀏覽作業期間，檢視模型，並使用它來擷取完整的順序的執行個體詳細資料從`OrderService`執行個體。

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>叫用的瀏覽使用行為

瀏覽通常是由使用者互動所觸發從檢視表。 例如，`LoginView`執行瀏覽下列驗證成功。 下列程式碼範例示範如何巡覽叫用由行為：

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

在執行階段，`EventToCommandBehavior`互動會回應[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)。 當`WebView`瀏覽至網頁， [ `Navigating` ](https://developer.xamarin.com/api/event/Xamarin.Forms.WebView.Navigating/)會引發事件，會同時執行`NavigateCommand`中`LoginViewModel`。 根據預設，此事件的事件引數會傳遞至命令。 這項資料會轉換來源和目標之間傳遞中指定轉換器`EventArgsConverter`屬性，它會傳回[ `Url` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebNavigationEventArgs.Url/)從[ `WebNavigatingEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebNavigatingEventArgs/)。 因此，當`NavigationCommand`會執行，網頁的 Url 做為參數傳遞到已註冊`Action`。

接著，`NavigationCommand`執行`NavigateAsync`方法，下列程式碼範例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

這個方法會叫用巡覽至`MainViewModel`，並遵循導覽中，移除`LoginView`巡覽堆疊中的頁面。

### <a name="confirming-or-cancelling-navigation"></a>確認或取消瀏覽

應用程式可能需要與使用者互動瀏覽作業期間，好讓使用者可以確認或取消巡覽。 這可能是必要的比方說，在使用者嘗試瀏覽之前完全完成資料的項目頁面。 在此情況下，應用程式應該提供可讓使用者巡覽離頁面上，或瀏覽作業之前它就會發生取消的通知。 這可藉由使用針對通知回應控制叫用巡覽達成中檢視模型類別。

## <a name="summary"></a>總結

Xamarin.Forms 包含頁面導覽，通常是從使用者互動具有 UI，或從應用程式本身，因為邏輯驅動的內部狀態的變更而支援。 但是，瀏覽很複雜，所以無法使用 MVVM 模式應用程式中實作。

本章呈現`NavigationService`類別，可用來在檢視模型檢視模型優先 （contract-first） 瀏覽。 將巡覽邏輯放在檢視模型類別，表示邏輯，可以運用到自動化測試。 此外，檢視模型然後實作邏輯，以確保特定的商務規則會強制執行控制項巡覽。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

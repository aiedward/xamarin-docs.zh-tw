---
title: 企業應用程式瀏覽
description: 本章說明在 eShopOnContainers 的行動應用程式從檢視模型所執行的檢視模型首次瀏覽。
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d306b0c1c0d08129671e27b96911ec771acb658e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994766"
---
# <a name="enterprise-app-navigation"></a>企業應用程式瀏覽

Xamarin.Forms 包含頁面巡覽，通常是從使用者的互動，使用 UI 或應用程式本身內部邏輯驅動的狀態變更的結果的支援。 不過，瀏覽可能會難以實作使用 Model View ViewModel (MVVM) 模式中，應用程式中，必須符合下列挑戰：

-   如何找出檢視來瀏覽至，使用不會引進緊密結合和檢視之間的相依性的方法。
-   如何協調處理序用來瀏覽至檢視具現化並初始化。 當使用 MVVM，檢視和檢視模型需要具現化，並透過檢視表的繫結內容與彼此相關聯。 當應用程式使用相依性插入容器時，檢視和檢視模型的具現化可能會需要特定的建構機制。
-   是否要執行檢視第一個瀏覽，或檢視模型首次瀏覽。 檢視第一個瀏覽，以瀏覽至頁面是指檢視類型的名稱。 在導覽中，指定的檢視具現化，以及其對應的檢視模型和其他相依服務。 另一個方法是使用檢視模型首次瀏覽，其中的頁面，即可瀏覽至指的是檢視的模型型別名稱。
-   如何，完全不同的應用程式的導覽行為之間的檢視和檢視模型。 MVVM 模式提供應用程式的 UI 和其展示層和商務邏輯之間的區隔。 不過，應用程式的瀏覽行為通常會跨越應用程式的 UI 和簡報組件。 使用者通常會起始從檢視中，瀏覽和檢視將會被取代，瀏覽的結果。 不過，瀏覽通常也需要啟動或從協調檢視模型中。
-   如何將參數傳遞期間進行初始化巡覽。 比方說，如果使用者瀏覽至更新訂單的詳細資料檢視時，將訂單資料必須傳遞至檢視，以便它可以顯示正確的資料。
-   如何共同合作的導覽，以確保會遵守 特定的商務規則。 比方說之前離開檢視，以便它們可以更正任何無效的資料，或提示您提交或捨棄檢視中所做的任何資料變更可能會提示使用者。

這一章所呈現來解決這些挑戰`NavigationService`用來執行檢視模型首次頁面巡覽的類別。

> [!NOTE]
> `NavigationService`所使用的應用程式的設計目的是執行 ContentPage 執行個體之間的階層式導覽。 使用服務來瀏覽其他網頁型別之間可能會導致非預期的行為。

## <a name="navigating-between-pages"></a>頁面之間巡覽

瀏覽邏輯可以位於檢視的程式碼後置，或在資料繫結檢視模型。 雖然將瀏覽邏輯放在檢視中，可能是最簡單的方法，但並非透過單元測試輕鬆地測試。 將瀏覽邏輯放在檢視模型類別，表示透過單元測試，可以運用的邏輯。 此外，檢視模型然後可實作邏輯，以控制瀏覽，以確保特定的商務規則會強制執行。 例如，應用程式可能不會允許使用者導覽離開頁面，且不需先確保有效輸入的資料。

A`NavigationService`通常會從檢視模型，以提升可測試性叫用類別。 不過，瀏覽至檢視，從檢視模型會需要參考檢視，並特別與，但不建議這麼做沒作用中的檢視模型的檢視表的檢視模型。 因此，`NavigationService`呈現以下的檢視模型類型指定為要瀏覽至的目標。

EShopOnContainers 的行動裝置應用程式會使用`NavigationService`類別，以提供檢視模型首次瀏覽。 這個類別會實作`INavigationService`介面，下列程式碼範例所示：

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
|`InitializeAsync`|應用程式啟動時，請執行瀏覽至其中的兩個頁面。|
|`NavigateToAsync`|執行階層式導覽至指定的頁面。|
|`NavigateToAsync(parameter)`|執行至指定的頁面，並將參數傳遞的階層式導覽。|
|`RemoveLastFromBackStackAsync`|從導覽堆疊移除前一個頁面。|
|`RemoveBackStackAsync`|從導覽堆疊中移除所有先前的頁面。|

颾魤 ㄛ`INavigationService`介面會指定實作的類別必須提供`PreviousPageViewModel`屬性。 這個屬性會傳回與前一個頁面巡覽堆疊中相關聯的檢視模型類型。

> [!NOTE]
> `INavigationService`介面通常會同時指定`GoBackAsync`方法，用來以程式設計的方式傳回至導覽堆疊中的上一頁。 不過，這個方法是遺漏 eShopOnContainers 的行動裝置應用程式，因為它不是必要。

### <a name="creating-the-navigationservice-instance"></a>建立的 NavigationService 執行個體

`NavigationService`類別，它會實作`INavigationService`介面中，會註冊為使用 Autofac 相依性插入容器中，單一值中，如下列程式碼範例所示：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

`INavigationService`介面會解析`ViewModelBase`類別建構函式，如下列程式碼範例所示：

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

這會傳回參考`NavigationService`物件，會儲存在 Autofac 相依性插入容器，它由`InitNavigation`方法中的`App`類別。 如需詳細資訊，請參閱 <<c0> [ 瀏覽時應用程式啟動](#navigating_when_the_app_is_launched)。

`ViewModelBase`類別存放區`NavigationService`執行個體中`NavigationService`型別的屬性`INavigationService`。 因此，所有檢視模型類別，衍生自`ViewModelBase`類別中，可以使用`NavigationService`屬性來存取所指定的方法`INavigationService`介面。 這可避免插入的額外負荷`NavigationService`從 Autofac 相依性插入容器，每個檢視模型類別的物件。

### <a name="handling-navigation-requests"></a>處理巡覽要求

Xamarin.Forms 提供[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)類別，實作中的使用者可向前及向後，視需要執行頁面，瀏覽的階層式導覽體驗。 如需有關階層式導覽的詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

而不是使用[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)類別直接，eShopOnContainers 應用程式包裝`NavigationPage`類別`CustomNavigationView`類別，如下列程式碼範例所示：

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

這個包裝的目的是為了方便樣式[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)內類別的 XAML 檔案的執行個體。

瀏覽叫用的其中一個時，會在檢視模型類別`NavigateToAsync`指定巡覽到，如下列程式碼範例所示頁面的檢視模型類型的方法：

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

下列程式碼範例所示`NavigateToAsync`所提供的方法`NavigationService`類別：

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

每個方法可讓任何檢視模型類別衍生自`ViewModelBase`類別來叫用執行階層式導覽`InternalNavigateToAsync`方法。 此外，第二個`NavigateToAsync`方法可讓瀏覽資料指定為引數傳遞至檢視模型所巡覽，其中它通常用來執行初始設定。 如需詳細資訊，請參閱 <<c0> [ 期間瀏覽並傳遞參數](#passing_parameters_during_navigation)。

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

`InternalNavigateToAsync`方法執行第一個呼叫至檢視模型的瀏覽`CreatePage`方法。 這個方法會找出對應至指定的檢視模型類型，以及建立並傳回此檢視類型的執行個體的檢視。 找出對應至檢視模型類型的檢視，會使用以慣例為基礎的方法，其中假設：

-   檢視表位於相同的組件的檢視模型類型。
-   檢視表位於。檢視子命名空間。
-   檢視模型位於。Viewmodel 子命名空間。
-   檢視名稱對應至檢視模型名稱，使用 「 模型 」 中移除。

檢視具現化時，它會使用其對應的檢視模型產生關聯。 如需有關如何發生這種情況的詳細資訊，請參閱 <<c0> [ 自動建立檢視模型的檢視模型定位器](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

正在建立的檢視是否`LoginView`，它會包裝內的新執行個體`CustomNavigationView`類別，並指派給[ `Application.Current.MainPage` ](xref:Xamarin.Forms.Application.MainPage)屬性。 否則，請`CustomNavigationView`執行個體中擷取，並提供，不是 null， [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage)方法被叫用的推播到導覽堆疊上所建立的檢視。 不過，如果擷取`CustomNavigationView`執行個體`null`，所建立的檢視內的新執行個體包裝`CustomNavigationView`類別，並指派給`Application.Current.MainPage`屬性。 這項機制可確保在巡覽期間，頁面會正確地加入至導覽堆疊是空的以及其包含的資料時。

> [!TIP]
> 考慮快取頁面。 頁面快取會導致記憶體耗用量，目前不會顯示的檢視。 不過，不含頁面快取其意思 XAML 剖析和頁面和它的檢視模型的建構，會發生每次新的頁面瀏覽至時，可以有複雜的頁面對效能造成影響。 設計良好的頁面未使用的控制項數目過多，效能應該就已足夠。 不過，如果發生緩慢頁面載入時間，有助於頁面快取。

建立檢視並巡覽之後,`InitializeAsync`執行檢視的相關聯的檢視模型的方法。 如需詳細資訊，請參閱 <<c0> [ 期間瀏覽並傳遞參數](#passing_parameters_during_navigation)。

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>瀏覽時應用程式啟動

啟動應用程式時，`InitNavigation`方法中的`App`叫用類別。 下列程式碼範例示範此方法：

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

此方法會建立新`NavigationService`Autofac 相依性插入容器中的物件，並傳回它的參考，然後再叫用其`InitializeAsync`方法。

> [!NOTE]
> 當`INavigationService`介面的解決方式`ViewModelBase`類別，容器會傳回參考`NavigationService`InitNavigation 方法會叫用時所建立的物件。

下列程式碼範例所示`NavigationService``InitializeAsync`方法：

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

`MainView`瀏覽至應用程式是否用於驗證的快取的存取語彙基元。 否則，`LoginView`巡覽。

如需有關 Autofac 相依性插入容器的詳細資訊，請參閱[相依性插入簡介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>在巡覽期間傳遞參數

其中一個`NavigateToAsync`所指定的方法`INavigationService`介面，可讓瀏覽資料，以指定為引數傳遞至檢視模型所巡覽，其中它通常用來執行初始設定。

例如，`ProfileViewModel`類別包含`OrderDetailCommand`當使用者選取訂單上執行的`ProfileView`頁面。 接著，這會執行`OrderDetailAsync`方法，以下列程式碼範例所示：

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

這個方法會叫用的瀏覽至`OrderDetailViewModel`，並傳遞`Order`執行個體，表示使用者選取的順序`ProfileView`頁面。 當`NavigationService`類別會建立`OrderDetailView`，則`OrderDetailViewModel`類別會具現化，並指派給檢視[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。 瀏覽至後`OrderDetailView`，則`InternalNavigateToAsync`方法執行`InitializeAsync`檢視的方法中的相關聯的檢視模型。

`InitializeAsync`方法定義在`ViewModelBase`類別可以覆寫的方法。 這個方法會指定`object`引數，表示要在瀏覽作業期間傳遞至檢視模型的資料。 因此，想要巡覽作業中接收資料的檢視模型類別會提供其自己實作的`InitializeAsync`方法，以執行必要的初始化。 下列程式碼範例所示`InitializeAsync`方法從`OrderDetailViewModel`類別：

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

這個方法會擷取`Order`瀏覽作業期間，傳遞至檢視模型，並使用它來擷取完整的順序的執行個體的詳細說明從`OrderService`執行個體。

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>您可以使用行為的叫用瀏覽

使用者互動，瀏覽通常就會觸發從檢視表。 比方說，`LoginView`執行瀏覽下列驗證成功。 下列程式碼範例示範如何叫用巡覽行為：

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

在執行階段`EventToCommandBehavior`會回應互動[ `WebView` ](xref:Xamarin.Forms.WebView)。 當`WebView`瀏覽至網頁[ `Navigating` ](xref:Xamarin.Forms.WebView.Navigating)就會引發事件，會同時執行`NavigateCommand`中`LoginViewModel`。 根據預設，事件的事件引數會傳遞至命令。 這項資料會轉換來源和目標之間傳遞中指定轉換器所`EventArgsConverter`屬性，會傳回[ `Url` ](xref:Xamarin.Forms.WebNavigationEventArgs.Url)從[ `WebNavigatingEventArgs` ](xref:Xamarin.Forms.WebNavigatingEventArgs)。 因此，當`NavigationCommand`會執行，網頁的 Url 做為參數傳遞至已註冊`Action`。

依次`NavigationCommand`執行`NavigateAsync`方法，以下列程式碼範例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

這個方法會叫用的瀏覽至`MainViewModel`，並遵循導覽中，移除`LoginView`從導覽堆疊上的頁面。

### <a name="confirming-or-cancelling-navigation"></a>確認或取消瀏覽

應用程式可能需要進行互動使用者瀏覽作業時，好讓使用者可以確認或取消巡覽。 這可能有必要，比方說，當使用者嘗試瀏覽之前完全完成資料輸入頁面。 在此情況下，應用程式應該提供可讓使用者導覽離開該頁面，或取消瀏覽作業之前發生的通知。 這可藉由使用針對通知回應控制叫用巡覽達成中檢視模型類別。

## <a name="summary"></a>總結

Xamarin.Forms 包含頁面巡覽，通常會從使用者的互動的 UI，或從應用程式本身，因為邏輯驅動的內部狀態的變更而造成的支援。 不過，瀏覽可以在使用 MVVM 模式的應用程式中實作複雜。

顯示這一章`NavigationService`類別，用來執行從檢視模型的檢視模型首次瀏覽。 將瀏覽邏輯放在檢視模型類別，表示邏輯，可以透過自動化的測試中運用。 此外，檢視模型然後可實作邏輯，以控制瀏覽，以確保特定的商務規則會強制執行。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

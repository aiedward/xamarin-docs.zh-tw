---
title: 對企業應用程式進行單元測試
description: 本章說明如何在 eShopOnContainers 行動應用程式中執行單元測試。
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dbc863c6f0de49c809de9d13dc9c682b43e2befe
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373948"
---
# <a name="unit-testing-enterprise-apps"></a>對企業應用程式進行單元測試

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

行動裝置應用程式有獨特的問題，那就是桌上型電腦和網頁應用程式都不需要擔心。 行動使用者會因其使用的裝置、網路連線能力、服務可用性和其他各種因素而有所不同。 因此，行動應用程式應該經過測試，因為它們會在真實世界中用來改善其品質、可靠性和效能。 有許多類型的測試都應該在應用程式上執行，包括單元測試、整合測試和使用者介面測試，且單元測試是最常見的測試形式。

單元測試會採用應用程式的小單位，通常是方法、將它與程式碼的其餘部分隔離，並驗證它是否如預期般運作。 其目標是要檢查每個功能單位是否如預期般執行，使錯誤不會在整個應用程式中傳播。 在發生錯誤的情況下，偵測 bug 發生的錯誤會更有效率，因為它會在次要失敗點間接觀察錯誤的影響。

當單元測試是軟體發展工作流程不可或缺的一部分時，對程式碼品質會有最大的影響。 一旦撰寫方法之後，就應該撰寫單元測試來驗證方法的行為，以回應輸入資料的標準、界限和不正確的案例，並檢查程式碼所做的任何明確或隱含假設。 或者，在測試導向開發中，單元測試會在程式碼之前撰寫。 在此案例中，單元測試會作為設計檔和功能規格。

> [!NOTE]
> 單元測試對回歸非常有效，也就是用來運作但發生錯誤更新的功能。

單元測試通常會使用排列-act 判斷提示模式：

- 單元測試方法的 [ *排列* ] 區段會初始化物件，並設定傳遞至受測方法的資料值。
- *Act* 區段會使用必要的引數叫用受測方法。
- [判斷提示 *] 區段會* 驗證受測方法的動作是否如預期般運作。

遵循此模式可確保單元測試是可讀取且一致的。

## <a name="dependency-injection-and-unit-testing"></a>相依性插入和單元測試

採用鬆散結合架構的動機之一，就是它有助於單元測試。 以 Autofac 註冊的其中一個類型是 `OrderService` 類別。 下列程式碼範例顯示此類別的大綱：

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

`OrderDetailViewModel`類別相依于容器在具現 `IOrderService` 化物件時所解析的型別 `OrderDetailViewModel` 。 不過，您 `OrderService` 可以 `OrderDetailViewModel` 改為使用模擬來取代物件，以進行類別的單元測試，而不是建立物件來進行 `OrderService` 測試。 圖10-1 說明此關聯性。

![執行 IOrderService 介面的類別](unit-testing-images/unittesting.png)

**圖10-1：** 執行 IOrderService 介面的類別

這種方法可讓 `OrderService` 物件 `OrderDetailViewModel` 在執行時間傳遞至類別中，而在可測試性方面，則是在 `OrderMockService` 測試時將類別傳遞至 `OrderDetailViewModel` 類別。 這種方法的主要優點是，它可讓您執行單元測試，而不需要很難處理的資源，例如 web 服務或資料庫。

## <a name="testing-mvvm-applications"></a>測試 MVVM 應用程式

從 MVVM 應用程式測試模型和查看模型與測試任何其他類別相同，而且可以使用相同的工具和技術，例如單元測試和模擬。 不過，有一些模式通常是模型和視圖模型類別，可受益于特定的單元測試技術。

> [!TIP]
> 在每個單元測試中測試一件事。 不要讓單元測試練習在單元的行為中有一個以上的層面。 這麼做會導致難以讀取和更新的測試。 這也可能會在解讀失敗時造成混淆。

EShopOnContainers 行動應用程式會使用 [xUnit](https://xunit.github.io/) 來執行單元測試，以支援兩種不同類型的單元測試：

- 事實是一律為 true 的測試，可測試非變異的狀況。
- 理論是僅適用于一組特定資料的測試。

EShopOnContainers 行動應用程式隨附的單元測試是事實測試，因此每個單元測試方法都會以 `[Fact]` 屬性裝飾。

> [!NOTE]
> 測試執行器會執行 xUnit 測試。 若要執行測試執行器，請執行所需平臺的 eShopOnContainers. TestRunner 專案。

### <a name="testing-asynchronous-functionality"></a>測試非同步功能

在實施 MVVM 模式時，view 模型通常會在服務上叫用作業，通常是非同步。 測試叫用這些作業的程式碼通常會使用模擬做為實際服務的取代。 下列程式碼範例將示範如何藉由將模擬服務傳遞到視圖模型，來測試非同步功能：

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

此單元測試會在叫 `Order` 用方法之後，檢查實例的屬性是否 `OrderDetailViewModel` 具有值 `InitializeAsync` 。 將 `InitializeAsync` 視圖模型的對應視圖流覽至時，會叫用方法。 如需導覽的詳細資訊，請參閱 [導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。

`OrderDetailViewModel`建立實例時，它預期 `OrderService` 會將實例指定為引數。 但是，會 `OrderService` 從 web 服務抓取資料。 因此，會將 `OrderMockService` 實例（也就是類別的模擬版本 `OrderService` ）指定為此函式的引數 `OrderDetailViewModel` 。 然後，當叫用視圖模型的 `InitializeAsync` 方法（叫 `IOrderService` 用作業）時，就會抓取模擬資料，而不是與 web 服務進行通訊。

### <a name="testing-inotifypropertychanged-implementations"></a>測試 INotifyPropertyChanged 的實施

執行 `INotifyPropertyChanged` 介面可讓視圖回應源自于視圖模型和模型的變更。 這些變更不限於控制項中顯示的資料，這些變更也用來控制視圖，例如會導致啟動動畫的視圖模型狀態，或是要停用的控制項。

可以透過將事件處理常式附加至 `PropertyChanged` 事件，並檢查是否在設定屬性的新值之後引發事件，來測試可直接由單元測試更新的屬性。 下列程式碼範例顯示這類測試：

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

此單元測試會叫 `InitializeAsync` `OrderViewModel` 用類別的方法， `Order` 以更新其屬性。 單元測試會通過，前提 `PropertyChanged` 是會引發屬性的事件 `Order` 。

### <a name="testing-message-based-communication"></a>測試以訊息為基礎的通訊

使用 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別在鬆散結合的類別之間進行通訊的視圖模型，可以藉由訂閱正在測試的程式碼所傳送的訊息來進行單元測試，如下列程式碼範例所示：

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

此單元測試會檢查是否 `CatalogViewModel` 發佈 `AddProduct` 訊息，以回應其 `AddCatalogItemCommand` 正在執行。 因為 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別支援多播訊息訂閱，所以單元測試可以訂閱 `AddProduct` 訊息，並執行回呼委派以回應接收訊息。 這個回呼委派（指定為 lambda 運算式）會設定一個 `boolean` 欄位，供 `Assert` 語句用來驗證測試的行為。

### <a name="testing-exception-handling"></a>測試例外狀況處理

您也可以撰寫單元測試，檢查是否針對不正確動作或輸入擲回特定的例外狀況，如下列程式碼範例所示：

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

此單元測試將擲回例外狀況，因為 [`ListView`](xref:Xamarin.Forms.ListView) 控制項沒有名為的事件 `OnItemTapped` 。 `Assert.Throws<T>`方法是泛型方法，其中 `T` 是預期例外狀況的型別。 傳遞給方法的引數 `Assert.Throws<T>` 是將擲回例外狀況的 lambda 運算式。 因此，單元測試會通過，並提供 lambda 運算式擲回 `ArgumentException` 。

> [!TIP]
> 避免撰寫檢查例外狀況訊息字串的單元測試。 例外狀況訊息字串可能會隨著時間而改變，因此依賴其目前狀態的單元測試會視為脆弱。

### <a name="testing-validation"></a>測試驗證

測試驗證的執行有兩個層面：測試是否已正確地執行任何驗證規則，以及測試類別是否 `ValidatableObject<T>` 如預期般執行。

驗證邏輯通常很容易測試，因為它通常是一個獨立的進程，其中的輸出取決於輸入。 在 `Validate` 每個具有至少一個相關聯驗證規則的屬性上，應該會有針對叫用方法的結果進行測試，如下列程式碼範例所示：

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

此單元測試會在實例中的兩個 `ValidatableObject<T>` 屬性都有資料時，檢查驗證是否成功 `MockViewModel` 。

除了檢查驗證是否成功，驗證單元測試也應該檢查 `Value` `IsValid` `Errors` 每個實例的、和屬性值 `ValidatableObject<T>` ，以確認類別如預期般執行。 下列程式碼範例將示範執行這項操作的單元測試：

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

當的 `Surname` 屬性沒有 `MockViewModel` 任何資料，且 `Value` `IsValid` `Errors` 每個實例的、和屬性 `ValidatableObject<T>` 都已正確設定時，此單元測試會檢查驗證是否失敗。

## <a name="summary"></a>總結

單元測試會採用應用程式的小單位，通常是方法、將它與程式碼的其餘部分隔離，並驗證它是否如預期般運作。 其目標是要檢查每個功能單位是否如預期般執行，使錯誤不會在整個應用程式中傳播。

您可以藉由將相依物件取代為模擬相依物件行為的 mock 物件，來隔離受測物件的行為。 這可讓單元測試執行，而不需要像是 web 服務或資料庫等不實用的資源。

從 MVVM 應用程式測試模型和查看模型與測試任何其他類別相同，而且可以使用相同的工具和技術。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)

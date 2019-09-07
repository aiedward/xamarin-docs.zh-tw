---
title: 對企業應用程式進行單元測試
description: 本章節說明如何在 eShopOnContainers 行動應用程式中執行單元測試。
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0fb63c650e73bce5a08b204f942f0c19583e4899
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770681"
---
# <a name="unit-testing-enterprise-apps"></a>對企業應用程式進行單元測試

行動應用程式有獨特的問題，而桌上型電腦和 web 應用程式都不需要擔心。 行動使用者會因其使用的裝置、網路連線能力、服務的可用性和各種其他因素而有所不同。 因此，行動裝置應用程式應該經過測試，因為它們會在現實世界中用來改善其品質、可靠性和效能。 應用程式必須執行許多類型的測試，包括單元測試、整合測試和使用者介面測試，而單元測試是最常見的測試形式。

單元測試會採用應用程式的小型單位，通常是方法，將它與其余的程式碼隔離，並驗證它是否如預期般運作。 其目標是檢查每個功能單位是否如預期般執行，讓錯誤不會在整個應用程式中傳播。 偵測發生錯誤的 bug 更有效率，因為它會在次要失敗點間接觀察錯誤的效果。

當單元測試是軟體發展工作流程不可或缺的一部分時，對程式碼品質的影響最大。 一旦撰寫方法，就應該撰寫單元測試，以驗證方法的行為，以回應輸入資料的標準、界限和不正確案例，並檢查程式碼所做的任何明確或隱含假設。 或者，透過測試導向開發，單元測試會在程式碼之前寫入。 在此案例中，單元測試會同時做為設計檔和功能規格。

> [!NOTE]
> 單元測試對回歸非常有效，也就是用來運作但受到錯誤更新干擾的功能。

單元測試通常會使用 [排列-act-assert] 模式：

- 單元測試方法的 [*排列*] 區段會初始化物件，並設定傳遞給受測方法之資料的值。
- *Act*區段會使用必要的引數叫用受測的方法。
- [判斷提示 *] 區段會*驗證受測方法的動作是否如預期般運作。

遵循此模式可確保單元測試是可讀取和一致的。

## <a name="dependency-injection-and-unit-testing"></a>相依性插入和單元測試

採用鬆散結合架構的動機之一，就是它可協助單元測試。 向 Autofac 註冊的其中一個類型是`OrderService`類別。 下列程式碼範例顯示此類別的大綱：

```csharp
public class OrderDetailViewModel : ViewModelBase  
{  
    private IOrderService _ordersService;  

    public OrderDetailViewModel(IOrderService ordersService)  
    {  
        _ordersService = ordersService;  
    }  
    ...  
}
```

類別相依于容器在具現`IOrderService`化`OrderDetailViewModel`物件時所解析的類型。 `OrderDetailViewModel` 不過，不是建立`OrderService`物件來對`OrderDetailViewModel`類別進行單元測試`OrderService` ，而是將物件取代為測試用途的 mock。 圖10-1 說明此關聯性。

![](unit-testing-images/unittesting.png "執行 IOrderService 介面的類別")

**圖10-1：** 執行 IOrderService 介面的類別

這種方法可`OrderService`讓物件在執行時間傳遞`OrderDetailViewModel`至類別，而且在可測試性的興趣下，它允許`OrderMockService`將類別傳遞至`OrderDetailViewModel`測試階段的類別。 這種方法的主要優點是，它可讓您執行單元測試，而不需要難以處理的資源，例如 web 服務或資料庫。

## <a name="testing-mvvm-applications"></a>測試 MVVM 應用程式

從 MVVM 應用程式測試模型和視圖模型，與測試任何其他類別相同，而且可以使用相同的工具和技術，例如單元測試和模擬。 不過，有些模式通常是模型和視圖模型類別，可受益于特定的單元測試技術。

> [!TIP]
> 在每個單元測試中測試一件事。 請不要讓單元測試練習在單元的行為中有一個以上的層面。 這麼做會導致測試不容易閱讀和更新。 這也可能會在解讀失敗時造成混淆。

EShopOnContainers 行動應用程式會使用[xUnit](https://xunit.github.io/)來執行單元測試，以支援兩種不同類型的單元測試：

- 事實是永遠為 true 的測試，這會測試不變的條件。
- 理論是僅適用于一組特定資料的測試。

EShopOnContainers 行動應用程式包含的單元測試是事實測試，因此每個單元測試方法都會以`[Fact]`屬性裝飾。

> [!NOTE]
> 測試執行器會執行 xUnit 測試。 若要執行測試執行器，請執行所需平臺的 eShopOnContainers TestRunner 專案。

### <a name="testing-asynchronous-functionality"></a>測試非同步功能

在執行 MVVM 模式時，view 模型通常會以非同步方式叫用服務上的作業。 針對叫用這些作業的程式碼進行測試時，通常會使用模擬做為實際服務的替代專案。 下列程式碼範例將示範如何藉由將 mock 服務傳遞至視圖模型來測試非同步功能：

```csharp
[Fact]  
public async Task OrderPropertyIsNotNullAfterViewModelInitializationTest()  
{  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.NotNull(orderViewModel.Order);  
}
```

這個單元測試會檢查`Order` `OrderDetailViewModel`實例的屬性在叫用`InitializeAsync`方法之後是否具有值。 當視圖模型的對應視圖流覽至時，會叫用方法。`InitializeAsync` 如需有關導覽的詳細資訊，請參閱[導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。

建立實例時，它會`OrderService`預期實例會指定為引數。 `OrderDetailViewModel` 不過， `OrderService`會從 web 服務抓取資料。 因此， `OrderMockService`實例（也就是`OrderService`類別的模擬版本）會指定`OrderDetailViewModel`為函式的引數。 然後，當叫用視圖模型`InitializeAsync`的方法（ `IOrderService`叫用作業）時，就會抓取模擬資料，而不是與 web 服務進行通訊。

### <a name="testing-inotifypropertychanged-implementations"></a>測試 INotifyPropertyChanged 的部署

`INotifyPropertyChanged`執行介面可讓視圖回應源自視圖模型和模型的變更。 這些變更不限於控制項中顯示的資料，它們也可用來控制視圖，例如導致動畫啟動的視圖模型狀態或停用控制項。

可以透過將事件處理常式附加至`PropertyChanged`事件，並檢查是否在設定屬性的新值之後引發事件，來測試可直接由單元測試更新的屬性。 下列程式碼範例顯示這類測試：

```csharp
[Fact]  
public async Task SettingOrderPropertyShouldRaisePropertyChanged()  
{  
    bool invoked = false;  
    var orderService = new OrderMockService();  
    var orderViewModel = new OrderDetailViewModel(orderService);  

    orderViewModel.PropertyChanged += (sender, e) =>  
    {  
        if (e.PropertyName.Equals("Order"))  
            invoked = true;  
    };  
    var order = await orderService.GetOrderAsync(1, GlobalSetting.Instance.AuthToken);  
    await orderViewModel.InitializeAsync(order);  

    Assert.True(invoked);  
}
```

這個單元測試`InitializeAsync`會叫用`OrderViewModel`類別的方法，這會導致`Order`更新其屬性。 單元測試將會通過，但前提`PropertyChanged`是會`Order`針對屬性引發事件。

### <a name="testing-message-based-communication"></a>測試以訊息為基礎的通訊

使用[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)類別在鬆散耦合類別之間進行通訊的視圖模型，可以藉由訂閱受測程式碼所傳送的訊息來進行單元測試，如下列程式碼範例所示：

```csharp
[Fact]  
public void AddCatalogItemCommandSendsAddProductMessageTest()  
{  
    bool messageReceived = false;  
    var catalogService = new CatalogMockService();  
    var catalogViewModel = new CatalogViewModel(catalogService);  

    Xamarin.Forms.MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
        this, MessageKeys.AddProduct, (sender, arg) =>  
    {  
        messageReceived = true;  
    });  
    catalogViewModel.AddCatalogItemCommand.Execute(null);  

    Assert.True(messageReceived);  
}
```

此單元測試會`CatalogViewModel`檢查是否`AddProduct`發行訊息以回應其`AddCatalogItemCommand`正在執行的。 因為類別支援多點傳播訊息訂閱，所以單元測試可以訂閱`AddProduct`訊息，並執行回呼委派以回應接收它。 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 這個回呼委派（指定為 lambda 運算式）會設定`boolean` `Assert`語句用來驗證測試行為的欄位。

### <a name="testing-exception-handling"></a>測試例外狀況處理

您也可以撰寫單元測試，以檢查是否針對不正確動作或輸入擲回特定的例外狀況，如下列程式碼範例所示：

```csharp
[Fact]  
public void InvalidEventNameShouldThrowArgumentExceptionText()  
{  
    var behavior = new MockEventToCommandBehavior  
    {  
        EventName = "OnItemTapped"  
    };  
    var listView = new ListView();  

    Assert.Throws<ArgumentException>(() => listView.Behaviors.Add(behavior));  
}
```

這個單元測試將會擲回例外狀況， [`ListView`](xref:Xamarin.Forms.ListView)因為控制項沒有名為`OnItemTapped`的事件。 方法是泛型方法，其中`T`是預期的例外狀況類型。 `Assert.Throws<T>` 傳遞至`Assert.Throws<T>`方法的引數是將擲回例外狀況的 lambda 運算式。 因此，如果 lambda 運算式`ArgumentException`擲回，則單元測試將會通過。

> [!TIP]
> 避免撰寫可檢查例外狀況訊息字串的單元測試。 例外狀況訊息字串可能會隨著時間而改變，因此依賴其目前狀態的單元測試會被視為脆弱。

### <a name="testing-validation"></a>測試驗證

測試驗證的執行有兩個層面：測試是否已正確實作為驗證規則，以及測試`ValidatableObject<T>`類別是否如預期般執行。

驗證邏輯通常很容易測試，因為它通常是一個獨立的進程，其中的輸出取決於輸入。 在至少有一個相關聯的驗證規則的`Validate`每個屬性上叫用方法的結果應該會進行測試，如下列程式碼範例所示：

```csharp
[Fact]  
public void CheckValidationPassesWhenBothPropertiesHaveDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  
    mockViewModel.Surname.Value = "Smith";  

    bool isValid = mockViewModel.Validate();  

    Assert.True(isValid);  
}
```

當`ValidatableObject<T>` 實例`MockViewModel`中的兩個屬性都有資料時，此單元測試會檢查驗證是否成功。

除了檢查驗證是否成功，驗證單元測試也應檢查`Value`每個`ValidatableObject<T>`實例的、 `IsValid`和`Errors`屬性值，以確認類別是否如預期般執行。 下列程式碼範例示範執行此動作的單元測試：

```csharp
[Fact]  
public void CheckValidationFailsWhenOnlyForenameHasDataTest()  
{  
    var mockViewModel = new MockViewModel();  
    mockViewModel.Forename.Value = "John";  

    bool isValid = mockViewModel.Validate();  

    Assert.False(isValid);  
    Assert.NotNull(mockViewModel.Forename.Value);  
    Assert.Null(mockViewModel.Surname.Value);  
    Assert.True(mockViewModel.Forename.IsValid);  
    Assert.False(mockViewModel.Surname.IsValid);  
    Assert.Empty(mockViewModel.Forename.Errors);  
    Assert.NotEmpty(mockViewModel.Surname.Errors);  
}
```

當的`Surname`屬性`IsValid` `Value` `Errors` `ValidatableObject<T>`沒有任何資料，而且每個實例的、和屬性都已正確設定時，此單元測試會檢查驗證是否失敗。 `MockViewModel`

## <a name="summary"></a>總結

單元測試會採用應用程式的小型單位，通常是方法，將它與其余的程式碼隔離，並驗證它是否如預期般運作。 其目標是檢查每個功能單位是否如預期般執行，讓錯誤不會在整個應用程式中傳播。

將相依物件取代為模擬相依物件行為的 mock 物件，即可隔離受測物件的行為。 這可讓您執行單元測試，而不需要很難以利用的資源，例如 web 服務或資料庫。

從 MVVM 應用程式測試模型和視圖模型，與測試任何其他類別相同，而且可以使用相同的工具和技術。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)

---
title: "單元測試"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0842ce24139da5d963e2c528b440e6592d5910f8
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="unit-testing"></a>單元測試

行動裝置應用程式有唯一的問題，桌面和 web 應用程式不需要擔心。 行動使用者將不同的裝置在使用，透過網路連線所服務的可用性以及其他因素的範圍。 因此，行動裝置應用程式應該經過測試，因為它們會使用真實世界中以改善其品質、 可靠性和效能。 有許多類型的測試應該執行的應用程式，包括單元測試、 整合測試，以及使用者介面測試、 單元測試所測試的最常見的表單上。

單元測試會採用應用程式時，通常是一種方法小單位、 隔離的程式碼中，其餘部分從並驗證它如預期般運作。 其目的是功能的要檢查的每個單位如預期般執行，以便錯誤不會傳播整個應用程式。 偵測它發生所在的 bug 會更有效率觀察間接在次要失敗點的效果。

軟體開發工作流程中不可或缺的一部分時，單元測試會有影響最大程式碼品質。 已寫入一個方法，如應該的驗證方法，以回應標準、 界限和不正確的輸入資料的情況下，該檢查的行為的程式碼所做的任何明確或隱含假設撰寫單元測試。 或者，以測試為導向的開發，撰寫單元測試程式碼之前。 此案例中，單元測試做為設計文件和功能規格。

> [!NOTE]
> 單元測試是很有成效迴歸 – 也就是用來搭配使用，但有問題的更新已干擾的功能。

單元測試通常會使用排列 act assert 模式：

-   *排列*單元測試方法的區段會初始化物件，並設定傳遞至受測方法的資料值。
-   *做*區段會叫用含有所需的引數的受測方法。
-   *Assert*區段會驗證受測方法的動作會在如預期般運作。

遵循此模式可確保單元測試是可讀取且一致。

## <a name="dependency-injection-and-unit-testing"></a>相依性插入和單元測試

其中一個採用鬆散偶合的架構動機是它能單元測試。 其中一個類型向 Autofac 是`OrderService`類別。 下列程式碼範例會顯示這個類別的大綱：

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

`OrderDetailViewModel`類別具有相依性`IOrderService`輸入容器解決當它執行個體化`OrderDetailViewModel`物件。 不過，而不要建立`OrderService`單元測試的物件`OrderDetailViewModel`類別，而是取代`OrderService`基於測試目的模擬物件。 圖 10 1 說明這個關聯性。

![](unit-testing-images/unittesting.png "類別可實作 IOrderService 介面")

**圖 10-1:**實作 IOrderService 介面的類別

這個方法可讓`OrderService`物件傳遞至`OrderDetailViewModel`類別在執行階段，以及的測試能力，它可讓`OrderMockService`類別，以傳遞至`OrderDetailViewModel`次測試的類別。 這種方法的主要優點是它可讓單元測試，而不需要龐大的資源，例如 web 服務或資料庫執行。

## <a name="testing-mvvm-applications"></a>測試 MVVM 應用程式

測試模型和檢視模型 MVVM 應用程式從相同測試的其他任何類別，並使用相同的工具和技術 – 例如單元測試和模擬。 不過，有一些一般模型的模式和檢視模型類別，可從特定的單元測試技術獲益。

> [!TIP]
> 測試與每個單元測試的一件事。 不會想要讓單元測試練習單位的行為的多個層面的。 這樣會導致難以讀取和更新的測試。 它也會導致混淆解譯失敗時。

EShopOnContainers 行動裝置應用程式會使用[xUnit](https://xunit.github.io/)執行單元測試，可支援兩種不同的單元測試：

-   事實是永遠為 true，測試的測試條件而異。
-   理論是只適用於特定的資料集的測試。

隨附 eShopOnContainers 行動裝置應用程式的單元測試是事實測試，因此每個單元測試方法都有`[Fact]`屬性。

> [!NOTE]
> xUnit 測試是由測試執行器執行。 若要執行測試執行器，執行必要的平台的 eShopOnContainers.TestRunner 專案。

### <a name="testing-asynchronous-functionality"></a>測試非同步功能

實作 MVVM 模式時，檢視模型通常上叫用作業的服務，通常以非同步的方式。 通常叫用這些作業的程式碼測試的實際服務取代為使用模擬。 下列程式碼範例示範如何將模擬服務傳遞至檢視模型測試非同步的功能：

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

此單元測試確認`Order`屬性`OrderDetailViewModel`執行個體的值之後將會`InitializeAsync`叫用方法。 `InitializeAsync`檢視模型的對應檢視巡覽時，會叫用方法。 如需瀏覽的詳細資訊，請參閱[瀏覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。

當`OrderDetailViewModel`建立執行個體時，它會預期`OrderService`指定做為引數的執行個體。 不過，`OrderService`從 web 服務擷取資料。 因此，`OrderMockService`執行個體，也就是模擬版本的`OrderService`類別中，指定的引數為`OrderDetailViewModel`建構函式。 然後，當檢視模型`InitializeAsync`叫用方法時，叫用`IOrderService`操作，模擬資料是擷取，而不是與 web 服務通訊。

### <a name="testing-inotifypropertychanged-implementations"></a>測試實作 INotifyPropertyChanged

實作`INotifyPropertyChanged`介面允許對檢視所源自的變更做出回應的檢視模型和模型。 這些變更不限於在控制項中顯示資料，它們也可以用來控制，請在檢視表，例如會導致啟動的動畫或停用控制項的檢視模型狀態。

進行測試的單元測試可以直接更新的屬性，請附加至事件處理常式`PropertyChanged`事件，並檢查是否設定屬性的新值後引發事件。 下列程式碼範例示範這類測試：

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

會叫用此單元測試`InitializeAsync`方法`OrderViewModel`類別，這會導致其`Order`更新屬性。 單元測試會成功，但前提是`PropertyChanged`都會引發事件`Order`屬性。

### <a name="testing-message-based-communication"></a>測試訊息為基礎的通訊

檢視模型使用[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別通訊鬆散偶合的類別之間可以進行單元測試訂閱，將受測程式碼所傳送的訊息，如下列程式碼範例所示：

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

此單元測試確認`CatalogViewModel`發行`AddProduct`訊息以回應其`AddCatalogItemCommand`正在執行。 因為[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別支援多點傳送的訊息訂閱、 單元測試可以訂閱`AddProduct`訊息並接收其回應中執行的回呼委派。 此回呼委派，做為 lambda 運算式，指定設定`boolean`欄位，以供`Assert`陳述式以驗證測試的行為。

### <a name="testing-exception-handling"></a>測試例外狀況處理

單元測試也可以寫入該檢查特定的例外狀況擲回無效的動作，或輸入，如下列程式碼範例所示：

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

此單元測試將會擲回例外狀況，因為[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)控制項沒有名稱為事件`OnItemTapped`。 `Assert.Throws<T>`方法是泛型方法其中`T`是預期的例外狀況的型別。 引數傳遞給`Assert.Throws<T>`方法則會擲回例外狀況是 lambda 運算式。 因此，單元測試會成功，提供 lambda 運算式就會擲回`ArgumentException`。

>💡 **提示**： 避免撰寫單元測試，檢查例外狀況訊息字串。 例外狀況訊息字串可能會隨著時間變更，並因此依賴它們的存在的單元測試會視為維護。

### <a name="testing-validation"></a>測試驗證

有兩個層面需要測試驗證實作： 測試，正確實作的任何驗證規則，以及測試，`ValidatableObject<T>`如預期般執行類別。

驗證邏輯，若要測試，通常是簡單，因為它通常是獨立的處理程序的輸出會根據輸入。 叫用的結果應該會有測試`Validate`方法的每一個屬性具有至少一個相關聯的驗證規則，如下列程式碼範例所示：

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

此單元測試會檢查驗證成功時兩個`ValidatableObject<T>`中的屬性`MockViewModel`這兩個的執行個體都有資料。

檢查驗證成功，以及驗證單元測試也應該檢查的值`Value`， `IsValid`，和`Errors`每個屬性`ValidatableObject<T>`執行個體，以確認此類別會在如預期般執行。 下列程式碼範例將示範此單元測試：

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

此單元測試會檢查驗證失敗時`Surname`屬性`MockViewModel`沒有任何資料，而`Value`， `IsValid`，和`Errors`每個屬性`ValidatableObject<T>`均已正確設定執行個體。

## <a name="summary"></a>總結

單元測試會採用應用程式時，通常是一種方法小單位、 隔離的程式碼中，其餘部分從並驗證它如預期般運作。 其目的是功能的要檢查的每個單位如預期般執行，以便錯誤不會傳播整個應用程式。

測試物件的行為可以隔離模擬相依性物件的行為的模擬物件使用取代相依的物件。 這可讓單元測試，而不需要龐大的資源，例如 web 服務或資料庫執行。

測試模型和檢視模型 MVVM 應用程式從相同測試的其他任何類別，並使用相同的工具和技術。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

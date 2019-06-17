---
title: 單元測試企業應用程式
description: 本章將說明如何在 eShopOnContainers 的行動裝置應用程式執行單元測試。
ms.prod: xamarin
ms.assetid: 4af82e52-f99b-4cad-b278-1745f190c240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 02aeedd5498c47950e2fbc0d218de05bc0bb3204
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298970"
---
# <a name="unit-testing-enterprise-apps"></a>單元測試企業應用程式

行動裝置應用程式會有唯一的問題，而桌面和 web 應用程式不需要擔心。 行動使用者會由裝置它們所使用的網路連線服務的可用性和範圍的其他因素而有所不同。 因此，行動裝置應用程式應該經過測試，因為它們會使用真實世界中來改善其品質、 可靠性和效能。 有許多類型的測試應該執行的應用程式，包括單元測試、 整合測試，以及使用者介面測試、 單元測試所測試的最常見的表單上。

單元測試會採用應用程式時，通常是一種方法小單位、 將它與程式碼的其餘部分隔離，並確認其行為如預期般運作。 其目標是功能的要檢查的每個單位如預期般執行，使錯誤不會傳播整個應用程式。 偵測的錯誤發生的位置會更有效率，觀察的 bug，以間接在次要失敗點影響。

軟體開發工作流程中不可或缺的一部分時，單元測試會有程式碼品質的影響最大。 已寫入的方法，因為應該的驗證方法，以回應標準、 界限和不正確的情況下，輸入資料，以及該檢查的行為的程式碼所做的任何明確或隱含假設撰寫單元測試。 或者，使用測試導向開發，撰寫單元測試程式碼之前。 此案例中，單元測試做為設計文件和功能規格。

> [!NOTE]
> 單元測試是針對迴歸 – 也就是用來搭配使用，但具有已更新錯誤的干擾的功能非常有效。

單元測試通常會使用排列 act assert 模式：

-   *排列*的單元測試方法的區段會初始化物件，並設定傳遞至受測方法的資料值。
-   *處理*區段會叫用具有必要的引數的受測方法。
-   *判斷提示*區段會驗證受測方法的動作會在如預期般運作。

遵循此模式可確保單元測試的可讀取且一致。

## <a name="dependency-injection-and-unit-testing"></a>相依性插入和單元測試

採用鬆散偶合的架構的動機之一是它能進行單元測試。 其中一個使用 Autofac 註冊類型是`OrderService`類別。 下列程式碼範例會示範這個類別的大綱：

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

`OrderDetailViewModel`類別具有相依性`IOrderService`類型的容器可以解決當具現化`OrderDetailViewModel`物件。 不過，而不是建立`OrderService`單元測試的物件`OrderDetailViewModel`類別，相反地，取代`OrderService`基於測試目的 mock 物件。 圖 10-1 說明這個關聯性。

![](unit-testing-images/unittesting.png "實作 IOrderService 介面的類別")

**圖 10-1:** 實作 IOrderService 介面的類別

此方法可讓`OrderService`傳遞至物件`OrderDetailViewModel`類別在執行階段，以及在感興趣的可測試性，它可讓`OrderMockService`類別，以傳遞至`OrderDetailViewModel`在測試階段的類別。 這種方法的主要優點是它可讓單元測試執行，但是不需要龐大的資源，例如 web 服務或資料庫。

## <a name="testing-mvvm-applications"></a>測試 MVVM 應用程式

測試模型和檢視模型從 MVVM 應用程式等同於測試任何其他類別，而且相同的工具和技術 – 例如單元測試與模擬 (mock)，可用。 不過，有一些模式的一般模型和檢視模型類別，可從特定的單元測試技術獲益。

> [!TIP]
> 測試與每個單元測試的一件事。 不會想要讓單元測試練習的單位行為的多個層面的。 這樣會導致難以讀取和更新的測試。 它也可能導致混淆解譯失敗時。

EShopOnContainers 的行動裝置應用程式會使用[xUnit](https://xunit.github.io/)執行單元測試，可支援兩種不同的單元測試：

-   事實是永遠都不成立，測試的測試條件而異。
-   理論是僅適用於特定的資料集的測試。

隨附在 eShopOnContainers 的行動裝置應用程式的單元測試是事實的測試，以及每個單元測試方法因此裝飾了`[Fact]`屬性。

> [!NOTE]
> xUnit 測試是由測試執行器執行。 若要執行的測試執行器，執行必要的平台的 eShopOnContainers.TestRunner 專案。

### <a name="testing-asynchronous-functionality"></a>測試非同步功能

實作 MVVM 模式時，檢視模型通常是叫用作業的服務，通常以非同步的方式。 通常將這些作業會叫用的程式碼測試的實際服務取代為使用模擬 （mock）。 下列程式碼範例會示範測試將模擬 （mock） 的服務傳遞至檢視模型的非同步功能：

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

此單元測試會檢查`Order`的屬性`OrderDetailViewModel`執行個體都有值之後`InitializeAsync`叫用方法。 `InitializeAsync`檢視模型的對應檢視巡覽時，會叫用方法。 如需有關瀏覽的詳細資訊，請參閱[瀏覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。

當`OrderDetailViewModel`會建立執行個體，它會預期`OrderService`做為引數指定的執行個體。 不過，`OrderService`從 web 服務擷取資料。 因此，`OrderMockService`執行個體，也就是模擬 （mock） 版本的`OrderService`類別中，指定的引數為`OrderDetailViewModel`建構函式。 然後，當檢視模型`InitializeAsync`方法會叫用，這會叫用`IOrderService`作業，模擬 （mock） 的資料是擷取，而不是與 web 服務通訊。

### <a name="testing-inotifypropertychanged-implementations"></a>測試實作 INotifyPropertyChanged

實作`INotifyPropertyChanged`介面可讓檢視，以回應變更源自於檢視模型和模型。 這些變更不一定要在控制項中顯示的資料，它們也可以用來控制檢視中，例如造成動畫要啟動或停用控制項的檢視模型狀態。

進行測試的單元測試可以直接更新的屬性，請附加至事件處理常式`PropertyChanged`事件，並檢查是否設定屬性的新值之後引發的事件。 下列程式碼範例會顯示這類測試：

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

叫用此單元測試`InitializeAsync`方法`OrderViewModel`類別，會造成其`Order`是要更新屬性。 單元測試會成功，但前提`PropertyChanged`引發事件`Order`屬性。

### <a name="testing-message-based-communication"></a>測試訊息式通訊

檢視模型，使用[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)通訊鬆散偶合的類別之間可進行單元測試訂閱正在測試的程式碼所傳送的訊息，如下列程式碼範例所示的類別：

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

此單元測試會檢查`CatalogViewModel`發佈`AddProduct`訊息，以回應其`AddCatalogItemCommand`正在執行。 因為[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)類別支援多點傳送的訊息的訂用帳戶、 單元測試可以訂閱`AddProduct`訊息，並執行以接收該回應的回呼委派。 此回呼委派，指定 lambda 運算式，為設定`boolean`欄位，可由`Assert`陳述式來確認測試的行為。

### <a name="testing-exception-handling"></a>測試例外狀況處理

單元測試也可以寫入該檢查的特定例外狀況擲回無效的動作，或輸入，如下列程式碼範例所示：

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

此單元測試會擲回的例外狀況，因為[ `ListView` ](xref:Xamarin.Forms.ListView)控制項並沒有名為事件`OnItemTapped`。 `Assert.Throws<T>`方法是泛型方法其中`T`是預期的例外狀況的型別。 將引數傳遞`Assert.Throws<T>`方法是將會擲回例外狀況的 lambda 運算式。 因此，單元測試將會通過前提是 lambda 運算式則會擲回`ArgumentException`。

>💡 **提示**:避免撰寫單元測試，檢查例外狀況訊息字串。 例外狀況訊息字串可能會隨著時間變更，並因此依賴它們的存在的單元測試會視為不完善。

### <a name="testing-validation"></a>測試驗證

有兩個層面來測試驗證的實作： 測試，正確地實作任何驗證規則，以及測試，`ValidatableObject<T>`類別會執行，如預期般運作。

驗證邏輯是若要測試，通常是簡單的因為它通常是獨立的處理程序輸出會根據輸入。 叫用的結果應該會有測試`Validate`方法的每一個屬性有至少一個相關聯的驗證規則，如下列程式碼範例所示：

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

此單元測試會驗證成功時，檢查兩個`ValidatableObject<T>`中的屬性`MockViewModel`這兩個的執行個體都有資料。

檢查驗證成功，以及驗證單元測試也應該檢查的值`Value`， `IsValid`，並`Errors`每個屬性`ValidatableObject<T>`執行個體，以確認此類別會在如預期般執行。 下列程式碼範例示範此單元測試：

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

此單元測試會驗證失敗時，檢查`Surname`屬性`MockViewModel`沒有任何資料，而`Value`， `IsValid`，和`Errors`每個屬性`ValidatableObject<T>`執行個體已正確設定。

## <a name="summary"></a>總結

單元測試會採用應用程式時，通常是一種方法小單位、 將它與程式碼的其餘部分隔離，並確認其行為如預期般運作。 其目標是功能的要檢查的每個單位如預期般執行，使錯誤不會傳播整個應用程式。

取代模擬相依性物件的行為的模擬 （mock） 物件的相依物件，可以隔離受測物件的行為。 這可讓單元測試執行，但是不需要龐大的資源，例如 web 服務或資料庫。

測試模型和檢視模型從 MVVM 應用程式等同於測試任何其他類別，並使用相同的工具和技術。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

---
title: "相依性插入"
ms.topic: article
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 538bb3f67a0612a93b8c3eb7f9de557ad6f321e3
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="dependency-injection"></a>相依性插入

一般而言，具現化物件時，會叫用類別建構函式和物件需要的任何值會當做引數傳遞給建構函式。 這是相依性插入的範例，具體而言就所謂*建構函式插入*。 需要物件的相依性會插入建構函式。

藉由指定做為介面類型的相依性，相依性插入 」 可讓分開處理相依於這些類型的程式碼的具象型別。 它通常會使用保留一份註冊的介面和抽象的型別之間的對應的容器以及實作或擴充這些類型的具象型別。

另外還有其他類型的相依性插入，例如*屬性 setter 插入*，和*方法呼叫插入*，但較不常看到它們。 因此，本章節將著重僅在執行相依性插入容器使用的建構函式插入。

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>相依性插入的簡介

相依性插入是一種特殊的逆轉控制 (IoC) 模式，其中要反轉的考量是取得必要的相依性的程序。 與相依性插入另一個類別負責插入成在執行階段物件的相依性。 下列程式碼範例示範如何`ProfileViewModel`使用相依性插入時，結構化類別：

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

`ProfileViewModel`建構函式收到`IOrderService`做為引數，插入由其他類別的執行個體。 只有相依性，在`ProfileViewModel`類別是介面類型上。 因此，`ProfileViewModel`類別沒有負責具現化類別的任何知識`IOrderService`物件。 負責具現化類別`IOrderService`物件，並將它插入`ProfileViewModel`類別，又稱為*相依性插入容器*。

相依性插入容器會降低藉由提供具現化類別執行個體和管理其存留期的容器設定為基礎的功能物件之間的連接。 物件在建立期間，容器會將任何物件需要的相依性插入至它。 如果這些相依性具有尚未建立，容器就會建立，並先解決它們的相依性。

> [!NOTE]
> 相依性插入也可以實作以手動方式使用處理站。 不過，使用容器會提供額外的功能，例如生命週期管理和註冊透過掃描的組件。

有數個使用相依性插入容器的優點：

-   容器中移除不需要類別來找出其相依性和管理其存留期。
-   容器可讓實作的相依性的對應，而不會影響該類別。
-   容器允許模仿的相依性，從而完成的測試能力。
-   容器會藉由使用新的類別來輕鬆地新增至應用程式增加可維護性。

在使用 MVVM Xamarin.Forms 應用程式內容中，登錄及解析檢視模型，以及註冊服務，並將它們插入檢視模型通常會使用相依性插入容器。

與 eShopOnContainers 行動應用程式使用 Autofac 管理檢視模型的具現化和服務應用程式中的類別，提供了許多相依性插入容器。 Autofac 有助於建置鬆散偶合的應用程式，並提供所有相依性插入容器，包括註冊型別對應和物件執行個體，方法中經常發現的功能解析物件、 管理物件的存留期，以及插入建構函式的物件，它會解析成的相依物件。 如需 Autofac 的詳細資訊，請參閱[Autofac](http://autofac.readthedocs.io/en/latest/index.html) readthedocs.io 上。

在 Autofac，`IContainer`介面提供相依性插入容器。 圖 3-1 顯示的相依性，使用這個容器，具現化時`IOrderService`物件，並將它插入`ProfileViewModel`類別。

![](dependency-injection-images/dependencyinjection.png "當使用相依性插入的相依性範例")

**圖 3-1:**時使用相依性插入的相依性

在執行階段，容器必須知道哪一個實作`IOrderService`介面，它應該具現化，它可以具現化之前`ProfileViewModel`物件。 這項作業包括：

-   決定如何實作的物件具現化的容器`IOrderService`介面。 這稱為*註冊*。
-   實作的物件具現化的容器`IOrderService`介面，而`ProfileViewModel`物件。 這稱為*解析*。

最後，應用程式將會完成使用`ProfileViewModel`物件並將可供記憶體回收。 此時，記憶體回收行程應該處置`IOrderService`執行個體如果其他的類別不會共用相同的執行個體。

> [!TIP]
> 寫入容器無從驗證程式碼。 永遠嘗試寫入分離應用程式所使用的特定相依性容器的容器無從驗證程式碼。

## <a name="registration"></a>註冊

相依性可以插入物件之前，與容器必須先註冊相依性的類型。 註冊類型通常會牽涉到傳遞容器，介面和實作介面的具象型別。

有兩種方式註冊透過程式碼的容器中的類型和物件的：

-   向容器的型別或對應。 必要時，容器會建置指定型別的執行個體。
-   註冊容器中的現有物件，為單一。 必要時，容器就會傳回現有物件的參考。

> [!TIP]
> 相依性插入容器並不一定適合。 相依性插入導入額外的複雜度和可能無法適當很有幫助小型應用程式的需求。 如果類別沒有任何相依性，或不是適用於其他類型的相依性，它可能不會進行將其置於容器的意義。 此外，如果類別具有單一整數類資料類型的相依性設定，且永遠不會變更，它可能不會進行將其置於容器的意義。

需要相依性插入的型別註冊應該執行單一方法，應用程式中，而且應該叫用此方法，以確保應用程式知道它的類別之間的相依性的應用程式的生命週期中及早。 EShopOnContainers 行動應用程式中這由執行`ViewModelLocator`類別，哪些組建`IContainer`物件，並保留該物件的參考的應用程式中是唯一。 下列程式碼範例示範如何 eShopOnContainers 行動裝置應用程式宣告`IContainer`物件存放至`ViewModelLocator`類別：

```csharp
private static IContainer _container;
```

類型和執行個體中註冊`RegisterDependencies`方法中的`ViewModelLocator`類別。 這藉由先建立`ContainerBuilder`執行個體，下列程式碼範例所示：

```csharp
var builder = new ContainerBuilder();
```

類型和執行個體然後向註冊`ContainerBuilder`物件，並在下列程式碼範例示範最常見的型別註冊形式：

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

`RegisterType`如下所示的方法會將介面型別對應至具象型別。 它會告訴要具現化的容器`RequestProvider`物件時，它會具現化的物件需要的資料隱碼`IRequestProvider`透過建構函式。

具象型別也未從介面類型，對應的直接註冊，如下列程式碼範例所示：

```csharp
builder.RegisterType<ProfileViewModel>();
```

當`ProfileViewModel`類型解析，容器會插入必要的相依性。

Autofac 也可讓執行個體登錄容器所在負責維護的參考類型的單一執行個體。 例如，下列程式碼範例顯示如何 eShopOnContainers 行動裝置應用程式註冊時使用的具象類型`ProfileViewModel`執行個體都需要`IOrderService`執行個體：

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

`RegisterType`如下所示的方法會將介面型別對應至具象型別。 `SingleInstance`方法會設定註冊，讓每個相依的物件會接收相同的共用執行個體。 因此，只有一個`OrderService`執行個體將會在容器中，由需要的資料隱碼的物件共用存在`IOrderService`透過建構函式。

您也可以與執行執行個體註冊`RegisterInstance`方法，下列程式碼範例所示：

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

`RegisterInstance`如下所示的方法建立新`OrderMockService`執行個體，並在容器上登錄它。 因此，只有一個`OrderMockService`在容器中，由需要的資料隱碼的物件共用的執行個體存在`IOrderService`透過建構函式。

下列類型和執行個體註冊`IContainer`物件必須先建置，這在下列程式碼範例示範：

```csharp
_container = builder.Build();
```

叫用`Build`方法`ContainerBuilder`執行個體會建立新的相依性插入容器，其中包含所做的註冊。

>💡 **提示**： 請考慮`IContainer`視為不變。 雖然 Autofac 提供`Update`方法，以更新登錄中現有的容器，呼叫這個方法應該盡量。 會修改容器之後所建置它，特別是如果容器已使用的風險。 如需詳細資訊，請參閱[為不可變的容器，請考慮](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable)readthedocs.io 上。

<a name="resolution" />

## <a name="resolution"></a>解決方式

註冊類型之後，它可以解決，或是做為相依性。 當正在解析類型，且容器需要建立的新執行個體時，它會將任何相依項目插入至執行個體中。

一般而言，解析型別時，其中三種情況發生：

1.  如果尚未註冊型別，容器就會擲回例外狀況。
1.  如果已經註冊為單一的類型，容器就會傳回單一執行個體。 如果這是第一次呼叫的型別時，容器就會建立必要時，，和維護的參考。
1.  如果型別尚未註冊為單一，容器傳回的新執行個體，並不會維護它的參考。

下列程式碼範例示範如何`RequestProvider`Autofac 先前註冊的類型是可解析：

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

在此範例中，若要解決的具象類型要求 Autofac`IRequestProvider`型別，以及任何相依性。 一般而言，`Resolve`需要特定類型的執行個體時，呼叫方法。 如需控制解析物件的存留期資訊，請參閱[管理存留期的已解決物件](#managing_the_lifetime_of_resolved_objects)。

下列程式碼範例顯示如何 eShopOnContainers 行動裝置應用程式會具現化檢視的模型類型和其相依性：

```csharp
var viewModel = _container.Resolve(viewModelType);
```

在此範例中，Autofac 要求解析要求的檢視模型的檢視模型型別，容器也會解決任何相依性。 當解析`ProfileViewModel`類型，若要解決的相依性是`IOrderService`物件。 因此，Autofac 先建構`OrderService`物件，並再將它傳遞至建構函式`ProfileViewModel`類別。 模型 eShopOnContainers 行動裝置應用程式如何建構檢視的詳細資訊，與將它們與檢視表建立關聯，請參閱[自動建立檢視模型的檢視模型定位器](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

> [!NOTE]
> 登錄及解析與容器的型別具有效能的容器使用反映來建立每個類型，因為成本，尤其是相依性會為每個應用程式中的頁面巡覽重新建構。 如果有許多或深入的相依性，建立的成本可以大幅增加。

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>管理已解決物件的存留期

在註冊之後的類型，Autofac 的預設行為是建立的新執行個體的已註冊型別每次的類型已解決或相依性機制時將執行個體插入其他類別。 在此案例中，容器不保留已解決物件的參考。 不過，當註冊執行個體，Autofac 的預設行為是為了管理為單一物件的存留期。 因此，執行個體保持在範圍內中是在容器的範圍，會處置時超出範圍的容器和已記憶體回收，或當程式碼明確處置容器。

Autofac 執行個體的範圍可以用來指定單一行為 Autofac 從已註冊型別所建立的物件。 Autofac 執行個體範圍管理容器來具現化物件存留期。 預設執行個體範圍`RegisterType`方法`InstancePerDependency`範圍。 不過，`SingleInstance`範圍可以搭配`RegisterType`方法，使容器建立或傳回類型的單一執行個體時呼叫`Resolve`方法。 下列程式碼範例示範如何使用 Autofac 指示建立的單一執行個體`NavigationService`類別：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

第一次`INavigationService`介面，請解決，容器會建立新`NavigationService`物件並維護它的參考。 上的任何後續的解決方案`INavigationService`介面，容器將參考傳回給`NavigationService`先前建立的物件。

> [!NOTE]
> SingleInstance 範圍在處置容器時處置已建立的物件。

Autofac 包含額外的執行個體的範圍。 如需詳細資訊，請參閱[執行個體範圍](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html)readthedocs.io 上。

## <a name="summary"></a>總結

相依性插入 」 可讓從程式碼相依於這些類型的具象型別分開處理。 通常會使用保留一份註冊的介面和抽象的型別之間的對應的容器以及實作或擴充這些類型的具象型別。

Autofac 有助於建置鬆散偶合的應用程式，並提供所有相依性插入容器，包括註冊型別對應和物件執行個體，方法中經常發現的功能解析物件、 管理物件的存留期，以及插入建構函式的物件，它會解析成的相依物件。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

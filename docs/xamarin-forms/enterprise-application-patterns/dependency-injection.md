---
title: 相依性插入
description: 本章說明 eShopOnContainers 的行動裝置應用程式如何使用相依性插入來減少從程式碼相依於這些類型的具象類型。
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: fb225349b9ffb1c950486a817897b3c26c6ffbe4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300249"
---
# <a name="dependency-injection"></a>相依性插入

一般而言，具現化物件時，會叫用的類別建構函式和物件所需的任何值做為引數傳遞至建構函式。 這是相依性插入的範例，具體而言就所謂*建構函式插入*。 物件所需的相依性會插入建構函式。

藉由指定做為介面類型的相依性，相依性插入會啟用具象類型，取決於這些類型的程式碼分開處理。 此外，它通常會使用保存的註冊與介面和抽象的型別之間的對應清單的容器和實作或擴充這些類型的具象型別。

另外還有其他類型的相依性插入，這類*屬性 setter 資料隱碼攻擊*，並*方法呼叫插入*，但較不常看到它們。 因此，這一章將著重於執行具有相依性插入容器的建構函式插入。

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>相依性插入簡介

相依性插入是一種特殊的控制反轉 (IoC) 模式，其中要反轉的考量是取得必要的相依性的程序。 使用相依性插入另一個類別負責在執行階段物件中插入相依性。 下列程式碼範例示範如何`ProfileViewModel`類別的結構時使用相依性插入：

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

`ProfileViewModel`建構函式會接收`IOrderService`做為引數，插入由其他類別的執行個體。 中的唯一相依性`ProfileViewModel`類別是介面類型。 因此，`ProfileViewModel`類別沒有任何類別的知識，負責具現化`IOrderService`物件。 類別負責具現化`IOrderService`物件，並將它插入`ProfileViewModel`類別中，就所謂*相依性插入容器*。

相依性插入容器會降低藉由提供具現化類別執行個體，並管理其存留期，根據容器的組態功能的物件之間的結合程度。 在物件建立時，容器會將任何物件需要的相依性插入至它。 如果這些相依性有尚未建立，容器會建立，並先解析其相依性。

> [!NOTE]
> 相依性插入也可以手動使用 factory 來實作。 不過，使用容器提供額外的功能，例如存留期管理，以及透過掃描組件的註冊。

有數個使用相依性插入容器的優點：

-   容器中移除類別能夠找出其相依性和管理其存留期的需求。
-   容器允許實作的相依性對應，而不會影響該類別。
-   容器允許模擬的相依性，從而完成可測試性。
-   容器會增加可維護性，藉由使用新的類別，輕鬆地新增至應用程式。

在內容中使用 MVVM 的 Xamarin.Forms 應用程式，來註冊及解析檢視模型，以及註冊服務和插入檢視模型通常會使用相依性插入容器。

有許多相依性插入容器，eShopOnContainers 行動應用程式使用 Autofac 管理檢視模型的具現化和服務應用程式中的類別。 Autofac 有助於建立鬆散偶合的應用程式，並提供所有相依性插入容器，包括註冊型別對應和物件執行個體的方法中經常發現的功能解析物件、 管理物件存留期，以及插入建構函式的物件，它會解析成的相依物件。 如需 Autofac 的詳細資訊，請參閱[Autofac](http://autofac.readthedocs.io/en/latest/index.html) readthedocs.io 上。

中，`IContainer`介面提供相依性插入容器。 圖 3-1 顯示的相依性，使用此容器，會具現化時`IOrderService`物件，並將它插入`ProfileViewModel`類別。

![](dependency-injection-images/dependencyinjection.png "當使用相依性插入的相依性範例")

**圖 3-1:** 當使用相依性插入的相依性

在執行階段，容器必須知道哪一個實作`IOrderService`介面，它應該具現化，它可以具現化之前`ProfileViewModel`物件。 這牽涉到：

-   決定如何實作的物件具現化的容器`IOrderService`介面。 這就所謂*註冊*。
-   實作的物件具現化的容器`IOrderService`介面，而`ProfileViewModel`物件。 這就所謂*解析度*。

最後，應用程式將會完成使用`ProfileViewModel`物件，而且它的可用記憶體回收。 此時，記憶體回收行程應該處置`IOrderService`執行個體，如果其他類別不會共用相同的執行個體。

> [!TIP]
> 寫入容器無關的程式碼。 永遠嘗試寫入容器無關的程式碼分離應用程式所使用的特定相依性容器。

## <a name="registration"></a>註冊

相依性可以插入物件之前，相依性的類型必須先註冊與容器。 註冊類型通常會牽涉到傳遞容器，介面和實作介面的具象類型。

有兩種方式註冊透過程式碼容器中的型別和物件：

-   向容器註冊類型或對應。 必要時，容器會建置指定型別的執行個體。
-   註冊容器中的現有物件，為單一值。 必要時，容器就會傳回現有物件的參考。

> [!TIP]
> 相依性插入容器並不一定適合。 相依性插入會引進額外的複雜性和可能無法適當或最實用小型的應用程式的需求。 如果類別沒有任何相依性，或不是其他類型的相依性，它可能毫無意義，將它放在容器中。 此外，如果類別具有單一設定相依性類型不可或缺的且永遠不會變更，它可能沒有任何要將它放在容器中的意義。

需要相依性插入類型的註冊應該執行中應用程式的單一方法，應叫用這個方法，以確保應用程式會留意其類別之間的相依性的應用程式的生命週期中及早。 在 eShopOnContainers 的行動裝置應用程式這由執行`ViewModelLocator`類別，哪些組建`IContainer`物件，並會保留該物件的參考應用程式中的唯一類別。 下列程式碼範例示範 eShopOnContainers 的行動裝置應用程式的宣告方式`IContainer`物件中`ViewModelLocator`類別：

```csharp
private static IContainer _container;
```

類型和執行個體中註冊`RegisterDependencies`方法中的`ViewModelLocator`類別。 做法是先建立`ContainerBuilder`執行個體，下列程式碼範例所示：

```csharp
var builder = new ContainerBuilder();
```

類型和執行個體再向註冊`ContainerBuilder`物件，並在下列程式碼範例示範最常見的型別註冊的形式：

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

`RegisterType`如下所示的方法會將介面類型對應到具象型別。 它會告訴要具現化的容器`RequestProvider`物件時需要的資料隱碼的物件具現化`IRequestProvider`透過建構函式。

具象型別也未對應，以從介面類型，直接註冊，如下列程式碼範例所示：

```csharp
builder.RegisterType<ProfileViewModel>();
```

當`ProfileViewModel`類型解析，容器就會插入必要的相依性。

Autofac 也可讓執行個體註冊其中容器是負責維護的參考類型的單一執行個體。 例如，下列程式碼範例顯示如何 eShopOnContainers 的行動裝置應用程式註冊時要使用的具象類型`ProfileViewModel`執行個體都需要`IOrderService`執行個體：

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

`RegisterType`如下所示的方法會將介面類型對應到具象型別。 `SingleInstance`方法則會設定註冊，讓每個相依的物件會接收相同的共用執行個體。 因此，只有一個`OrderService`執行個體出現在容器中，由需要插入的物件共用`IOrderService`透過建構函式。

您也可以與執行執行個體註冊`RegisterInstance`方法，以下列程式碼範例所示：

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

`RegisterInstance`如下所示的方法建立新`OrderMockService`執行個體，並向容器。 因此，只有一個`OrderMockService`執行個體存在於容器中，由需要插入的物件共用`IOrderService`透過建構函式。

下列類型和執行個體註冊`IContainer`物件必須先建置，這下列程式碼範例所示：

```csharp
_container = builder.Build();
```

叫用`Build`方法`ContainerBuilder`執行個體會建立新的相依性插入容器，其中包含已註冊。

>💡 **提示**:請考慮`IContainer`視為不變。 雖然 Autofac 提供`Update`方法來更新現有的容器，呼叫這個方法中的註冊應該盡可能避免。 會修改容器之後它所建置，, 特別是如果容器已使用的風險。 如需詳細資訊，請參閱 <<c0> [ 不可變的容器，請考慮](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable)readthedocs.io 上。

<a name="resolution" />

## <a name="resolution"></a>解決方式

型別登錄之後，它可以解決或插入作為相依性。 正在解析型別，而且容器需要時建立新的執行個體，它會將任何相依性插入至執行個體中。

通常，解析為型別時，三個事項之一發生：

1.  如果尚未註冊類型，容器就會擲回例外狀況。
1.  如果類型具有尚未註冊為單一性，容器就會傳回的單一執行個體。 如果這是呼叫類型的第一次，容器會視需要加以建立，並維護它的參考。
1.  如果尚未註冊類型為單一性，容器會傳回新的執行個體，並不會維護它的參考。

下列程式碼範例示範如何`RequestProvider`先前已向 Autofac 的型別可以是已解決：

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

在此範例中，若要解決的具象類型要求 Autofac`IRequestProvider`型別，以及任何相依性。 一般而言，`Resolve`需要特定類型的執行個體時，會呼叫方法。 如需控制已解決物件的存留期資訊，請參閱 <<c0> [ 管理存留期的已解決物件](#managing_the_lifetime_of_resolved_objects)。

下列程式碼範例示範如何在 eShopOnContainers 的行動應用程式具現化的檢視模型類型和其相依性：

```csharp
var viewModel = _container.Resolve(viewModelType);
```

在此範例中，Autofac 要求解析要求的檢視模型的檢視模型型別，容器也會解析任何相依性。 當解析`ProfileViewModel`型別，要解析的相依性是`IOrderService`物件。 因此，Autofac 先建構`OrderService`物件，並再將它傳遞給建構函式的`ProfileViewModel`類別。 如 eShopOnContainers 的行動裝置應用程式如何建構檢視的詳細資訊模型，並將它們關聯至檢視，請參閱 <<c0> [ 自動建立檢視模型的檢視模型定位器](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

> [!NOTE]
> 註冊及解析與容器的型別具有效能的容器使用反映來建立每個類型，因為成本，尤其是相依性會為每個應用程式中的頁面巡覽重新建構。 如果有許多或深層相依性，可以大幅增加成本的建立。

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>管理已解決物件的存留期

在註冊之後的型別，Autofac 的預設行為是建立新的執行個體的已註冊的型別每次的類型解析，或當相依性機制會插入其他類別的執行個體。 在此案例中，容器不會保留已解決物件的參考。 不過，當註冊執行個體，Autofac 的預設行為是管理為單一物件的存留期。 因此，執行個體保持在範圍內中是容器位於範圍內，而容器超出範圍，且已記憶體回收，在處置時，或當程式碼明確處置容器。

Autofac 執行個體範圍可用來指定單一行為，Autofac 從已註冊的型別所建立的物件。 Autofac 執行個體範圍管理的容器來具現化物件存留期。 預設執行個體範圍`RegisterType`方法是`InstancePerDependency`範圍。 不過，`SingleInstance`可以搭配範圍`RegisterType`方法，以便建立容器，或傳回類型的單一執行個體時呼叫`Resolve`方法。 下列程式碼範例示範如何使用 Autofac 指示建立的單一執行個體`NavigationService`類別：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

第一次`INavigationService`介面是解決、 建立新的容器`NavigationService`物件，並會維護它的參考。 上的任何後續的解決方案`INavigationService`介面，容器會傳回參考`NavigationService`先前建立的物件。

> [!NOTE]
> 在處置容器時，SingleInstance 範圍就會處置已建立的物件。

Autofac 包含額外的執行個體範圍。 如需詳細資訊，請參閱 <<c0> [ 執行個體範圍](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html)readthedocs.io 上。

## <a name="summary"></a>總結

相依性插入可分離的具象類型取決於這些類型的程式碼。 它通常會使用保存的註冊與介面和抽象的型別之間的對應清單的容器和實作或擴充這些類型的具象型別。

Autofac 有助於建立鬆散偶合的應用程式，並提供所有相依性插入容器，包括註冊型別對應和物件執行個體的方法中經常發現的功能解析物件、 管理物件存留期，以及插入建構函式的物件，它會解析成的相依物件。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

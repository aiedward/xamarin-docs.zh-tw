---
title: 相依性插入
description: 本章說明 eShopOnContainers 行動應用程式如何使用相依性插入，將具象的類型與依賴這些類型的程式碼分離。
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8aea3ad36f6c35e9faf2771fc6b54c378c304afb
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933597"
---
# <a name="dependency-injection"></a>相依性插入

一般而言，當具現化物件時，會叫用類別的函式，而且物件所需的任何值都會當做引數傳遞至函式。 這是相依性插入的範例，特別是所謂的「程式性*插入*」。 物件所需的相依性會插入至此函式中。

藉由將相依性指定為介面類別型，相依性插入可將具象的類型與依賴這些類型的程式碼分離。 它通常會使用容器來保存介面與抽象類別型之間的註冊和對應清單，以及用來執行或擴充這些類型的實體類型。

另外還有其他類型的相依性插入，例如*屬性 setter 插入*和*方法呼叫插入*，但較不常看到。 因此，本章僅著重于使用相依性插入容器來執行函式插入。

## <a name="introduction-to-dependency-injection"></a>相依性插入簡介

相依性插入是一種特製化版本的控制反轉（IoC）模式，其中的顧慮是指取得所需相依性的過程。 使用相依性插入時，另一個類別會負責在執行時間將相依性插入物件中。 下列程式碼範例示範如何在 `ProfileViewModel` 使用相依性插入時結構化類別：

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

此函式會 `ProfileViewModel` 接收 `IOrderService` 實例做為引數，並由另一個類別插入。 類別中的唯一相依性 `ProfileViewModel` 是在介面型別上。 因此， `ProfileViewModel` 類別並不知道負責具現化物件的類別 `IOrderService` 。 負責具現化 `IOrderService` 物件並將其插入至類別的類別，稱為相依性 `ProfileViewModel` *插入容器*。

相依性插入容器會提供一個可具現化類別實例的功能，並根據容器的設定來管理其存留期，藉此減少物件之間的耦合。 建立物件時，容器會在其中插入物件所需的任何相依性。 如果尚未建立這些相依性，容器會先建立並解析其相依性。

> [!NOTE]
> 您也可以使用 factory 手動實作為相依性插入。 不過，使用容器會提供額外的功能，例如生命週期管理，並透過元件掃描進行註冊。

使用相依性插入容器有幾個優點：

- 容器不需要類別來尋找其相依性並管理其存留期。
- 容器允許對應已執行的相依性，而不會影響類別。
- 容器可讓您模擬相依性，藉此協助測試。
- 容器可讓您輕鬆地將新的類別新增至應用程式，以提高維護性。

在 Xamarin.Forms 使用 MVVM 的應用程式內容中，相依性插入容器通常會用來註冊和解析視圖模型，以及註冊服務並將其插入視圖模型。

有許多相依性插入容器可用，使用 TinyIoC 來管理應用程式中的視圖模型和服務類別的具現化 eShopOnContainers 行動應用程式。 TinyIoC 是在評估多個不同的容器之後選擇，而且與大多數已知的容器相較之下，在行動平臺上的效能較佳。 它有助於建立鬆散結合的應用程式，並提供在相依性插入容器中常用的所有功能，包括註冊型別對應、解析物件、管理物件存留期，以及將相依物件插入至它所解析之物件的方法。 如需 TinyIoC 的詳細資訊，請參閱 github.com 上的[TinyIoC](https://github.com/grumpydev/TinyIoC/wiki) 。

在 TinyIoC 中， `TinyIoCContainer` 類型會提供相依性插入容器。 [圖 3-1] 顯示使用此容器時的相依性，它會具現化 `IOrderService` 物件，並將其插入至 `ProfileViewModel` 類別。

![使用相依性插入時的相依性範例](dependency-injection-images/dependencyinjection.png)

**圖3-1：** 使用相依性插入時的相依性

在執行時間，容器必須知道它應該具現 `IOrderService` 化之介面的執行，才能具現化 `ProfileViewModel` 物件。 這包括：

- 容器，決定如何具現化可實作為介面的物件 `IOrderService` 。 這就是所謂的*註冊*。
- 具現化可實作為介面之物件的容器 `IOrderService` ，以及 `ProfileViewModel` 物件。 這就是所謂的*解決方案*。

最後，應用程式將會使用物件完成， `ProfileViewModel` 而且它將會變成可供垃圾收集之用。 此時， `IOrderService` 如果其他類別不共用相同的實例，垃圾收集行程就應該處置實例。

> [!TIP]
> 撰寫與容器無關的程式碼。 請一律嘗試撰寫與容器無關的程式碼，以將應用程式與所使用的特定相依性容器分離。

## <a name="registration"></a>註冊

在將相依性插入物件之前，必須先向容器註冊相依性的類型。 註冊類型通常牽涉到將介面和實作為介面的具象型別傳遞給容器。

有兩種方式可透過程式碼在容器中註冊類型和物件：

- 向容器註冊類型或對應。 必要時，容器將會建立指定類型的實例。
- 在容器中，將現有的物件註冊為 singleton。 必要時，容器會傳回現有物件的參考。

> [!TIP]
> 相依性插入容器不一定是合適的。 相依性插入導入了對小型應用程式可能不適用或很有用的額外複雜性和需求。 如果類別沒有任何相依性，或不是其他類型的相依性，則將它放在容器中可能沒有意義。 此外，如果類別具有一組屬於類型的整數相依性，而且永遠不會變更，則將它放在容器中可能沒有意義。

需要相依性插入的類型註冊應該在應用程式的單一方法中執行，而且應該在應用程式的生命週期早期叫用此方法，以確保應用程式知道其類別之間的相依性。 在 eShopOnContainers 行動應用程式中，這是由 `ViewModelLocator` 類別所執行，它 `TinyIoCContainer` 會建立物件，而是應用程式中的唯一類別，它會保存該物件的參考。 下列程式碼範例顯示 eShopOnContainers 行動應用程式如何 `TinyIoCContainer` 在類別中宣告物件 `ViewModelLocator` ：

```csharp
private static TinyIoCContainer _container;
```

類型是在函式中註冊 `ViewModelLocator` 。 這是藉由先建立實例來達成 `TinyIoCContainer` ，如下列程式碼範例所示：

```csharp
_container = new TinyIoCContainer();
```

然後，會向物件註冊類型 `TinyIoCContainer` ，下列程式碼範例會示範最常見的類型註冊形式：

```csharp
_container.Register<IRequestProvider, RequestProvider>();
```

`Register`這裡所示的方法會將介面型別對應至實體型別。 根據預設，每個介面註冊都會設定為 singleton，讓每個相依物件接收相同的共用實例。 因此， `RequestProvider` 容器中只會有一個實例，這是由需要透過函式的插入的物件所共用 `IRequestProvider` 。

實體類型也可以直接註冊，而不需要介面類別型的對應，如下列程式碼範例所示：

```csharp
_container.Register<ProfileViewModel>();
```

根據預設，每個具體的類別註冊都會設定為多重實例，因此每個相依物件都會收到新的實例。 因此，當 `ProfileViewModel` 解決時，將會建立新的實例，而且容器會插入其所需的相依性。

## <a name="resolution"></a>解決方案

註冊型別之後，可以將它解析或插入為相依性。 當類型已解析且容器需要建立新的實例時，它會將任何相依性插入實例。

一般而言，當類型解析時，會發生下列三種情況之一：

1. 如果類型尚未註冊，容器會擲回例外狀況。
1. 如果類型已註冊為 singleton，則容器會傳回單一實例。 如果這是第一次針對呼叫類型，容器會在必要時建立它，並維護它的參考。
1. 如果類型尚未註冊為 singleton，容器會傳回新的實例，而且不會維護它的參考。

下列程式碼範例會示範如何 `RequestProvider` 解析先前向 TinyIoC 註冊的類型：

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

在此範例中，會要求 TinyIoC 解析 `IRequestProvider` 類型的具象類型，以及任何相依性。 一般而言， `Resolve` 當需要特定類型的實例時，會呼叫方法。 如需控制已解析物件之存留期的詳細資訊，請參閱[管理已解析物件的存留期](#managing-the-lifetime-of-resolved-objects)。

下列程式碼範例顯示 eShopOnContainers 行動應用程式如何具現化視圖模型類型及其相依性：

```csharp
var viewModel = _container.Resolve(viewModelType);
```

在此範例中，系統會要求 TinyIoC 為要求的視圖模型解析視圖模型類型，而且容器也會解析任何相依性。 解析類型時 `ProfileViewModel` ，要解析的相依性是 `ISettingsService` 物件和 `IOrderService` 物件。 因為註冊和類別時使用了介面 `SettingsService` 註冊 `OrderService` ，所以 TinyIoC 會傳回和類別的單一實例， `SettingsService` 然後將 `OrderService` 它們傳遞給 `ProfileViewModel` 類別的函式。 如需 eShopOnContainers 行動應用程式如何建立視圖模型並將其與視圖產生關聯的詳細資訊，請參閱[使用視圖模型定位器自動建立視圖模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator)。

> [!NOTE]
> 使用容器來登錄和解析類型具有效能成本，因為容器會使用反映來建立每種類型，特別是在針對應用程式中的每個頁面巡覽重建相依性時。 如果有許多或深度相依性，則建立的成本可能會大幅增加。

## <a name="managing-the-lifetime-of-resolved-objects"></a>管理已解析物件的存留期

使用實體類別註冊來註冊型別之後，TinyIoC 的預設行為是在每次解析型別時，或是在相依性機制將實例插入其他類別時，建立註冊型別的新實例。 在此案例中，容器不會保存解析物件的參考。 不過，使用介面註冊來註冊型別時，TinyIoC 的預設行為是將物件的存留期當做 singleton 來管理。 因此，當容器在範圍內時，實例會維持在範圍內，而且會在容器超出範圍並已進行垃圾收集時處置，或是在程式碼明確處置容器時進行處置。

您可以使用流暢的 `AsSingleton` 和 API 方法來覆寫預設的 TinyIoC 註冊行為 `AsMultiInstance` 。 例如， `AsSingleton` 方法可以搭配 `Register` 方法使用，以便在呼叫方法時，容器會建立或傳回類型的單一實例 `Resolve` 。 下列程式碼範例顯示如何指示 TinyIoC 建立類別的單一實例 `LoginViewModel` ：

```csharp
_container.Register<LoginViewModel>().AsSingleton();
```

第一次 `LoginViewModel` 解析類型時，容器會建立新的 `LoginViewModel` 物件，並維護它的參考。 在的任何後續解決方案上 `LoginViewModel` ，容器 `LoginViewModel` 會傳回先前建立之物件的參考。

> [!NOTE]
> 當處置容器時，會處置註冊為單次個體的類型。

## <a name="summary"></a>總結

相依性插入可將具象的類型與依賴這些類型的程式碼分離。 它通常會使用容器來保存介面與抽象類別型之間的註冊和對應清單，以及用來執行或擴充這些類型的實體類型。

TinyIoC 是輕量容器，相較于大部分知名的容器，其功能優於行動平臺上的效能。 它有助於建立鬆散結合的應用程式，並提供在相依性插入容器中常用的所有功能，包括註冊型別對應、解析物件、管理物件存留期，以及將相依物件插入至它所解析之物件的方法。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)

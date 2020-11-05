---
title: 相依性插入
description: 本章說明 eShopOnContainers 行動應用程式如何使用相依性插入，從相依于這些型別的程式碼分離出具體類型。
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3facf6e1e5796d8e17488f3c018cba23e5f99b7f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375352"
---
# <a name="dependency-injection"></a>相依性插入

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

通常會在具現化物件時叫用類別的函式，而物件需要的任何值會以引數的形式傳遞至函式。 這是相依性插入的範例，特別稱為「函式 *插入* 」。 物件需要的相依性會插入至函式。

藉由將相依性指定為介面類別型，相依性插入可以從相依于這些型別的程式碼中分離出具體類型。 它通常會使用容器來保存介面和抽象型別之間的註冊和對應清單，以及執行或擴充這些型別的具體型別。

另外還有其他類型的相依性插入，例如 *屬性 setter 插入* ，以及 *方法呼叫插入* ，但較不常出現。 因此，這一章只著重于使用相依性插入容器來執行函式插入。

## <a name="introduction-to-dependency-injection"></a>相依性插入簡介

相依性插入是一種特殊版本的控制項反轉 (IoC) 模式，而反向考慮是取得必要相依性的程式。 使用相依性插入時，另一個類別會負責在執行時間將相依性插入物件。 下列程式碼範例示範如何在 `ProfileViewModel` 使用相依性插入時結構化類別：

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

此函 `ProfileViewModel` 式會接收 `IOrderService` 實例作為引數，並由另一個類別插入。 類別中唯一的相依性 `ProfileViewModel` 是在介面類別型上。 因此， `ProfileViewModel` 類別沒有任何負責具現化物件之類別的知識 `IOrderService` 。 負責具現化 `IOrderService` 物件，並將其插入至類別的類別 `ProfileViewModel` ，稱為相依性 *插入容器* 。

相依性插入容器藉由提供可具現化類別實例的工具，並根據容器的設定來管理其存留期，藉以減少物件之間的結合。 在建立物件期間，容器會插入物件所需的任何相依性。 如果尚未建立這些相依性，容器會先建立並解析其相依性。

> [!NOTE]
> 您也可以使用 factory 手動執行相依性插入。 不過，使用容器可提供額外的功能，例如存留期管理，以及透過元件掃描註冊。

使用相依性插入容器有幾項優點：

- 容器可讓您不需要類別來找到其相依性，以及管理其存留期。
- 容器允許對應已執行的相依性，而不會影響類別。
- 容器可讓您模擬相依性，藉此促進可測試性。
- 容器可讓您輕鬆地將新類別新增至應用程式，以提高可維護性。

在 Xamarin.Forms 使用 MVVM 的應用程式內容中，相依性插入容器通常會用來註冊和解析視圖模型，以及註冊服務並將其插入至 view 模型。

有許多相依性插入容器可供使用，使用 TinyIoC eShopOnContainers 行動應用程式來管理應用程式中的視圖模型和服務類別的具現化。 評估許多不同的容器之後，您可以選擇 TinyIoC，而且相較于大部分知名的容器，在行動平臺上提供更優異的效能。 它可協助您建立鬆散結合的應用程式，並提供在相依性插入容器中經常發現的所有功能，包括註冊類型對應、解析物件、管理物件存留期，以及將相依物件插入至其所解析之物件的函式的方法。 如需 TinyIoC 的詳細資訊，請參閱 github.com 上的 [TinyIoC](https://github.com/grumpydev/TinyIoC/wiki) 。

在 TinyIoC 中，此類型會提供相依性 `TinyIoCContainer` 插入容器。 圖3-1 顯示使用這個容器時的相依性，它會具現化 `IOrderService` 物件，並將它插入 `ProfileViewModel` 類別中。

![使用相依性插入時的相依性範例](dependency-injection-images/dependencyinjection.png)

**圖3-1：** 使用相依性插入時的相依性

在執行時間，容器必須知道它應該具現 `IOrderService` 化之介面的實作，才能具現化 `ProfileViewModel` 物件。 這包括：

- 容器，決定如何具現化可實介面的物件 `IOrderService` 。 這就是所謂的 *註冊* 。
- 容器具現化物件，該物件會執行 `IOrderService` 介面和 `ProfileViewModel` 物件。 這就是所謂的 *解決方法* 。

最後，應用程式會使用物件完成， `ProfileViewModel` 而且它將會變成可供垃圾收集。 此時， `IOrderService` 如果其他類別未共用相同的實例，垃圾收集行程就應該處置該實例。

> [!TIP]
> 寫入容器中立的程式碼。 一律嘗試撰寫與容器無關的程式碼，以將應用程式與所使用的特定相依性容器分離。

## <a name="registration"></a>註冊

在將相依性插入物件之前，必須先向容器註冊相依性的類型。 註冊型別通常牽涉到將介面傳遞給容器，以及實介面的實體型別。

有兩種方式可透過程式碼在容器中註冊類型和物件：

- 向容器註冊類型或對應。 需要時，容器將會建立指定之類型的實例。
- 以 singleton 的形式在容器中註冊現有的物件。 必要時，容器會傳回現有物件的參考。

> [!TIP]
> 不一定適合相依性插入容器。 相依性插入會導致額外的複雜性和需求，對小型應用程式而言可能不適合或很有用。 如果類別沒有任何相依性，或不是其他類型的相依性，將它放在容器中可能並不合理。 此外，如果類別具有單一的相依性集合，而這是類型的整數，而且永遠不會變更，將它放在容器中可能並不合理。

需要相依性插入的型別註冊應該在應用程式中的單一方法中執行，而這個方法應該在應用程式的生命週期早期叫用，以確保應用程式知道其類別之間的相依性。 在 eShopOnContainers 行動裝置應用程式中，這是由類別所執行 `ViewModelLocator` ，該類別會建立 `TinyIoCContainer` 物件，而是應用程式中保存該物件之參考的唯一類別。 下列程式碼範例顯示 eShopOnContainers 行動應用程式如何 `TinyIoCContainer` 在類別中宣告物件 `ViewModelLocator` ：

```csharp
private static TinyIoCContainer _container;
```

類型會在函式中註冊 `ViewModelLocator` 。 這是藉由先建立實例來完成 `TinyIoCContainer` ，如下列程式碼範例所示：

```csharp
_container = new TinyIoCContainer();
```

然後會向物件註冊類型 `TinyIoCContainer` ，而下列程式碼範例會示範最常見的類型註冊形式：

```csharp
_container.Register<IRequestProvider, RequestProvider>();
```

`Register`此處所示的方法會將介面型別對應到實體型別。 根據預設，每個介面註冊都會設定為 singleton，讓每個相依物件都會收到相同的共用實例。 因此， `RequestProvider` 容器中只會有一個實例，而這些物件是由需要透過函式插入的物件所共用 `IRequestProvider` 。

實體類型也可以直接註冊，而不需要來自介面類別型的對應，如下列程式碼範例所示：

```csharp
_container.Register<ProfileViewModel>();
```

依預設，每個實體類別註冊都會設定為多重實例，讓每個相依物件都會收到新的實例。 因此，當解析完成時 `ProfileViewModel` ，將會建立新的實例，而容器將會插入其必要的相依性。

## <a name="resolution"></a>解決方案

註冊型別之後，就可以將它解析或插入為相依性。 當類型被解析且容器需要建立新的實例時，會將任何相依性插入實例中。

一般來說，當類型解決時，會發生下列其中一種情況：

1. 如果類型尚未註冊，則容器會擲回例外狀況。
1. 如果類型已註冊為 singleton，則容器會傳回單一實例。 如果這是呼叫類型的第一次，則容器會在必要時建立它，並維護其參考。
1. 如果類型尚未註冊為 singleton，則容器會傳回新的實例，而不會維護它的參考。

下列程式碼範例會示範如何 `RequestProvider` 解析先前向 TinyIoC 註冊的型別：

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

在此範例中，系統會要求 TinyIoC 來解析 `IRequestProvider` 類型的具象型別，以及任何相依性。 一般來說， `Resolve` 當需要特定類型的實例時，就會呼叫方法。 如需控制已解析物件之存留期的詳細資訊，請參閱 [管理已解析物件的存留期](#managing-the-lifetime-of-resolved-objects)。

下列程式碼範例顯示 eShopOnContainers 行動應用程式如何具現化視圖模型類型及其相依性：

```csharp
var viewModel = _container.Resolve(viewModelType);
```

在此範例中，系統會要求 TinyIoC 解析所要求視圖模型的視圖模型類型，而且容器也會解析任何相依性。 解析型別時 `ProfileViewModel` ，要解析的相依性是 `ISettingsService` 物件和 `IOrderService` 物件。 因為在註冊和類別時使用介面 `SettingsService` 註冊 `OrderService` ，TinyIoC 會傳回和類別的單一實例， `SettingsService` `OrderService` 然後將它們傳遞給 `ProfileViewModel` 類別的函式。 如需 eShopOnContainers 行動應用程式如何建立模型並將其與視圖產生關聯的詳細資訊，請參閱 [使用 View Model 定位器自動建立視圖模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically-creating-a-view-model-with-a-view-model-locator)。

> [!NOTE]
> 使用容器來登錄和解析類型具有效能成本，因為容器會使用反映來建立每種類型，特別是在針對應用程式中的每個頁面巡覽重建相依性時。 如果有許多或深度相依性，則建立的成本可能會大幅增加。

## <a name="managing-the-lifetime-of-resolved-objects"></a>管理已解析物件的存留期

使用具體類別註冊註冊型別之後，TinyIoC 的預設行為是在每次解析型別時，或當相依性機制將實例插入其他類別時，建立已註冊型別的新實例。 在此案例中，容器不會保存已解析物件的參考。 不過，使用介面註冊註冊類型時，TinyIoC 的預設行為是將物件的存留期視為 singleton 來管理。 因此，當容器在範圍內時，實例會維持在範圍內，並在容器超出範圍且已進行垃圾收集，或在程式碼明確處置容器時處置。

您可以使用流暢的 `AsSingleton` 和 API 方法來覆寫預設的 TinyIoC 註冊行為 `AsMultiInstance` 。 例如， `AsSingleton` 方法可以與方法搭配使用 `Register` ，讓容器在呼叫方法時，建立或傳回類型的單一實例 `Resolve` 。 下列程式碼範例顯示如何指示 TinyIoC 建立類別的單一實例 `LoginViewModel` ：

```csharp
_container.Register<LoginViewModel>().AsSingleton();
```

第一次 `LoginViewModel` 解析類型時，容器會建立新的 `LoginViewModel` 物件，並維護它的參考。 在的任何後續解析中 `LoginViewModel` ，容器 `LoginViewModel` 會傳回先前建立之物件的參考。

> [!NOTE]
> 當處置容器時，會處置註冊為 singleton 的類型。

## <a name="summary"></a>總結

相依性插入可讓您從相依于這些型別的程式碼中分離出具象類型。 它通常會使用容器來保存介面和抽象型別之間的註冊和對應清單，以及執行或擴充這些型別的實體型別。

TinyIoC 是輕量的容器，相較于大部分的知名容器，其具有優於行動平臺的效能。 它可協助您建立鬆散結合的應用程式，並提供在相依性插入容器中經常發現的所有功能，包括註冊類型對應、解析物件、管理物件存留期，以及將相依物件插入至其所解析物件之物件的方法。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)

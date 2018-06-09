---
title: 之間通訊的鬆散結合的元件
description: '本章說明 eShopOnContainers 行動裝置應用程式如何實作發行-訂閱模式，讓不方便，連結物件和型別參考的元件之間的訊息為基礎的通訊 '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 797032d17babe986de1357c6ac3291a4960d87ff
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245077"
---
# <a name="communicating-between-loosely-coupled-components"></a>之間通訊的鬆散結合的元件

發佈-訂閱模式為傳訊模式，其中 「 發行者 」 傳送訊息而不需要任何的接收者，又稱為 「 訂閱者 」 的知識。 同樣地，「 訂閱者 」 來接聽特定的訊息，而不需要任何發行者的知識。

在.NET 中的事件實作發行-訂閱模式，而且最簡單且直接的方法通訊層元件如果鬆散偶合不是必要項目，例如控制項和包含它的頁面之間。 不過，發行者和訂閱者端的存留期會結合的物件參考，而且訂閱者類型必須具有 「 發行者 」 類型的參考。 這可以時，建立記憶體管理的問題，尤其是有靜態或存留較久物件的事件訂閱短期存在的物件。 如果未移除事件處理常式，「 訂閱者 」 將會保持作用中 「 發行者 」，它參考，這會防止或延遲的訂閱者端記憶體回收。

## <a name="introduction-to-messagingcenter"></a>MessagingCenter 簡介

Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別會實作發行-訂閱模式，讓不方便，連結物件和型別參考的元件之間的訊息為基礎的通訊。 此機制可讓發行者和訂閱者進行通訊，而不需要的參考，幫助減少元件，同時也允許以獨立開發和測試的元件之間的相依性。

[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別會提供多點傳送的發佈-訂閱功能。 這表示可以有多個發佈單一訊息的發行者，而且可以有多個訂閱者接聽相同的訊息。 圖 4-1 會說明這個關聯性：

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "多點傳送的發佈-訂閱功能")

**圖 4-1:** 多點傳送的發佈-訂閱功能

「 發行者 」 使用傳送郵件[ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender%7D/p/TSender/System.String/)方法，而訂閱者接聽訊息使用[ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/)方法。 此外，「 訂閱者 」 可以也取消訂閱的訊息訂閱，必要時，與[ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender%7D/p/System.Object/System.String/)方法。

就內部而言， [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別會使用弱式參考。 這表示它不會保留物件保持運作，並允許其進行記憶體回收。 因此，它應該只是必要類別不再想要接收訊息時，從訊息取消訂閱。

EShopOnContainers 行動裝置應用程式會使用[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別之間通訊的鬆散結合的元件。 應用程式會定義三個訊息：

-   `AddProduct`訊息發佈的`CatalogViewModel`類別項目新增至購物籃時。 傳回，`BasketViewModel`類別訂閱訊息，並遞增回應購物籃中的項目數。 此外，`BasketViewModel`類別也取消訂閱根據這則訊息。
-   `Filter`訊息發佈的`CatalogViewModel`類別時，使用者將品牌或類型篩選套用至從目錄中顯示的項目。 傳回，`CatalogView`類別訂閱訊息，並更新 UI，讓符合篩選準則的項目會顯示。
-   `ChangeTab`訊息發佈的`MainViewModel`類別時`CheckoutViewModel`瀏覽至`MainViewModel`成功建立和提交新訂單。 傳回，`MainView`類別訂閱該訊息並更新 UI 因此的**我的設定檔** 索引標籤為作用中，以顯示使用者訂單。

> [!NOTE]
> 雖然[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別允許鬆散偶合的類別之間的通訊，它不提供此問題只架構方案。 例如，檢視模型和檢視之間的通訊都可以達成，繫結引擎，以及透過屬性變更通知。 此外，兩個檢視模型之間的通訊也可藉由在巡覽期間傳遞的資料。

EShopOnContainers 行動應用程式，[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)用來更新至另一個類別中發生的動作的回應中的 UI 中。 因此，訊息，在 UI 執行緒上發佈與訂閱者接收訊息的相同執行緒上。

> [!TIP]
> 封送處理 UI 執行緒執行 UI 更新時。 如果從背景執行緒傳送的訊息，才能更新 UI，處理 「 訂閱者 」 中的 UI 執行緒上的訊息叫用[ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/)方法。

如需有關[ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)，請參閱[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)。

## <a name="defining-a-message"></a>定義訊息

[`MessagingCenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) 訊息是用來識別訊息的字串。 下列程式碼範例顯示 eShopOnContainers 行動裝置應用程式內定義的訊息：

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

在此範例中，定義訊息使用的常數。 這種方法的優點是，它會提供編譯時間型別安全和重構支援。

## <a name="publishing-a-message"></a>發佈訊息

發行者通知 「 訂閱者 」 的訊息，其中[ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)多載。 下列程式碼範例示範如何發行`AddProduct`訊息：

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

在此範例中， [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)方法會指定三個引數：

-   第一個引數會指定寄件者類別。 必須指定任何 「 訂閱者 」 要接收的訊息寄件者類別。
-   第二個引數指定的訊息。
-   第三個引數指定的裝載資料傳送到 「 訂閱者 」。 在此案例是裝載資料`CatalogItem`執行個體。

[ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)方法將會發佈訊息，以及其裝載的資料，使用和不理方法。 因此，即使沒有任何訂閱者註冊以接收訊息可以傳送訊息。 在此情況下，會忽略已傳送的訊息。

> [!NOTE]
> [ `MessagingCenter.Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)方法可以使用泛型參數，來控制傳遞訊息的方式。 因此，不同的訂閱者可以接收共用訊息的身分識別，但傳送不同的裝載資料類型的多個訊息。

## <a name="subscribing-to-a-message"></a>訂閱訊息

訂閱者可以接收訊息，使用其中一種註冊[ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/)多載。 下列程式碼範例示範如何 eShopOnContainers 行動裝置應用程式，訂閱和處理序，`AddProduct`訊息：

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

在此範例中， [ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/)方法會訂閱`AddProduct`訊息和接收訊息的回應中執行的回呼委派。 此回呼委派，做為 lambda 運算式，指定執行更新 UI 的程式碼。

> [!TIP]
> 請考慮使用不可變的內容資料。 請勿嘗試修改從裝載資料中的回呼委派，因為數個執行緒可能已接收的資料同時存取。 在此案例中，裝載資料應該是不變，若要避免並行存取錯誤。

訂閱者可能不需要處理的已發佈的訊息，每個執行個體和指定的泛型型別引數可以控制這[ `Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender%7D/p/System.Object/System.String/System.Action%7BTSender%7D/TSender/)方法。 在此範例中，「 訂閱者 」 只會接收`AddProduct`從傳送訊息`CatalogViewModel`類別，其內容資料是`CatalogItem`執行個體。

## <a name="unsubscribing-from-a-message"></a>從訊息 unsubscribing

「 訂閱者 」 可以取消訂閱它們不再想要接收的訊息。 這使用其中一個來達成[ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/)多載，如下列程式碼範例所示：

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

在此範例中， [ `Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/)方法語法反映訂閱接收時所指定的型別引數`AddProduct`訊息。

## <a name="summary"></a>總結

Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別會實作發行-訂閱模式，讓不方便，連結物件和型別參考的元件之間的訊息為基礎的通訊。 此機制可讓發行者和訂閱者進行通訊，而不需要的參考，幫助減少元件，同時也允許以獨立開發和測試的元件之間的相依性。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

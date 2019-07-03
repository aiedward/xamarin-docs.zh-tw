---
title: 在偶合程度低的元件之間通訊
description: '本章說明在 eShopOnContainers 的行動應用程式如何實作發行-訂閱模式，讓不方便連結物件和型別參考的元件之間的訊息式通訊 '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9848d2b832990032bc7eb7f2e3a93c896457134c
ms.sourcegitcommit: e95296f9e516975f5f32d822c323a71fd84007b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538689"
---
# <a name="communicating-between-loosely-coupled-components"></a>在偶合程度低的元件之間通訊

發佈-訂閱模式是傳訊模式，在其中發行者會傳送訊息而不需要任何接收者，稱為 「 訂閱者 」 的知識。 同樣地，訂閱者接聽的特定訊息，而不需要任何發行者的知識。

在.NET 中的事件實作發行-訂閱模式，而且最簡單又直接的方法的通訊層元件如果鬆散結合不是必要項目，例如控制項和包含它的頁面之間。 不過，發行者和訂閱者端的存留期會結合物件參考，而且訂閱者類型必須具有 「 發行者 」 類型的參考。 這造成記憶體管理的問題，特別是當訂閱的靜態或長時間執行的物件事件短期存在的物件。 如果未移除的事件處理常式，「 訂閱者 」 會保持運作的 「 發行者 」，將它的參考，這會防止或延遲的訂閱者端記憶體回收。

## <a name="introduction-to-messagingcenter"></a>MessagingCenter 簡介

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)類別會實作 「 發佈-訂閱模式，允許訊息式都不太方便，連結物件和型別參考的元件之間的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要的參考，協助降低元件，同時也允許獨立開發和測試的元件之間的相依性。

[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)類別會提供多點傳送的發佈-訂閱功能。 這表示可以有多個發佈單一訊息的發行者，而且可以有多個訂閱者接聽相同的訊息。 圖 4-1 會說明這個關聯性：

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "多點傳送的發佈-訂閱功能")

**圖 4-1:** 多點傳送的發佈-訂閱功能

發行者將使用的訊息傳送[ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)方法，而訂閱者接聽使用的訊息[ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)方法。 此外，訂閱者也可以從訊息的訂用帳戶，如有需要，使用[ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)方法。

就內部而言， [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)類別會使用弱式參考。 這表示它將不讓物件保持運作，並可讓他們進行記憶體回收。 因此，它只應該需要類別不再希望收到訊息時，從訊息取消訂閱。

EShopOnContainers 的行動裝置應用程式會使用[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)類別之間通訊的鬆散結合的元件。 應用程式會定義三個訊息：

-   `AddProduct`訊息由發佈`CatalogViewModel`類別的項目會新增至購物籃。 在 退貨，`BasketViewModel`類別訂閱訊息，並遞增在回應中購物籃中的項目數。 颾魤 ㄛ`BasketViewModel`類別也會取消訂閱此訊息。
-   `Filter`訊息由發佈`CatalogViewModel`類別，在使用者的品牌或型別將篩選套用到從目錄顯示的項目。 在 退貨，`CatalogView`類別訂閱訊息，並更新 UI，如此會顯示符合篩選準則的項目。
-   `ChangeTab`訊息由發佈`MainViewModel`類別時`CheckoutViewModel`瀏覽至`MainViewModel`成功建立和提交新訂單。 另一方面`MainView`類別訂閱的訊息和更新 UI 因此所**我的設定檔** 索引標籤為作用中，以顯示使用者的訂單。

> [!NOTE]
> 雖然[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)類別允許鬆散偶合的類別之間的通訊，它不提供此問題只架構解決方案。 比方說，檢視模型和檢視之間的通訊也可藉由繫結引擎，以及透過屬性變更通知。 此外，兩個檢視模型之間的通訊也可藉由在巡覽期間傳遞資料。

在 eShopOnContainers 的行動應用程式中[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)用來更新至另一個類別中發生動作的回應中的 UI 中。 因此，訊息會在 UI 執行緒上發行的訂閱者接收的相同執行緒上的訊息。

> [!TIP]
> 執行 UI 更新時，封送至 UI 執行緒。 如果需要更新 UI 從背景執行緒傳送訊息，則在叫用處理 「 訂閱者 」 中的 UI 執行緒上的訊息[ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法。

如需詳細資訊[ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)，請參閱[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)。

## <a name="defining-a-message"></a>定義訊息

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 訊息是用來識別訊息的字串。 下列程式碼範例會顯示在 eShopOnContainers 的行動裝置應用程式內定義的訊息：

```csharp
public class MessengerKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

在此範例中，訊息是使用常數來定義。 這種方法的優點是，它會提供編譯時期型別安全和重構支援。

## <a name="publishing-a-message"></a>發佈訊息

發行者通知 「 訂閱者 」 的訊息，其中[ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)多載。 下列程式碼範例示範如何發行`AddProduct`訊息：

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

在此範例中， [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)方法會指定三個引數：

-   第一個引數指定寄件者的類別。 必須指定任何訂閱者想要接收訊息的寄件者類別。
-   第二個引數指定的訊息。
-   第三個引數會指定要傳送至 「 訂閱者 」 的承載資料。 在此情況下裝載資料是`CatalogItem`執行個體。

[ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)訊息和其承載資料，使用單一事件-fire-and-forget 方法，將會發行方法。 因此，即使沒有任何已註冊要接收訊息的訂閱者可以傳送訊息。 在此情況下，會忽略已傳送的訊息。

> [!NOTE]
> [ `MessagingCenter.Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)方法可以使用泛型參數，來控制如何傳遞訊息。 因此，不同的訂閱者可以接收共用訊息的身分識別，但傳送不同的裝載資料類型的多個訊息。

## <a name="subscribing-to-a-message"></a>訂閱訊息

訂閱者可以註冊要接收訊息，使用其中一種[ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)多載。 下列程式碼範例示範如何訂閱，eShopOnContainers 的行動裝置應用程式，並處理，`AddProduct`訊息：

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

在此範例中， [ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)方法會訂閱`AddProduct`訊息，然後執行回呼委派，以接收訊息的回應。 此回呼委派，指定 lambda 運算式，為執行更新 UI 的程式碼。

> [!TIP]
> 請考慮使用不可變的承載資料。 請勿嘗試修改從承載資料中的回呼委派，因為有數個執行緒可能已接收的資料同時存取。 在此案例中，承載資料應該是不可變，以避免發生並行錯誤。

訂閱者可能不需要處理的已發佈的訊息，每個執行個體，且這可以受指定的泛型型別引數[ `Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)方法。 在此範例中，「 訂閱者 」 只會收到`AddProduct`所傳來的訊息`CatalogViewModel`類別，其內容資料是`CatalogItem`執行個體。

## <a name="unsubscribing-from-a-message"></a>取消訂閱訊息

訂閱者可以從他們不再想要接收的訊息取消訂閱。 這使用其中一個來達成[ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)多載，如下列程式碼範例所示：

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

在此範例中， [ `Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)方法的語法會反映接收訂閱時所指定的型別引數`AddProduct`訊息。

## <a name="summary"></a>總結

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)類別會實作 「 發佈-訂閱模式，允許訊息式都不太方便，連結物件和型別參考的元件之間的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要的參考，協助降低元件，同時也允許獨立開發和測試的元件之間的相依性。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)

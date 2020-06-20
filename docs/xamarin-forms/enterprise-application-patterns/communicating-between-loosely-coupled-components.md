---
title: 在偶合程度低的元件之間通訊
description: '本章說明 eShopOnContainers 行動應用程式如何執行發佈-訂閱模式，以允許物件與類型參考不方便連結的元件之間以訊息為基礎的通訊 '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c35cd6e30e7843cda0431581025aa7440a21cc29
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140045"
---
# <a name="communicating-between-loosely-coupled-components"></a>在偶合程度低的元件之間通訊

發行-訂閱模式是一種訊息模式，發行者可以在不知道任何接收者 (稱為訂閱者) 的情況下傳送訊息。 同樣地，訂閱者可以在不知道任何發行者的情況下接聽特定訊息。

.NET 中的事件會執行發佈-訂閱模式，如果不需要鬆散結合（例如控制項和包含它的頁面），則是在元件之間的通訊層最簡單且直接的方法。 不過，「發行者」和「訂閱者」存留期會結合彼此的物件參考，而「訂閱者」型別必須具有「發行者」型別的參考。 這可能會造成記憶體管理問題，特別是當有短期的物件訂閱靜態或長時間物件的事件時。 如果未移除事件處理常式，訂閱者將會在發行者中的參考保持運作狀態，這會防止或延遲訂閱者的垃圾收集。

## <a name="introduction-to-messagingcenter"></a>MessagingCenter 簡介

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會執行發行-訂閱模式，允許不方便透過物件和類型參考連結的元件之間以訊息為基礎的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要彼此的參考，協助減少元件之間的相依性，同時也允許獨立開發和測試元件。

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)類別提供多播發布-訂閱功能。 這表示可以有多個發佈單一訊息的發行者，而且可以有多個訂閱者接聽相同的訊息。 圖4-1 說明此關聯性：

![](communicating-between-loosely-coupled-components-images/messagingcenter.png "Multicast publish-subscribe functionality")

**圖4-1：** 多播發布-訂閱功能

發行者會使用方法來傳送訊息 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) ，而訂閱者則會使用方法來接聽訊息 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 。 此外，訂閱者也可以使用方法，在必要時取消訂閱訊息訂閱 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 。

就內部而言， [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會使用弱式參考。 這表示它將不會讓物件持續運作，並將允許它們進行記憶體回收。 因此，應該只有當類別不再希望收到訊息時，才需要取消訂閱訊息。

EShopOnContainers 行動應用程式會使用 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別，在鬆散結合的元件之間進行通訊。 應用程式會定義三個訊息：

- `AddProduct` `CatalogViewModel` 當專案新增至購物籃時，類別會發佈此訊息。 在傳回時， `BasketViewModel` 類別會訂閱訊息，並遞增購物籃中的專案數以回應。 此外， `BasketViewModel` 類別也會從這個訊息取消訂閱。
- `Filter` `CatalogViewModel` 當使用者將品牌或類型篩選套用至顯示在目錄中的專案時，類別會發佈訊息。 在傳回時， `CatalogView` 類別會訂閱訊息並更新 UI，因此只會顯示符合篩選準則的專案。
- `ChangeTab` `MainViewModel` 當 `CheckoutViewModel` 流覽至 `MainViewModel` 下列成功建立和提交新訂單時，類別會發佈訊息。 在 [傳回] 中， `MainView` 類別會訂閱訊息並更新 UI，讓 [**我的設定檔**] 索引標籤為作用中狀態，以顯示使用者的訂單。

> [!NOTE]
> 雖然 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別允許鬆散結合類別之間的通訊，但它並不提供此問題的唯一架構解決方案。 例如，您也可以透過系結引擎和屬性變更通知來達成視圖模型和視圖之間的通訊。 此外，兩個視圖模型之間的通訊也可以藉由在導覽期間傳遞資料來達成。

在 eShopOnContainers 行動裝置應用程式中， [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 會用來更新 UI 中的，以回應另一個類別中發生的動作。 因此，訊息會在 UI 執行緒上發佈，而訂閱者會在相同的執行緒上接收訊息。

> [!TIP]
> 執行 UI 更新時封送處理至 UI 執行緒。 如果需要從背景執行緒傳送的訊息來更新 UI，請叫用方法，以在「訂閱者」端的 UI 執行緒上處理訊息 [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) 。

如需的詳細資訊 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) ，請參閱[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)。

## <a name="defining-a-message"></a>定義訊息

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)訊息是用來識別訊息的字串。 下列程式碼範例顯示在 eShopOnContainers 行動裝置應用程式中定義的訊息：

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

在此範例中，會使用常數來定義訊息。 這種方法的優點是它會提供編譯時期型別安全和重整支援。

## <a name="publishing-a-message"></a>發行訊息

發行者會使用其中一個多載來通知訊息的訂閱者 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 。 下列程式碼範例將示範如何發佈 `AddProduct` 訊息：

```csharp
MessagingCenter.Send(this, MessengerKeys.AddProduct, catalogItem);
```

在此範例中， [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法會指定三個引數：

- 第一個引數會指定寄件者類別。 所有想要接收訊息的訂閱者，都必須指定寄件者類別。
- 第二個引數會指定訊息。
- 第三個引數會指定要傳送給訂閱者的裝載資料。 在此情況下，裝載資料是 `CatalogItem` 實例。

[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法會使用暫時的方法來發行訊息和其內容資料。 因此，即使沒有任何訂閱者已註冊要接收訊息，也會傳送訊息。 在此情況下，會忽略已傳送的訊息。

> [!NOTE]
> [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法可以使用泛型參數來控制訊息的傳遞方式。 因此，不同的訂閱者可以接收共用訊息識別但傳送不同裝載資料類型的多個訊息。

## <a name="subscribing-to-a-message"></a>訂閱訊息

訂閱者可以註冊，以使用其中一個多載來接收訊息 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 。 下列程式碼範例示範 eShopOnContainers mobile 應用程式如何訂閱和處理 `AddProduct` 訊息：

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

在此範例中， [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法會訂閱 `AddProduct` 訊息，並執行回呼委派以回應接收訊息。 這個回呼委派（指定為 lambda 運算式）會執行更新 UI 的程式碼。

> [!TIP]
> 請考慮使用不可變的裝載資料。 請不要嘗試修改回呼委派內的裝載資料，因為有數個執行緒可以同時存取接收的資料。 在此案例中，裝載資料應該是不可變的，以避免發生並行錯誤。

訂閱者可能不需要處理已發行訊息的每個實例，這可以由方法上指定的泛型型別引數來控制 [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 。 在此範例中，訂閱者只會接收 `AddProduct` 從類別傳送的訊息 `CatalogViewModel` ，其裝載資料為 `CatalogItem` 實例。

## <a name="unsubscribing-from-a-message"></a>從郵件取消訂閱

若訂閱者不想再接收訊息，則應從訊息中取消訂閱。 這是使用其中一個多載來達成 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) ，如下列程式碼範例所示：

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessengerKeys.AddProduct);
```

在此範例中， [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法語法會反映訂閱接收訊息時所指定的型別引數 `AddProduct` 。

## <a name="summary"></a>摘要

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會執行發行-訂閱模式，允許不方便透過物件和類型參考連結的元件之間以訊息為基礎的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要彼此的參考，協助減少元件之間的相依性，同時也允許獨立開發和測試元件。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)

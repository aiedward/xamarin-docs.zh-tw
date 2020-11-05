---
title: 在偶合程度低的元件之間通訊
description: '本章說明 eShopOnContainers 行動應用程式如何執行發佈-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊 '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be8b0866a6d16fdec62f7c6cd3f4fc3e6de3168d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374897"
---
# <a name="communicating-between-loosely-coupled-components"></a>在偶合程度低的元件之間通訊

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

發行-訂閱模式是一種訊息模式，發行者可以在不知道任何接收者 (稱為訂閱者) 的情況下傳送訊息。 同樣地，訂閱者可以在不知道任何發行者的情況下接聽特定訊息。

.NET 中的事件會執行發佈-訂閱模式，而且如果不需要鬆散結合（例如控制項和包含它的頁面），則是在元件之間的通訊層最簡單明瞭的方法。 不過，「發行者」和「訂閱者」存留期是由彼此的物件參考所結合，而「訂閱者」類型必須參考「發行者」類型。 這可能會造成記憶體管理的問題，尤其是當有短暫的物件訂閱靜態或長時間的物件的事件時。 如果未移除事件處理常式，訂閱者將會在發行者中將其參考保持運作，而這將會防止或延遲訂閱者的垃圾收集。

## <a name="introduction-to-messagingcenter"></a>MessagingCenter 簡介

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會執行發佈-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要彼此的參考，有助於減少元件之間的相依性，同時也允許獨立開發和測試元件。

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)類別提供多播發布/訂閱功能。 這表示您可以有多個發佈單一訊息的發行者，而且可以有多個訂閱者接聽相同的訊息。 圖4-1 說明此關聯性：

![多點傳送的發行-訂閱功能](communicating-between-loosely-coupled-components-images/messagingcenter.png)

**圖4-1：** 多播發布-訂閱功能

發行者會使用方法來傳送訊息 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) ，而訂閱者則使用方法來接聽訊息 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 。 此外，訂閱者也可以在必要時，使用方法取消訂閱訊息訂閱 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 。

就內部而言，此 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會使用弱式參考。 這表示它將不會讓物件持續運作，並將允許它們進行記憶體回收。 因此，應該只有當類別不再希望收到訊息時，才需要取消訂閱訊息。

EShopOnContainers 行動應用程式會使用 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別在鬆散結合的元件之間進行通訊。 應用程式會定義三個訊息：

- 將 `AddProduct` `CatalogViewModel` 專案新增至購物籃時，類別會發佈此訊息。 傳回時， `BasketViewModel` 類別會訂閱訊息，並以回應方式遞增購物籃中的專案數。 此外， `BasketViewModel` 此類別也會從此訊息取消訂閱。
- `Filter` `CatalogViewModel` 當使用者將品牌或類型篩選套用至目錄中顯示的專案時，類別會發佈此訊息。 在傳回時， `CatalogView` 類別會訂閱訊息並更新 UI，因此只會顯示符合篩選準則的專案。
- `ChangeTab` `MainViewModel` 當您 `CheckoutViewModel` 流覽至 `MainViewModel` 下列成功建立和提交新訂單時，類別會發佈此訊息。 在傳回時， `MainView` 類別會訂閱訊息並更新 UI，讓 **我的設定檔** 索引標籤為使用中狀態，以顯示使用者的訂單。

> [!NOTE]
> 雖然 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別允許鬆散結合類別之間的通訊，但它並不提供唯一的架構解決方案來解決此問題。 例如，您也可以透過系結引擎和屬性變更通知來達成視圖模型與視圖之間的通訊。 此外，您也可以藉由在流覽期間傳遞資料來達成兩個視圖模型之間的通訊。

在 eShopOnContainers 行動裝置應用程式中， [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 是用來更新 UI 以回應另一個類別中發生的動作。 因此，訊息會在 UI 執行緒上發佈，而訂閱者會在相同的執行緒上接收訊息。

> [!TIP]
> 執行 UI 更新時封送處理至 UI 執行緒。 如果需要從背景執行緒傳送的訊息更新 UI，請叫用方法，在訂閱者的 UI 執行緒上處理訊息 [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) 。

如需的詳細資訊 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) ，請參閱 [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)。

## <a name="defining-a-message"></a>定義訊息

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 訊息是用來識別訊息的字串。 下列程式碼範例顯示在 eShopOnContainers 行動裝置應用程式中定義的訊息：

```csharp
public class MessageKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

在此範例中，會使用常數來定義訊息。 這種方法的優點是它會提供編譯時間類型的安全和重構支援。

## <a name="publishing-a-message"></a>發行訊息

發行者會使用其中一個多載通知訊息的訂閱者 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 。 下列程式碼範例將示範如何發行 `AddProduct` 訊息：

```csharp
MessagingCenter.Send(this, MessageKeys.AddProduct, catalogItem);
```

在此範例中， [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法會指定三個引數：

- 第一個引數會指定傳送者類別。 傳送者類別必須由任何想要接收訊息的訂閱者指定。
- 第二個引數會指定訊息。
- 第三個引數會指定要傳送給訂閱者的裝載資料。 在此情況下，承載資料是 `CatalogItem` 實例。

[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法會使用火災和遺忘的方法，發佈訊息及其承載資料。 因此，即使沒有任何訂閱者已註冊要接收訊息，也會傳送訊息。 在此情況下，會忽略已傳送的訊息。

> [!NOTE]
> [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法可以使用泛型參數來控制訊息的傳遞方式。 因此，不同的訂閱者可以接收共用訊息識別但傳送不同承載資料類型的多個訊息。

## <a name="subscribing-to-a-message"></a>訂閱訊息

訂閱者可以註冊，以使用其中一個多載接收訊息 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 。 下列程式碼範例示範 eShopOnContainers mobile 應用程式如何訂閱和處理 `AddProduct` 訊息：

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

在此範例中， [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法會訂閱 `AddProduct` 訊息，並執行回呼委派以回應接收訊息。 這個回呼委派（指定為 lambda 運算式）會執行更新 UI 的程式碼。

> [!TIP]
> 請考慮使用不可變的裝載資料。 請勿嘗試從回呼委派內修改承載資料，因為有數個執行緒可能同時存取接收的資料。 在此案例中，裝載資料應該是不可變的，以避免發生並行錯誤。

訂閱者可能不需要處理已發行訊息的每個實例，而且可以由方法上指定的泛型型別引數來控制 [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 。 在此範例中，訂閱者只會收到 `AddProduct` 從類別傳送的訊息 `CatalogViewModel` ，其承載資料為 `CatalogItem` 實例。

## <a name="unsubscribing-from-a-message"></a>取消訂閱訊息

若訂閱者不想再接收訊息，則應從訊息中取消訂閱。 使用其中一個多載即可達成此目的 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) ，如下列程式碼範例所示：

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessageKeys.AddProduct);
```

在此範例中， [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法語法會反映在訂閱接收訊息時所指定的類型引數 `AddProduct` 。

## <a name="summary"></a>總結

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會執行發佈-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊。 這項機制可讓發行者和訂閱者進行通訊，而不需要彼此的參考，有助於減少元件之間的相依性，同時也允許獨立開發和測試元件。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)

---
title: Xamarin.Forms MessagingCenter
description: Xamarin.FormsMessagingCenter 類別會實作為發佈-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d24ff6831bb8b52d840ff2db7c13c3eb674b7bd8
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555784"
---
# <a name="no-locxamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

發行-訂閱模式是一種訊息模式，發行者可以在不知道任何接收者 (稱為訂閱者) 的情況下傳送訊息。 同樣地，訂閱者可以在不知道任何發行者的情況下接聽特定訊息。

.NET 中的事件會執行發佈-訂閱模式，而且如果不需要鬆散結合（例如控制項和包含它的頁面），則是在元件之間的通訊層最簡單明瞭的方法。 不過，「發行者」和「訂閱者」存留期是由彼此的物件參考所結合，而「訂閱者」類型必須參考「發行者」類型。 這可能會造成記憶體管理的問題，尤其是當有短暫的物件訂閱靜態或長時間的物件的事件時。 如果未移除事件處理常式，訂閱者將會在發行者中將其參考保持運作，而這將會防止或延遲訂閱者的垃圾收集。

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會執行發佈-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊。 此機制讓發行者和訂閱者不需彼此參考就能進行通訊，有助於減少兩者之間的相依性。

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)類別提供多播發布/訂閱功能。 這表示可以有多個發行單一訊息的發行者，而且可以有多個接聽相同訊息的訂閱者：

![多點傳送的發行-訂閱功能](messaging-center-images/messaging-center.png)

發行者會使用方法來傳送訊息 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) ，而訂閱者則使用方法來接聽訊息 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 。 此外，訂閱者也可以在必要時，使用方法取消訂閱訊息訂閱 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 。

> [!IMPORTANT]
> 就內部而言，此 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會使用弱式參考。 這表示它將不會讓物件持續運作，並將允許它們進行記憶體回收。 因此，應該只有當類別不再希望收到訊息時，才需要取消訂閱訊息。

## <a name="publish-a-message"></a>發行訊息

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 訊息為字串。 發行者會使用其中一個多載通知訊息的訂閱者 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 。 下列程式碼範例會發行 `Hi` 訊息：

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

在此範例中， [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法會指定代表寄件者的泛型引數。 若要接收訊息，訂閱者也必須指定相同的泛型引數，表示它們正在接聽來自該傳送者的訊息。 此外，此範例會指定兩個方法引數：

- 第一個引數會指定傳送者執行個體。
- 第二個引數會指定訊息。

承載資料也可以與訊息一起傳送：

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

在此範例中， [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法會指定兩個泛型引數。 第一個是傳送訊息的類型，而第二個是要傳送的承載資料類型。 若要接收訊息，訂閱者也必須指定相同的泛型引數。 這會啟用多則訊息，這類訊息會共用一個訊息識別碼，但傳送不同的承載資料類型以供不同的訂閱者接收。 此外，此範例會指定第三個方法引數，表示要傳送給訂閱者的承載資料。 在此案例中，承載資料為 `string`。

[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法會使用火災和遺忘的方法，發佈訊息和任何承載資料。 因此，即使沒有任何訂閱者已註冊要接收訊息，也會傳送訊息。 在此情況下，會忽略已傳送的訊息。

## <a name="subscribe-to-a-message"></a>訂閱訊息

訂閱者可以註冊，以使用其中一個多載接收訊息 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 。 下列程式碼範例示範此作法的範例：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

在此範例中， [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法會將 `this` 物件訂閱給型別所 `Hi` 傳送的訊息 `MainPage` ，並執行回呼委派以回應接收訊息。 指定為 Lambda 運算式的回呼委派可以是更新 UI、儲存一些資料或觸發一些其他作業的程式碼。

> [!NOTE]
> 訂閱者可能不需要處理已發行訊息的每個實例，而且可以由方法上指定的泛型型別引數來控制 [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 。

下列範例示範如何訂閱包含承載資料的訊息：

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

在此範例中， [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法會訂閱 `Hi` 型別所傳送的訊息 `MainPage` ，其承載資料為 `string` 。 回呼委派會執行以回應接收這類訊息，其會在警示中顯示承載資料。

> [!IMPORTANT]
> 方法所執行的委派將會 `Subscribe` 在使用方法發行訊息的相同執行緒上執行 `Send` 。

## <a name="unsubscribe-from-a-message"></a>從訊息中取消訂閱

若訂閱者不想再接收訊息，則應從訊息中取消訂閱。 使用其中一個多載可達成此目的 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) ：

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

在此範例中， [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法會 `this` 從型別所傳送的訊息取消訂閱物件 `Hi` `MainPage` 。

包含承載資料的訊息應使用 [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 指定兩個泛型引數的多載來取消訂閱：

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

在此範例中， [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法會從型別所傳送的訊息中取消訂閱 `this` 物件 `Hi` `MainPage` ，其承載資料為 `string` 。

## <a name="related-links"></a>相關連結

- [MessagingCenterSample](/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
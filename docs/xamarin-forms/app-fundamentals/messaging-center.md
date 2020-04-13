---
title: Xamarin.Forms MessagingCenter
description: Xamarin.Forms MessagingCenter 類別會實作發行-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 0e5fd88678becd7becfcb1c43e14b1e33aad72de
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75489872"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

發行-訂閱模式是一種訊息模式，發行者可以在不知道任何接收者 (稱為訂閱者) 的情況下傳送訊息。 同樣地，訂閱者可以在不知道任何發行者的情況下接聽特定訊息。

.NET 中的事件實現發佈-訂閱模式,並且對於不需要鬆散耦合(如控件和包含它的頁面)的元件之間的通信層,這是最簡單、最直接的方法。 但是,發佈者和訂閱者存留期通過彼此的物件引用耦合,並且訂閱者類型必須具有對發行者類型的引用。 這會產生記憶體管理問題,尤其是在有短壽命物件訂閱靜態或長壽命物件的事件時。 如果未刪除事件處理程式,則發布者中的引用將保持訂閱者處於活動狀態,這將防止或延遲訂閱者的垃圾回收。

Xamarin.Forms[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)類實現發佈-訂閱模式,允許元件之間基於消息的通信,這些元件不方便按物件和類型引用連結。 此機制讓發行者和訂閱者不需彼此參考就能進行通訊，有助於減少兩者之間的相依性。

該[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)類提供多播發佈訂閱功能。 這表示可以有多個發行單一訊息的發行者，而且可以有多個接聽相同訊息的訂閱者：

![](messaging-center-images/messaging-center.png "Multicast publish-subscribe functionality")

發行者使用[`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法 發送消息,而訂閱者[`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*)使用 方法 偵聽消息。 此外,訂閱者還可以使用[`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)方法 取消訂閱消息訂閱(如果需要)。

> [!IMPORTANT]
> 在內部,[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)類使用弱引用。 這表示它將不會讓物件持續運作，並將允許它們進行記憶體回收。 因此，應該只有當類別不再希望收到訊息時，才需要取消訂閱訊息。

## <a name="publish-a-message"></a>發行訊息

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)消息是字串。 發佈者會通知訂閱者具有其中一個[`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*)重載的消息。 下列程式碼範例會發行 `Hi` 訊息：

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

在此範例中,[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法指定表示傳送者的泛型參數。 若要接收訊息，訂閱者也必須指定相同的泛型引數，表示它們正在接聽來自該傳送者的訊息。 此外，此範例會指定兩個方法引數：

- 第一個引數會指定傳送者執行個體。
- 第二個引數會指定訊息。

承載資料也可以與訊息一起傳送：

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

在此範例中,[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)該方法指定兩個泛型參數。 第一個是傳送訊息的類型，而第二個是要傳送的承載資料類型。 若要接收訊息，訂閱者也必須指定相同的泛型引數。 這會啟用多則訊息，這類訊息會共用一個訊息識別碼，但傳送不同的承載資料類型以供不同的訂閱者接收。 此外，此範例會指定第三個方法引數，表示要傳送給訂閱者的承載資料。 在此案例中，承載資料為 `string`。

該方法[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)將使用"觸發和忘記"方法發佈消息和任何有效負載數據。 因此，即使沒有任何訂閱者已註冊要接收訊息，也會傳送訊息。 在此情況下，會忽略已傳送的訊息。

## <a name="subscribe-to-a-message"></a>訂閱訊息

訂閱者可以註冊以使用其中一個[`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*)重載接收消息。 下列程式碼範例示範此作法的範例：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

此方法[`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*)會`this`用於`Hi``MainPage`傳送的訊息,並執行回檔委託以回應接收訊息。 指定為 Lambda 運算式的回呼委派可以是更新 UI、儲存一些資料或觸發一些其他作業的程式碼。

> [!NOTE]
> 訂閱者可能不需要處理已發佈消息的每個實例,並且這可以通過[`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*)在方法上指定的泛型類型參數進行控制。

下列範例示範如何訂閱包含承載資料的訊息：

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

[`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*)這個方法訂閱`Hi`由`MainPage`類型傳送的訊息,有效負載資料為 。 `string` 回呼委派會執行以回應接收這類訊息，其會在警示中顯示承載資料。

> [!IMPORTANT]
> `Subscribe`方法執行的委託將在使用`Send`方法發佈消息的同一線程上執行。

## <a name="unsubscribe-from-a-message"></a>從訊息中取消訂閱

若訂閱者不想再接收訊息，則應從訊息中取消訂閱。 這是通過過載實現: [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

在此範例中,[`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)方法`this``Hi``MainPage`從 類型發送的消息中取消訂閱物件。

包含有效負載資料的消息應取消訂閱,使用指定兩[`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)個泛型參數的重載:

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

[`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)這個選項`this`, 方法`Hi``MainPage`從型態傳送的訊息中取消訂閱物件,有效載入資料為`string`。

## <a name="related-links"></a>相關連結

- [MessagingCenterSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

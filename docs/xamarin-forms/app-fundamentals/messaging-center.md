---
title: Xamarin.Forms MessagingCenter
description: Xamarin.Forms MessagingCenter 類別會實作發行-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2019
ms.openlocfilehash: a4d246419c7449c2395759cf5a8b04469e7a2309
ms.sourcegitcommit: 266e75fa6893d3732e4e2c0c8e79c62be2804468
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68821002"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

發行-訂閱模式是一種訊息模式，發行者可以在不知道任何接收者 (稱為訂閱者) 的情況下傳送訊息。 同樣地，訂閱者可以在不知道任何發行者的情況下接聽特定訊息。

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會實作發行-訂閱模式，允許在不方便透過物件和類型參考連結的元件之間進行以訊息為基礎的通訊。 此機制讓發行者和訂閱者不需彼此參考就能進行通訊，有助於減少兩者之間的相依性。

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別提供多點傳送的發行-訂閱功能。 這表示可以有多個發行單一訊息的發行者，而且可以有多個接聽相同訊息的訂閱者：

![](messaging-center-images/messaging-center.png "多點傳送的發行-訂閱功能")

發行者會使用 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法來傳送訊息，而訂閱者會使用 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法來接聽訊息。 此外，訂閱者也可以在必要時，使用 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法，取消訂閱訊息訂閱。

> [!IMPORTANT]
> 在內部，[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別會使用弱式參考。 這表示它將不會讓物件持續運作，並將允許它們進行記憶體回收。 因此，應該只有當類別不再希望收到訊息時，才需要取消訂閱訊息。

## <a name="publish-a-message"></a>發行訊息

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 訊息為字串。 發行者會使用其中一個 [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 多載來通知訊息的訂閱者。 下列程式碼範例會發行 `Hi` 訊息：

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

在此範例中，[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法會指定代表傳送者的泛型引數。 若要接收訊息，訂閱者也必須指定相同的泛型引數，表示它們正在接聽來自該傳送者的訊息。 此外，此範例會指定兩個方法引數：

- 第一個引數會指定傳送者執行個體。
- 第二個引數會指定訊息。

承載資料也可以與訊息一起傳送：

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

在此範例中，[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法會指定兩個泛型引數。 第一個是傳送訊息的類型，而第二個是要傳送的承載資料類型。 若要接收訊息，訂閱者也必須指定相同的泛型引數。 這會啟用多則訊息，這類訊息會共用一個訊息識別碼，但傳送不同的承載資料類型以供不同的訂閱者接收。 此外，此範例會指定第三個方法引數，表示要傳送給訂閱者的承載資料。 在此案例中，承載資料為 `string`。

[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 方法將使用射後不理方法來發行訊息和任何承載資料。 因此，即使沒有任何訂閱者已註冊要接收訊息，也會傳送訊息。 在此情況下，會忽略已傳送的訊息。

## <a name="subscribe-to-a-message"></a>訂閱訊息

訂閱者可以註冊，以使用其中一個 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 多載來接收訊息。 下列程式碼範例示範此作法的範例：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

在此範例中，[`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法會將 `this` 物件訂閱至 `MainPage` 類型所傳送的 `Hi` 訊息，並執行回呼委派以回應接收訊息。 指定為 Lambda 運算式的回呼委派可以是更新 UI、儲存一些資料或觸發一些其他作業的程式碼。

> [!NOTE]
> 訂閱者可能不需要處理已發行訊息的每個執行個體，而且這可由 [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法中指定的泛型類型引數來控制。

下列範例示範如何訂閱包含承載資料的訊息：

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

在此範例中，[`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) 方法會訂閱 `MainPage` 類型所傳送的 `Hi` 訊息，其承載資料為 `string`。 回呼委派會執行以回應接收這類訊息，其會在警示中顯示承載資料。

## <a name="unsubscribe-from-a-message"></a>從訊息中取消訂閱

若訂閱者不想再接收訊息，則應從訊息中取消訂閱。 這會使用其中一個 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 多載來達成：

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

在此範例中，[`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法會從 `MainPage` 類型所傳送的 `Hi` 訊息中取消訂閱 `this` 物件。

包含承載資料的訊息應使用指定兩個泛型引數的 [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 多載來取消訂閱：

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

在此範例中，[`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 方法會從 `MainPage` 類型所傳送的 `Hi` 訊息中取消訂閱 `this` 物件，其承載資料為 `string`。

## <a name="related-links"></a>相關連結

- [MessagingCenterSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

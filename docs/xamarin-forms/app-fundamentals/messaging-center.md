---
title: Xamarin.Forms MessagingCenter
description: 本文說明如何使用 Xamarin.Forms MessagingCenter 傳送和接收訊息，減低檢視模型等類別之間的結合程度。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 6af392988b8ffa5f01ef07f81be53ea8f82e8fb5
ms.sourcegitcommit: 086edd9c44dfc0e77412e1ed5eda7318bbd1ce7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58477326"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/UsingMessagingCenter)

_Xamarin.Forms 包含簡易傳訊服務，可傳送與接收訊息。_

<a name="Overview" />

## <a name="overview"></a>總覽

除了簡易訊息合約，Xamarin.Forms `MessagingCenter` 也可讓檢視模型和其他元件在不知道彼此的情況下互相通訊。

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>MessagingCenter 的運作方式

`MessagingCenter` 分成兩部分：

-  **Subscribe** - 接聽具有特定簽章的訊息，並在收到訊息後執行某些動作。 多位訂閱者可以接聽相同的訊息。
-  **Send** - 發佈訊息讓接聽者採取動作。 若沒有任何接聽者訂閱訊息，則會忽略該訊息。

`MessagingCenter` 是靜態類別，具有 `Subscribe` 和 `Send` 方法，他們會用於整個解決方案。

訊息有 `message` 參數字串，用來為訊息「定址」。 `Subscribe` 與 `Send` 方法會使用泛型參數進一步控制訊息的傳遞方式，`message` 文字相同但泛型型別引數不同的兩則訊息並不會傳遞給同一位訂閱者。

`MessagingCenter` 的 API 很簡單：

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

以下將說明這些方法。

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>使用 MessagingCenter

傳送訊息的原因可能是使用者互動 (例如按一下按鈕)、系統事件 (例如變更狀態的控制項) 或一些其他事件 (例如非同步下載完成)。 訂閱者可能會接聽，以變更使用者介面的外觀、儲存資料或觸發一些其他作業。

如需如何使用 `MessagingCenter` 類別的詳細資訊，請參閱 [Communicating Between Loosely Coupled Components](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md) (在鬆散結合的元件間通訊)。

### <a name="simple-string-message"></a>簡易字串訊息

最簡易的訊息就是只在 `message` 參數中包含字串。 「接聽」簡易字串訊息的 `Subscribe` 方法如下所示，請注意指定傳送者的泛型型別需為 `MainPage` 類別。 解決方案中的所有類別都可使用此語法訂閱訊息：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

在 `MainPage` 類別中，下列程式碼會「傳送」訊息。 `this` 參數是 `MainPage` 的執行個體。

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

字串不會變更，其表示「訊息類別」，並用來決定要通知的訂閱者。 這類訊息用來指出發生了某些事件，例如「已完成上傳」，而不需深入資訊。

### <a name="passing-an-argument"></a>傳遞引數

若要在訊息中傳遞引數，請指定 `Subscribe` 泛型引數和動作簽章中的引數類型。

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

若要傳送具有引數的訊息，請在 `Send` 方法呼叫中，包含類型泛型參數與引數值。

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

這個簡易範例使用 `string` 引數，不過可傳遞所有 C# 物件。

### <a name="unsubscribe"></a>取消訂閱

物件可從訊息簽章取消訂閱，往後便不會再傳遞任何訊息。 `Unsubscribe` 方法語法應反映訊息簽章 (因此可能需要包含訊息引數的泛型型別參數)。

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>總結

MessagingCenter 是降低結合程度 (特別是檢視模型之間) 的簡易方式。 它可以用來傳送和接受簡易訊息，或在類別之間傳遞引數。 若是不想再接受訊息，類別應從該訊息取消訂閱。


## <a name="related-links"></a>相關連結

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)
- [在相依性低的元件之間通訊](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)

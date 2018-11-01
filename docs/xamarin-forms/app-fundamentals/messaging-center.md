---
title: Xamarin.Forms MessagingCenter
description: 這篇文章說明如何使用 Xamarin.Forms MessagingCenter 傳送和接收訊息，因此減少類別，例如檢視模型之間的結合。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 7fef4443cacba0fa8bdb8d5df070c4244730b4f5
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675168"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

_Xamarin.Forms 包含簡單的傳訊服務，可傳送和接收訊息。_

<a name="Overview" />

## <a name="overview"></a>總覽

Xamarin.Forms`MessagingCenter`可讓檢視模型和其他元件通訊，而不需要知道彼此資訊的任何項目，除了簡單的訊息合約。

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>MessagingCenter 的運作方式

有兩個部分`MessagingCenter`:

-  **訂閱**-接聽使用特定簽章的訊息，並接收它們時執行某些動作。 多個訂閱者可以接聽相同的訊息。
-  **傳送**-發佈的訊息採取動作的接聽程式。 如果沒有接聽程式已訂閱會忽略該訊息。


`MessagingService`是靜態類別，具有`Subscribe`和`Send`用於整個解決方案的方法。

訊息有一個字串`message`做為方法的參數*地址*訊息。 `Subscribe`並`Send`方法會使用泛用參數來進一步控制訊息的傳遞方式-兩個訊息具有相同`message`文字，但不同的泛型類型引數將不會傳遞至相同的訂閱者 」。

適用於 API`MessagingCenter`很簡單：

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

以下將說明這些方法。

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>使用 MessagingCenter

因為使用者互動 （例如按一下按鈕）、 系統事件 （例如變更狀態的控制項） 或其他事件 （例如進行非同步下載完成），可能會傳送訊息。 訂閱者可能會接聽變更的使用者介面的外觀、 將資料儲存或觸發程序的其他作業。

### <a name="simple-string-message"></a>簡單的字串訊息

最簡單的訊息中包含的字串中`message`參數。 A`Subscribe`方法所*接聽*的簡單字串訊息如下所示-請注意指定寄件者必須是型別的泛型型別`MainPage`。 使用此語法的訊息可以訂閱方案中的所有類別：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

在 `MainPage`類別的下列程式碼*傳送*訊息。 `this`參數是的執行個體`MainPage`。

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

字串不會變更-它會指出*訊息類型*並用它來判斷通知訂閱者。 這類的訊息用來表示，某些事件發生，例如 「 已完成上傳，」 需要任何進一步資訊的位置。

### <a name="passing-an-argument"></a>傳遞引數

若要傳遞的引數，並出現訊息，指定的引數型別中`Subscribe`泛型引數和動作簽章中。

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

若要傳送的訊息引數，包含型別泛型參數和中的引數的值`Send`方法呼叫。

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

這個簡單的範例會使用`string`引數，但任何C#物件可以傳遞。

### <a name="unsubscribe"></a>取消訂閱

物件可以取消訂閱的訊息簽章的任何未來的訊息會傳遞。 `Unsubscribe`方法語法應該要反映訊息的簽章 （因此可能需要包含訊息引數的泛型型別參數）。

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>總結

MessagingCenter 是簡單的方式來降低結合程度，特別是之間檢視模型。 它可用來傳送和接收訊息的簡單或類別之間傳遞引數。 他們不想要接收的訊息應該取消訂閱類別。


## <a name="related-links"></a>相關連結

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)

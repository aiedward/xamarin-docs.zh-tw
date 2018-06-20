---
title: Xamarin.Forms MessagingCenter
description: 本文說明如何使用 Xamarin.Forms MessagingCenter 傳送和接收訊息，以減少結合，例如檢視模型類別之間。
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 49a7ecdad53c7820594f7ebc047ae6fbc5a9bc56
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209410"
---
# <a name="xamarinforms-messagingcenter"></a>Xamarin.Forms MessagingCenter

_Xamarin.Forms 包含簡單的傳訊服務，可傳送和接收訊息。_

<a name="Overview" />

## <a name="overview"></a>總覽

Xamarin.Forms`MessagingCenter`啟用檢視模型和其他元件，以與通訊，而不需要知道任何關於彼此除了簡單的訊息合約。

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>MessagingCenter 的運作方式

有兩個部分`MessagingCenter`:

-  **訂閱**-接聽使用特定簽章的訊息並接收它們時執行某些動作。 多個訂閱者可以接聽相同的訊息。
-  **傳送**-發行要作用的接聽程式的訊息。 如果沒有接聽程式已訂閱會忽略該訊息。


`MessagingService`是靜態類別與`Subscribe`和`Send`用於整個解決方案的方法。

訊息具有字串`message`用做為方法參數*位址*訊息。 `Subscribe`和`Send`方法使用泛型參數，來進一步控制傳遞訊息的方式-兩個訊息具有相同`message`文字，但不同的泛型型別引數將不會傳遞至相同的訂閱者 」。

API`MessagingCenter`很簡單：

-  訂閱&lt;否則 > (物件訂閱者 」、 「 字串訊息動作&lt;否則 > 回呼，否則來源 = null)
-  訂閱&lt;，TArgs > (物件訂閱者 」、 「 字串訊息動作&lt;，TArgs > 回呼，否則來源 = null)
-  傳送&lt;否則 > （否則寄件者，字串訊息）
-  傳送&lt;，TArgs > （否則寄件者，字串訊息 TArgs 引數）
-  取消訂閱&lt;，TArgs > （物件訂閱者、 字串訊息）
-  取消訂閱&lt;否則 > （物件訂閱者、 字串訊息）


以下將說明這些方法。

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>使用 MessagingCenter

因為使用者互動 （例如按一下按鈕）、 系統事件 （例如變更狀態的控制項） 或一些其他事件 （例如進行非同步下載完成），可能會傳送訊息。 「 訂閱者 」 可能會接聽變更的使用者介面的外觀、 將資料儲存或觸發其他作業。

### <a name="simple-string-message"></a>簡單的字串訊息

最簡單的訊息包含的字串中`message`參數。 A`Subscribe`方法，*接聽*的簡單字串訊息如下所示-請注意指定寄件者必須是類型的泛型型別`MainPage`。 使用此語法的訊息可以訂閱方案中的任何類別：

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

在`MainPage`類別下列程式碼*傳送*訊息。 `this`參數是的執行個體`MainPage`。

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

字串不會變更-它會指出*訊息類型*並用它來判斷哪些通知的訂閱者。 這類的訊息用來指示的某些事件發生，例如 「 已完成上傳，「 需要的進一步資訊的地方。

### <a name="passing-an-argument"></a>傳遞引數

若要傳送訊息之引數，指定的引數型別中`Subscribe`泛型引數和動作的簽章中。

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

若要傳送訊息的引數，包含泛型型別參數和引數中的值`Send`方法呼叫。

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

這個簡單的範例會使用`string`傳遞引數，但任何 C# 物件。

### <a name="unsubscribe"></a>取消訂閱

物件可以取消訂閱的訊息簽章，讓任何未來的訊息傳遞。 `Unsubscribe`方法語法，應該要反映訊息的簽章 （因此可能需要包含訊息的引數的泛型類型參數）。

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>總結

MessagingCenter 是簡單的方式來降低，特別是啟用之間的結合檢視模型。 它可用來傳送和接收訊息的簡單或類別之間傳遞引數。 它們不想要接收的訊息應該取消訂閱類別。


## <a name="related-links"></a>相關連結

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)

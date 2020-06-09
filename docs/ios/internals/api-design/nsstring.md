---
title: 在 NSString 中的 iOS 和 Xamarin. Mac
description: '本檔描述 Xamarin 如何在不發生這種情況時，以透明方式將 NSString 物件轉換成 c # 字串物件。'
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 314c94fc9208a63e2f9305511df262327df921a5
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84565066"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>在 NSString 中的 iOS 和 Xamarin. Mac

Xamarin 和 Xamarin 的設計會呼叫使用 API 來公開原生 .NET 字串類型（ `string` ），以便在 c # 和其他 .net 程式設計語言中進行字串操作，並將字串公開為 API 所公開的資料類型，而不是  `NSString`   資料類型。

這表示開發人員不應該在特殊類型（）中保留用來呼叫 Xamarin. iOS & Xamarin. Mac API （）的字串 `Foundation.NSString` ，它們可以繼續使用 Mono `System.String` 來進行所有作業，而當 Xamarin 或 xamarin 中的 API 需要字串時，我們的 api 系結會負責封送處理資訊。

例如，類型之的目標-C "text" 屬性 `UILabel` `NSString` 會宣告如下：

```objc
@property(nonatomic, copy) NSString *text
```

這會在 Xamarin 中公開為：

```csharp
class UILabel {
    public string Text { get; set; }
}
```

在幕後，此屬性的執行會將 c # 字串封送處理成 `NSString` ，並 `objc_msgSend` 以目標 C 的相同方式呼叫方法。

有一些協力廠商的目標 C Api 不會取用 `NSString` ，而是使用 C 字串（"*char*"）。 在這些情況下，您仍然可以使用 c # 字串資料類型，但您必須使用[[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md)屬性來通知系結產生器，此字串不應封送處理為 `NSString` ，而是以 C 字串的形式。

 <a name="Exceptions_to_the_Rule"></a>

## <a name="exceptions-to-the-rule"></a>規則的例外狀況

在 Xamarin. iOS 和 Xamarin 中，我們對此規則做了例外狀況。 當我們公開時  `string` ，以及當我們進行 except 和公開時  `NSString` ，會在  `NSString`   方法執行指標比較而非內容比較時做出決定。

當目標-C Api 使用公用  `NSString`   常數做為代表某個動作的權杖，而不是比較字串的實際內容時，就會發生這種情況。

在這些情況下，會 `NSString`   公開 api，而且有少數的 api 具有此功能。 您也會注意到，NSString 屬性會在某些類別中公開。 這些 `NSString` 屬性會公開給通知之類的專案。 這些屬性通常如下所示：

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

通知是 `NSNotification` 當您想要註冊執行時間所廣播的特定事件時，用於類別的索引鍵。

金鑰通常看起來像這樣：

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

在 API 中公開的另一個位置， `NSString` 就像是在 iOS 或 OS X 中作為參數使用的權杖，這些 api 會採用 `NSDictionary` 物件做為參數。 字典通常會包含索引 `NSString` 鍵。 依照慣例，您 `NSString` 可以藉由新增「金鑰」名稱來命名這些靜態屬性。

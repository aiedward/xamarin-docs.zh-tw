---
title: 在 NSString 中的 iOS 和 Xamarin. Mac
description: 本檔描述 Xamarin 如何在不發生這種情況時C# ，以透明的方式將 NSString 物件轉換成字串物件。
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022353"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>在 NSString 中的 iOS 和 Xamarin. Mac

Xamarin 和 Xamarin 的設計會呼叫使用 API 來公開原生 .NET 字串類型，`string`，用於和其他 .NET 程式設計語言中C#的字串操作，並將字串公開為 API 所公開的資料類型，而不是 `NSString` 資料類型。

這表示開發人員不應該在特殊類型（`Foundation.NSString`）中保留要用來呼叫 Xamarin. iOS & Xamarin. Mac API （統一）的字串，它們可以繼續針對所有作業使用 Mono 的 `System.String`，而當 Xamarin 或 Xamarin 中的 API 需要字串時，我們的 API 系結會負責封送處理資訊。

例如，`NSString`類型的 `UILabel` 上的目標-C "text" 屬性會宣告如下：

```objc
@property(nonatomic, copy) NSString *text
```

這會在 Xamarin 中公開為：

```csharp
class UILabel {
    public string Text { get; set; }
}
```

在幕後，此屬性的執行會將C#字串封送處理為 `NSString`，並以目標 C 的相同方式呼叫 `objc_msgSend` 方法。

有幾個協力廠商的目標-C Api 不會耗用 `NSString`，而是使用 C 字串（"*char*"）。 在這些情況下，您仍然可以使用C# string 資料類型，但您必須使用[[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md)屬性來通知系結產生器，此字串不應封送處理為 `NSString`，而是以 C 字串的形式。

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>規則的例外狀況

在 Xamarin. iOS 和 Xamarin 中，我們對此規則做了例外狀況。 當我們公開 `string`s 的時機，以及當我們進行 except 和公開 `NSString`s 時，如果 `NSString` 方法可能會執行指標比較而不是內容比較，則會進行這項決定。

當目標-C Api 使用公用 `NSString` 常數做為代表某個動作的權杖，而不是比較字串的實際內容時，就可能發生這種情況。

在這些情況下，`NSString` Api 會公開，而且有少數的 Api 具有此功能。 您也會注意到，NSString 屬性會在某些類別中公開。 這些 `NSString` 屬性會公開給通知之類的專案。 這些屬性通常如下所示：

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

通知是當您想要註冊執行時間所廣播的特定事件時，用於 `NSNotification` 類別的索引鍵。

金鑰通常看起來像這樣：

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

在 API 中公開 `NSString`的另一個位置，就像是用來做為 iOS 或 OS X 中某些 Api 參數的權杖，而這些應用程式採用 `NSDictionary` 物件做為參數。 字典通常包含 `NSString` 索引鍵。 依照慣例，會藉由新增「索引鍵」名稱來命名這些靜態 `NSString` 屬性。

---
title: 在 NSString 中的 iOS 和 Xamarin. Mac
description: 本檔描述 Xamarin 如何在不發生這種情況時C# , 以透明的方式將 NSString 物件轉換成字串物件。
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 589b584e0526ffdc56dd5ec26aa25a0b61e2141a
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889894"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>在 NSString 中的 iOS 和 Xamarin. Mac

Xamarin. iOS 和 xamarin 的設計都會呼叫 use API 來公開原生 .net 字串型別, `string`以及和其他 .net 程式設計語言中C#的字串操作, 並將字串公開為 API 所公開的資料型別, 而不是 `NSString`資料類型 。

這表示開發人員應該不需要在特殊類型 (`Foundation.NSString`) 中保留要用來呼叫 xamarin. iOS & xamarin. Mac API (整合) 的字串, 它們可以繼續使用 Mono 的`System.String`來進行所有作業, 以及每次在 Xamarin 或 Xamarin 中的 API 需要字串, 我們的 API 系結會負責封送處理資訊。

例如, 類型`UILabel` `NSString`之的目標-C "text" 屬性會宣告如下:

```objc
@property(nonatomic, copy) NSString *text
```

這會在 Xamarin 中公開為:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

在幕後, 此屬性的執行會將C#字串封送處理成`NSString` , 並以目標 C 的相同方式呼叫`objc_msgSend`方法。

有一些協力廠商的目標 C api 不`NSString`會取用, 而是使用 C 字串 ("*char*")。 在這些情況下, 您仍然可以使用C# string 資料類型, 但您必須使用[[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md)屬性來通知系結產生`NSString`器, 此字串不應封送處理為, 而是以 C 字串來表示。

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>規則的例外狀況

在 Xamarin. iOS 和 Xamarin 中, 我們對此規則做了例外狀況。 當我們公開 `string`時, 以及當我們進行 except 和公開 `NSString`時 `NSString`  , 會在方法執行指標比較而非內容比較時做出決定。

當目標-C api 使用公用 `NSString`  常數做為代表某個動作的權杖, 而不是比較字串的實際內容時, 就會發生這種情況。

在這些情況下`NSString`,  會公開 api, 而且有少數的 api 具有此功能。 您也會注意到, NSString 屬性會在某些類別中公開。 這些`NSString`屬性會公開給通知之類的專案。 這些屬性通常如下所示:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

通知是當您想要註冊運行`NSNotification`時間所廣播的特定事件時, 用於類別的索引鍵。

金鑰通常看起來像這樣:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

在 API 中`NSString`公開的另一個位置, 就像是在 iOS 或 OS X 中作為參數使用的權杖, 這些 api 會`NSDictionary`採用物件做為參數。 字典通常會包含`NSString`索引鍵。 依照慣例, 您可以藉由新增「金鑰`NSString` 」名稱來命名這些靜態屬性。

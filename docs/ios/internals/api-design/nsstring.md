---
title: 在 Xamarin.iOS 和 Xamarin.Mac NSString
description: 本文件說明如何 Xamarin.iOS 無障礙地轉換到 NSString 物件C#時不是這樣的字串物件。
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: cc9e3f992642f3cdc3d16fe6f829b6a6c06b50fc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115027"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>在 Xamarin.iOS 和 Xamarin.Mac NSString

Xamarin.iOS 和 Xamarin.Mac 的設計呼叫會使用 API 來公開原生.NET 字串型別的`string`，用於字串操作中C#與其他.NET 程式設計語言，並公開 （expose） 為而不是 API 所公開的資料類型的字串 `NSString` 資料型別。

這表示一種特殊類型中的 開發人員應該不需要保留要用於呼叫 Xamarin.iOS 和 Xamarin.Mac API （整合） 的字串 (`Foundation.NSString`)，他們可以繼續使用 Mono`System.String`所有作業，以及每當Xamarin.iOS 或 Xamarin.Mac 中的 API 需要字串時，我們的 API 繫結處理的封送處理資訊。

比方說，OBJECTIVE-C"text"上的屬性`UILabel`型別的`NSString`，宣告如下：

```objc
@property(nonatomic, copy) NSString *text
```

這會顯示在 Xamarin.iOS 為︰

```csharp
class UILabel {
    public string Text { get; set; }
}
```

在幕後，此屬性的實作封送處理C#字串`NSString`並呼叫`objc_msgSend`中的相同方式來 OBJECTIVE-C 方法。

有少數幾個第三方 Objective C Api，並不會耗用`NSString`，但改為使用 C 字串 ("*char*")。 在這些情況下，您仍然可以使用C#的字串資料類型，但您必須使用[[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md)通知此字串應該不會封送處理為繫結產生器的屬性`NSString`，而是以 C 字串。

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>規則的例外狀況

在 Xamarin.iOS 和 Xamarin.Mac 中，我們已進行這項規則的例外狀況。 之間時，我們會公開 `string`s，和當我們做出 except 和公開 `NSString`s，如果進行 `NSString` 方法可能會進行指標比較，而不是內容的比較。

這可能會發生時的 Objective C Api 會使用公用 `NSString` 常數做為表示某些動作，而不是比較字串的實際內容語彙基元。

在這些情況下， `NSString` 公開 Api，還有少數的 Api，此行為。 此外，您也會注意到 NSString 屬性會公開在一些類別。 這些`NSString`為通知等項目所公開的屬性。 這些是屬性通常看起來像這樣：

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```
通知是金鑰，用於`NSNotification`類別，在您想要針對特定事件，廣播由執行階段註冊。

金鑰通常看起來像這樣：

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

另一個位置所在`NSString`s 會公開在 API 是以權杖做為參數來在 iOS 中的某些 Api 或 OS X`NSDictionary`物件做為參數。 字典通常包含`NSString`索引鍵。 Xamarin.iOS，依照慣例，名稱的靜態`NSString`藉由新增 「 金鑰 」 名稱的屬性。

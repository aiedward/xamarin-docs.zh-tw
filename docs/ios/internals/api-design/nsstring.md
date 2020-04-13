---
title: Xamarin.iOS 和 Xamarin.Mac 中的 NSString
description: 本文件介紹 Xamarin.iOS 如何透明地將 NSString 物件轉換為 C# 字串物件,當這種情況不會發生時。
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022353"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>Xamarin.iOS 和 Xamarin.Mac 中的 NSString

Xamarin.iOS 和 Xamarin.Mac 的設計都要求使用 API 公開本機 .NET 字串類型,`string`在 C# 和其他 .NET 程式設計語言中進行字串操作,並將字 `NSString` 串公開為 API 公開的資料類型而不是資料類型。

這意味著開發人員不必將用於調用 Xamarin.iOS & Xamarin.Mac API(統一)的字串保留為特殊類型`Foundation.NSString`(), 他們可以在所有操作中繼續`System.String`使用 Mono, 並且每當 Xamarin.iOS 或 Xamarin.Mac 中的 API 需要字串時,我們的 API 綁定負責對資訊進行封送。

例如,在類型`UILabel``NSString`上的目標 C"文本"屬性聲明為:如下所示:

```objc
@property(nonatomic, copy) NSString *text
```

這在 Xamarin.iOS 中公開如下:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

在後臺,此屬性的實現將 C# 字串封送到`NSString`a`objc_msgSend`中, 並且以與 Objective-C 相同的方式調用方法。

有少數第三方 Objective-C API`NSString`不消耗 , 而是使用 C 字串*char*("char")。 在這些情況下,您仍可以使用 C# 字串資料類型,但必須使用[[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md)屬性來通知綁定產生器,此字串不應`NSString`作為封送為 ,而是作為 C 字串。

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>規則的例外情況

在 Xamarin.iOS 和 Xamarin.Mac 中,我們對此規則都做出了例外。 如果 `string` 方法可以執行指標比較而不是內容比較,則在公開 s `NSString`和"公開"之間做出決策 `NSString`。

當 Objective-C API `NSString` 使用公共 常量作為表示某些操作的權杖,而不是比較字串的實際內容時,可能會發生這種情況。

在這些情況下,API`NSString` 會公開,並且有少數 API 具有此。 您還會注意到 NSString 屬性在某些類中公開。 這些`NSString`屬性將公開用於通知等項。 這些屬性通常如下所示:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

通知是當您要註冊運行時廣播的特定`NSNotification`事件時,用於類的鍵。

鍵通常如下所示:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

在 API`NSString`中公開的另一個位置是作為權杖,用作 iOS 或 OS X`NSDictionary`中某些 API 的參數 ,這些 API 將物件視為參數。 字典通常包含`NSString`鍵。 按照慣例,Xamarin.iOS 透過添加「Key」名稱來命名這些靜`NSString`態 屬性。

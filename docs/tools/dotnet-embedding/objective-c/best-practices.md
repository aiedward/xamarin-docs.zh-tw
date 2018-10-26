---
title: .NET 內嵌適用於 OBJECTIVE-C 的最佳作法
description: 這份文件描述使用.NET 內嵌 OBJECTIVE-C 的各種最佳做法 它討論公開 managed 程式碼的子集、 公開 chunkier API、 命名和更多功能。
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 33138b7858b8bc04a5be30f9fad1709e916f5575
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105388"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>.NET 內嵌適用於 OBJECTIVE-C 的最佳作法

這是草稿，可能不在同步功能目前支援工具。 我們希望這份文件會個別發展並最後比對的最後一個工具，也就是我們會建議在長期最佳方法不直接的因應措施。

本文件的大型部分也適用於其他支援的語言。 不過所有提供的範例是在C#和 OBJECTIVE-C

## <a name="exposing-a-subset-of-the-managed-code"></a>公開 managed 程式碼的子集

產生原生程式庫/架構包含呼叫每個受管理的 Api 所公開的 OBJECTIVE-C 程式碼。 您呈現多個 API （公開） 然後大原生_黏附_將成為程式庫。

它可能是個不錯的主意，若要建立不同且較小組件，以公開 （expose） 的必要的 Api 的原生程式開發人員。 該外觀也可讓您更充分掌控的可見度、 命名、 錯誤...檢查產生的程式碼。

## <a name="exposing-a-chunkier-api"></a>公開 chunkier API

沒有從機器碼轉換支付的價格為 managed （與上一步）。 因此，最好是公開_區塊，而不是多對話_Api 來原生開發人員，例如

**多對話**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**區塊**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

因為轉換數目較小的效能會更好。 它也需要較少的程式碼產生，因此這會產生較小原生程式庫也。

## <a name="naming"></a>命名

命名是兩個最困難的問題之一在電腦科學中，其他的快取無效判定 」 和 「 關閉 x 1 的錯誤。 希望.NET 內嵌可以免除您以外的所有命名。

### <a name="types"></a>類型

Objective C 不支援命名空間。 一般情況下，其型別前面會加上 2 （適用於 Apple) 或 3 （適用於第 3 個合作對象） 字元的前置詞，例如`UIView`UIKit 的檢視，其代表此架構。

.NET 類型略過命名空間不可能因為它可能會重複，或令人混淆，名稱。 這可讓現有的.NET 類型很長例如

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

會使用類似：

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

不過您可以重新公開的類型為：

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

讓更多的 Objective C 方便使用，例如：

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>方法

更好的.NET 名稱可能不適合 Objective C API。

在 OBJECTIVE-C 中的命名慣例的不同.NET （而不是依照 pascal 命名法大小寫，更詳細的駝峰式大小寫）。
請閱讀[程式碼撰寫方針的 Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF)。

從 OBJECTIVE-C 開發人員的觀點來看，使用的方法`Get`前置詞表示您並未擁有此執行個體，亦即[取得規則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1)。

此命名的規則在.NET GC 世界裡，有沒有相符項目使用的.NET 方法`Create`前置詞在.NET 中的行為相同。 然而，適用於 OBJECTIVE-C 開發人員，這通常表示您擁有傳回的執行個體，亦即[建立規則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

## <a name="exceptions"></a>例外狀況

是相當常見，在.NET 中使用廣泛用來報告錯誤的例外狀況。 不過，它們是速度慢且不在 OBJECTIVE-C 中完全相同 可能的話您應該將它們隱藏 OBJECTIVE-C 開發人員。

例如，.NET`Try`模式則很容易從 OBJECTIVE-C 程式碼使用：

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

與

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>內部的例外狀況 `init*`

在.NET 中的建構函式必須成功並傳回 (_希望_) 有效的執行個體或擲回例外狀況。

相較之下，可讓 OBJECTIVE-C`init*`傳回`nil`時無法建立執行個體。 這是常見的但不是針對一般，模式，用於許多 Apple 的架構。 在某些情況下`assert`可以發生 （和終止目前的處理序）。

產生器，請遵循相同`return nil`模式產生`init*`方法。 如果擲回 managed 例外狀況，則會列印 (使用`NSLog`) 和`nil`將傳回給呼叫者。

## <a name="operators"></a>運算子

Objective C 不允許運算子多載做為C#可行，因此這些會轉換成類別選取器。

[「 易記 」](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)具名的方法產生而非運算子多載時找不到，而且可以產生以更容易取用的 API。

覆寫這些運算子的類別`==`和/或`!=`應該覆寫標準 Equals (Object) 方法。

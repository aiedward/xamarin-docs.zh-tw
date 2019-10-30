---
title: 適用于目標的 .NET 內嵌最佳做法-C
description: 本檔說明使用 .NET 內嵌與目標-C 的各種最佳作法。 其中討論公開 managed 程式碼的子集、公開 chunkier API、命名等等。
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 2f632e3218d817aa0162a63ea81c61ca18c52b93
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006790"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>適用于目標的 .NET 內嵌最佳做法-C

這是草稿，而且可能不會與此工具目前支援的功能同步。 我們希望這份檔會分開發展，最後才符合最終的工具，也就是我們建議長期的最佳方法，不是立即的因應措施。

本檔的一大部分也適用于其他支援的語言。 不過，所有提供的範例C#都位於和目標-C 中。

## <a name="exposing-a-subset-of-the-managed-code"></a>公開 managed 程式碼的子集

產生的原生程式庫/架構包含用來呼叫每個公開之 managed Api 的目標 C 程式碼。 您所呈現的 API 越多（設為公用），則原生的_膠水_程式庫會變大。

建立不同、較小的元件，只向原生開發人員公開必要的 Api，可能是個不錯的主意。 該外觀也能讓您更充分掌控可見度、命名、錯誤檢查 ...產生的程式碼。

## <a name="exposing-a-chunkier-api"></a>公開 chunkier API

從原生轉換為受控（及備份）有一種費用。 因此，最好將_大量而非_多對話 api 公開給原生開發人員，例如

**聊天**

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

**大量**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

由於轉換的數目較小，效能會比較好。 這也需要產生較少的程式碼，因此也會產生較小的原生程式庫。

## <a name="naming"></a>命名

命名專案是電腦科學中兩個最困難的問題之一，另一個則是快取失效，而不是1個錯誤。 希望 .NET 內嵌可以保護您的所有，但不會命名。

### <a name="types"></a>型別

目標-C 不支援命名空間。 一般來說，其類型前面會加上2（適用于 Apple）或3（適用于協力廠商）字元前置詞，例如 UIKit 的 View 的 `UIView`，代表架構。

若為 .NET 類型，則無法略過命名空間，因為它可能會引進重複或混亂的名稱。 這會讓現有的 .NET 類型非常長，例如

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

其使用方式如下：

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

不過，您可以將類型重新公開為：

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

讓它更容易使用，例如：

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>方法

即使是良好的 .NET 名稱也可能不適合用于目標-C API。

目標-C 中的命名慣例與 .NET （camel 大小寫，而不是 pascal 大小寫，更詳細）不同。
請閱讀[Cocoa 的程式碼撰寫方針](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF)。

從目標-C 開發人員的觀點來看，具有 `Get` 前置詞的方法表示您沒有擁有實例，也就是[取得規則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1)。

此命名規則在 .NET GC 世界中沒有相符的結果;具有 `Create` 前置詞的 .NET 方法在 .NET 中會有相同的行為。 不過，對於目標-C 開發人員而言，這通常表示您擁有傳回的實例，也就是[建立規則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

## <a name="exceptions"></a>例外狀況

這在 .NET 中相當常見，可廣泛使用例外狀況來報告錯誤。 不過，它們的速度很慢，而且在目標-C 中並不完全相同。 可能的話，您應該從目標-C 開發人員隱藏它們。

例如，.NET `Try` 模式會比從目標 C 程式碼更輕鬆地使用：

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

何時

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>`init*` 內的例外狀況

在 .NET 中，函式必須成功並傳回（_希望_）有效的實例，或擲回例外狀況。

相反地，在無法建立實例時，目標-C 允許 `init*` 傳回 `nil`。 這是許多 Apple 架構中使用的常見（但不是一般）模式。 在某些其他情況下，可能會發生 `assert` （並終止目前的進程）。

產生器會遵循所產生 `init*` 方法的相同 `return nil` 模式。 如果擲回 managed 例外狀況，則會將它列印（使用 `NSLog`），而 `nil` 會傳回給呼叫者。

## <a name="operators"></a>運算子

目標-C 不允許將運算子多載，因為C#它們會轉換成類別選取器。

「[易記](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)」命名的方法會在找到時以喜好設定的方式產生，並可產生更容易取用的 API。

覆寫運算子 `==` 和/或 `!=` 的類別也應該覆寫標準 Equals （Object）方法。

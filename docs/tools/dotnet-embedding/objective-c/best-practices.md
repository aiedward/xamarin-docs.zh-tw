---
title: 適用于目標的 .NET 嵌入最佳作法-C
description: 本檔說明使用 .NET 內嵌與目標 C 的各種最佳做法。 其中討論公開 managed 程式碼的子集、公開 chunkier API、命名等等。
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 7eccc83a28d0bac7b9ff15a46022942c7238c714
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457389"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>適用于目標的 .NET 嵌入最佳作法-C

這是草稿，可能不會與工具目前支援的功能同步。 我們希望這份檔會分開發展，最後符合最終的工具，也就是我們建議長期的最佳方法，而不是立即的因應措施。

本檔中的一大部分也適用于其他支援的語言。 不過，所有提供的範例都是在 c # 和目標 C 中。

## <a name="exposing-a-subset-of-the-managed-code"></a>公開 managed 程式碼的子集

產生的原生程式庫/架構包含目標 C 程式碼，用來呼叫每個公開的 managed Api。 您所呈現的 API 越多 (使公用) 之後，原生的 _膠水_ 程式庫將會變大。

建立不同的較小元件可能是最好的作法，只將必要的 Api 公開給原生開發人員。 該外觀也可讓您更充分掌控可見度、命名、錯誤檢查 .。。產生的程式碼。

## <a name="exposing-a-chunkier-api"></a>公開 chunkier API

要支付從原生轉換為受控 (和回) 的價格。 因此，最好將 _大量（而非_ 多對話 api）公開給原生開發人員，例如

**健談**

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

**矮胖**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

由於轉換的數目較小，因此效能將會更好。 它也需要產生較少的程式碼，因此也會產生較小的原生程式庫。

## <a name="naming"></a>命名

命名專案是電腦科學中最棘手的兩個問題之一，而其他則是快取失效和非1個錯誤。 希望 .NET 內嵌可以保護您的所有，但不像命名一樣。

### <a name="types"></a>類型

目標-C 不支援命名空間。 一般情況下，其類型前面會加上第 2 (的 Apple)  (或協力廠商) 字元前置詞，例如 `UIView` UIKit 的 View （代表架構）。

針對 .NET 類型，略過命名空間是不可能的，因為它可能會導致重複或混淆的名稱。 這使得現有的 .NET 類型非常長，例如

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

的使用方式如下：

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

不過，您可以將類型重新公開為：

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

讓它更便於使用，例如：

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>方法

即使是良好的 .NET 名稱，也可能不適合用于目標 C API。

目標-C 的命名慣例與 .NET (的 camel 大小寫不同，而不是 pascal 大小寫，) 更詳細的資訊。
請閱讀 [Cocoa 的程式碼撰寫方針](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF)。

從目標 C 開發人員的觀點來看，具有前置詞的方法 `Get` 表示您未擁有實例，也就是 [取得規則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1)。

此命名規則在 .NET GC 環境中沒有相符;具有前置詞的 .NET 方法 `Create` 在 .net 中的行為會相同。 不過，針對目標 C 開發人員，通常表示您擁有傳回的實例，也就是 [建立規則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

## <a name="exceptions"></a>例外狀況

在 .NET 中，經常會使用例外狀況來報告錯誤。 不過，它們的速度很慢，而且在目標 C 中並不完全相同。 可能的話，您應該在目標 C 開發人員中隱藏它們。

例如， `Try` 從目標 C 程式碼使用 .net 模式會更容易使用：

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

### <a name="exceptions-inside-init"></a>內的例外狀況 `init*`

在 .NET 中，函式必須成功，並傳回 (_希望_) 有效的實例，或擲回例外狀況。

相反地，在 `init*` `nil` 無法建立實例時，目標 C 允許傳回。 這是許多 Apple 架構中所使用的常見但不是一般的模式。 在某些其他情況下 `assert` ，可能會 (，並終止目前的進程) 。

產生器會針對產生的方法遵循相同的 `return nil` 模式 `init*` 。 如果擲回 managed 例外狀況，則會使用)  (列印出來， `NSLog` 並 `nil` 將其傳回給呼叫者。

## <a name="operators"></a>運算子

目標 C 不允許在 c # 中多載操作員，因此這些運算子會轉換成類別選取器。

在找到時，會以喜好設定的方式將「[易記](/dotnet/standard/design-guidelines/operator-overloads)」命名方法產生給運算子多載，而且可以產生更容易使用的 API。

覆寫運算子 `==` 和/或的類別， `!=` 也應該覆寫標準 Equals (物件) 方法。
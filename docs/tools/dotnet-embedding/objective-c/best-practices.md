---
title: ObjC Embeddinator 4000 最佳作法
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 93dd98dcff772adceb3650ec327cc1a14e4e056b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="embeddinator-4000-best-practices-for-objc"></a>ObjC Embeddinator 4000 最佳作法

這是一個草稿和可能不在同步處理的功能目前支援工具。 我們希望這份文件將會個別發展和最終符合最後一個工具，也就是我們會建議長期最佳方法不直接的因應措施。

大部分的這份文件也適用於其他支援的語言。 不過所有提供的範例是在 C# 和目標。


# <a name="exposing-a-subset-of-the-managed-code"></a>公開 managed 程式碼的子集

產生原生程式庫/架構包含 Objective C 程式碼以呼叫每個受管理的 Api 公開。 您呈現多個 API （設為公用） 再放大原生_黏附_程式庫將會變成。

它可能是不錯的想法，若要建立不同的小型組件，以公開 （expose） 的必要的 Api 原生開發人員。 該外觀也可讓您更充分掌控的可見性，命名時，檢查 產生的程式碼時發生錯誤。


# <a name="exposing-a-chunkier-api"></a>公開 chunkier API

沒有從原生轉換支付的價格為 managed （回復）。 因此，最好是公開_大量而不是多對話_Api 以原生開發人員，例如

**多對話**
```
public class Person {
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

```csharp
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**Chunky**
```
public class Person {
    public Person (string firstName, string lastName) {}
}
```

```csharp
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

因為轉換數目較小者為將會更好的效能。 它還需要較少的程式碼產生，因此這會產生較小原生程式庫以及。


# <a name="naming"></a>命名

命名項目是其中一個最複雜問題在電腦科學中，其他人所快取無效判定 」 和關閉 x 1 的錯誤。 但願.NET 內嵌可以讓您從以外的所有命名。

## <a name="types"></a>類型

Objective C 不支援命名空間。 一般情況下，其型別前面會加上 2 （適用於 Apple) 或 3 （適用於第 3 個合作對象） 字元的前置詞，例如`UIView`UIKit 的檢視，這代表架構。

.NET 類型略過命名空間不可能因為它可能會重複，或會造成混淆，名稱。 這可讓現有的.NET 類型很長，例如：

```csharp
namespace Xamarin.Xml.Configuration {
    public class Reader {}
}
```

會使用類似：

```csharp
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

不過，您可以重新公開做為型別：

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

讓多個 OBJECTIVE-C 易記若要使用，例如：

```csharp
id reader = [[XAMXmlConfigReader alloc] init];
```

## <a name="methods"></a>方法

即使是良好的.NET 名稱可能不適合 OBJECTIVE-C API。

Objective C 中的命名慣例是不同於.NET （而不是依照 pascal 命名法情況下，更詳細的 camel 案例）。
請閱讀[撰寫程式碼的指導方針 Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF)。

Objective C 開發人員的觀點來看，具有的方法從`Get`前置詞表示您並未擁有執行個體，也就是[取得規則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1)。

這個命名規則.NET GC 世界; 中有沒有相符項目使用的.NET 方法`Create`前置詞在.NET 的行為即會相同。 不過，對於 Objective C 的開發人員，通常表示您自己的傳回執行個體，也就是[建立規則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

# <a name="exceptions"></a>例外狀況

在.NET 中使用廣泛地報告錯誤的例外狀況的竟全功 commont 它。 不過，它們是速度慢且不 ObjC 完全相同。 可能的話您應該從 OBJECTIVE-C developer 隱藏它們。

例如，.NET`Try`模式將會更輕鬆地取用來自 Objective C 程式碼：

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

## <a name="exceptions-inside-init"></a>內部例外狀況 `init*`

在.NET 中的建構函式必須是成功，並傳回 (_希望_) 有效的執行個體或擲回例外狀況。

相較之下，可讓 OBJECTIVE-C`init*`傳回`nil`時無法建立執行個體。 這是使用多個 Apple 的架構不常見，但不是一般情況下，模式。 在某些情況下`assert`可以發生 （和終止目前的處理序）。

產生器可遵循相同`return nil`模式產生`init*`方法。 如果擲回 managed 例外狀況，則會列印 (使用`NSLog`) 和`nil`將傳回給呼叫者。

## <a name="operators"></a>運算子

ObjC 不允許運算子多載與 C#，因此這些會轉換為類別選取器。

[「 易記 」](https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx)具名的方法所產生狀況運算子多載時找不到，並且可以產生更輕鬆地取用應用程式開發介面。

類別，覆寫的運算子 = = 和/或 ！ = 應該覆寫標準 Equals (Object) 方法。

---
title: 目標-C 支援
description: 本檔提供在 .NET 內嵌中支援目標-C 的說明。 它會討論自動參考計數、NSString、通訊協定、NSObject 通訊協定、例外狀況等等。
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: c172208b77728423617d17b3f4c5b5a516a0b932
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457350"
---
# <a name="objective-c-support"></a>目標-C 支援

## <a name="specific-features"></a>特殊功能

目標-C 的世代有一些值得注意的特殊功能。

### <a name="automatic-reference-counting"></a>自動參考計數

若要呼叫產生的系結， **必須** 使用自動參考計數 (ARC) 。 使用以 .NET 內嵌為基礎之程式庫的專案，必須使用編譯 `-fobjc-arc` 。

### <a name="nsstring-support"></a>NSString 支援

公開類型的 Api `System.String` 會轉換成 `NSString` 。 這使得記憶體管理比處理時更容易 `char*` 。

### <a name="protocols-support"></a>通訊協定支援

Managed 介面會轉換成目標 C 通訊協定，其中所有成員都是 `@required` 。

### <a name="nsobject-protocol-support"></a>NSObject 通訊協定支援

根據預設，.NET 和目標 C 執行時間的預設雜湊和相等會假設是可互換的，因為它們共用類似的語法。

當 managed 類型覆寫 `Equals(Object)` 或時 `GetHashCode` ，通常表示預設的 ( .net) 行為不足; 這表示預設的目標 C 行為可能還不夠。

在這種情況下，產生器會覆寫 [`isEqual:`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) [`hash`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) 在[ `NSObject` 通訊協定](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)中定義的方法和屬性。 這可讓您以透明的方式從目標 C 程式碼使用自訂受控實作為。

### <a name="exceptions-support"></a>例外狀況支援

傳遞 `--nativeexception` 做為引數， `objcgen` 會將 managed 例外狀況轉換成可攔截和處理的目標 C 例外狀況。 

### <a name="comparison"></a>比較

實 `IComparable` (或其泛型版本) 的 Managed 型別 `IComparable<T>` 會產生可傳回 `NSComparisonResult` 並接受引數的目標 C 好方法 `nil` 。 這可讓所產生的 API 更適合目標 C 開發人員。 例如：

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>類別

Managed 擴充方法會轉換成類別。 例如，下列的擴充方法 `Collection` ：

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

會建立如下的目標 C 類別：

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

當單一 managed 類型延伸數個類型時，會產生多個目標 C 類別。

### <a name="subscripting"></a>下標

Managed 索引屬性會轉換成物件注標。 例如：

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

會建立如下的目標： C：

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

可以透過目標-C 注標語法使用：

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

視索引子的類型而定，會在適當的位置產生索引或索引注標。

本文 [是注標](https://nshipster.com/object-subscripting/) 的絕佳簡介。

## <a name="main-differences-with-net"></a>.NET 的主要差異

### <a name="constructors-vs-initializers"></a>函數和初始化運算式

在目標 C 中，您可以呼叫繼承鏈中任何父類別的任何初始化運算式原型，除非它標示為無法使用 (`NS_UNAVAILABLE`) 。

在 c # 中，您必須在類別內明確宣告一個函式成員，這表示不會繼承這些函式。

若要將 c # API 的正確標記法公開至目標 C， `NS_UNAVAILABLE` 則會新增至任何不存在父類別之子類別中的初始化運算式。

C # API：

```csharp
public class Unique {
    public Unique () : this (1)
    {
    }

    public Unique (int id)
    {
    }
}

public class SuperUnique : Unique {
    public SuperUnique () : base (911)
    {
    }
}
```

目標-C 呈現的 API：

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

在這裡，已 `initWithId:` 標示為無法使用。

### <a name="operator"></a>運算子

目標 C 不支援運算子多載，如同 c # 一樣，因此運算子會轉換成類別選取器：

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

至

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

不過，有些 .NET 語言並不支援運算子多載，所以除了運算子多載之外，通常也會包含「 [易記](/dotnet/standard/design-guidelines/operator-overloads) 」的命名方法。

如果找到操作員版本和「易記」版本，則只會產生易記版本，因為它們會產生相同的目標 C 名稱。

```csharp
public static AllOperatorsWithFriendly operator + (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}

public static AllOperatorsWithFriendly Add (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}
```

 會變成：

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>等號比較運算子

C # 中的一般運算子會如上面所述，以一般運算子的形式來 `==` 處理。

但是，如果找到「易記」 Equals 運算子， `==` `!=` 就會在世代中略過運算子和運算子。

### <a name="datetime-vs-nsdate"></a>DateTime 與 NSDate

從 [`NSDate`](https://developer.apple.com/reference/foundation/nsdate?language=objc) 檔中：

> `NSDate` 物件會封裝單一時間點，與任何特定的 calendrical 系統或時區無關。 Date 物件是不可變的，表示相對於絕對參考日期的非變異時間間隔 (00:00:00 年1月 2001) 的 UTC。

由於 `NSDate` 參考日期，它之間的所有轉換都 `DateTime` 必須以 UTC 來完成。

#### <a name="datetime-to-nsdate"></a>DateTime 至 NSDate

從轉換成 `DateTime` 時 `NSDate` ，中的 `Kind` 屬性 `DateTime` 會列入考慮：

|種類|結果|
|---|---|
|`Utc`|使用所提供的 `DateTime` 物件來執行轉換。|
|`Local`|`ToUniversalTime()`在提供的物件中呼叫的結果 `DateTime` 會用於轉換。|
|`Unspecified`|提供的 `DateTime` 物件假設為 UTC，因此當為時，就會有相同的行為 `Kind` `Utc` 。|

轉換會使用下列公式：

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

在此公式中： 

- `NSDateReferenceDateTicks` 的計算是根據 `NSDate` 1 月1月 1 2001 日的參考日期 00:00:00 UTC： 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](/dotnet/api/system.timespan.tickspersecond) 定義于 [`TimeSpan`](/dotnet/api/system.timespan)

若要建立 `NSDate` 物件，可搭配 `TimeInterval` `NSDate` [dateWithTimeIntervalSinceReferenceDate：](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) 選取器使用。

#### <a name="nsdate-to-datetime"></a>NSDate 至 DateTime

從轉換 `NSDate` 成會 `DateTime` 使用下列公式：

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

在此公式中： 

- `NSDateReferenceDateTicks` 的計算是根據 `NSDate` 1 月1月 1 2001 日的參考日期 00:00:00 UTC： 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](/dotnet/api/system.timespan.tickspersecond) 定義于 [`TimeSpan`](/dotnet/api/system.timespan)

計算之後 `DateTimeTicks` ，會叫用此函式 `DateTime` [constructor](/dotnet/api/system.datetime.-ctor#System_DateTime__ctor_System_Int64_System_DateTimeKind_) ，並將其設定 `kind` 為 `DateTimeKind.Utc` 。

> [!NOTE]
> `NSDate` 可以是 `nil` ，但 `DateTime` 是 .net 中的結構，其定義不能是 `null` 。 如果您提供 `nil` `NSDate` ，則會將它轉譯為預設 `DateTime` 值，而此值會對應至 `DateTime.MinValue` 。

`NSDate` 支援的最大值和最小值比更高 `DateTime` 。 從轉換成 `NSDate` 時 `DateTime` ，這些較高和較低的值會分別變更為 Int32.maxvalue `DateTime` [MaxValue](/dotnet/api/system.datetime.maxvalue)或[MinValue](/dotnet/api/system.datetime.minvalue)。
---
title: 目標-C 支援
description: 本檔提供 .NET 內嵌中的目標-C 支援說明。 其中討論自動參考計數、NSString、通訊協定、NSObject 通訊協定、例外狀況等。
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: e72f950dc6fcf12e70714e0fbb996ad5ea432548
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029714"
---
# <a name="objective-c-support"></a>目標-C 支援

## <a name="specific-features"></a>特殊功能

目標-C 的產生有一些值得注意的特殊功能。

### <a name="automatic-reference-counting"></a>自動參考計數

呼叫所產生的系結時，**需要**使用自動參考計數（ARC）。 使用 .NET 內嵌程式庫的專案必須以 `-fobjc-arc`編譯。

### <a name="nsstring-support"></a>NSString 支援

公開 `System.String` 類型的 Api 會轉換成 `NSString`。 這使得記憶體管理變得更容易，而不是處理 `char*`。

### <a name="protocols-support"></a>通訊協定支援

Managed 介面會轉換成目標-C 通訊協定，其中的所有成員都 `@required`。

### <a name="nsobject-protocol-support"></a>NSObject 通訊協定支援

根據預設，.NET 和目標 C 執行時間的預設雜湊和相等會假設為可互換，因為它們共用類似的語法。

當 managed 類型覆寫 `Equals(Object)` 或 `GetHashCode`時，通常表示預設（.NET）行為不足夠;這表示預設的目標 C 行為可能不是足夠的。

在這種情況下，產生器會覆寫[`NSObject` 通訊協定](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)中定義的[`isEqual:`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc)方法和[`hash`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc)屬性。 這可讓您以透明的方式從目標 C 程式碼使用自訂的 managed 執行。

### <a name="exceptions-support"></a>例外狀況支援

將 `--nativeexception` 當做引數傳遞至 `objcgen` 會將 managed 例外狀況轉換成可攔截和處理的目標-C 例外狀況。 

### <a name="comparison"></a>邏輯比對

執行 `IComparable` 的 Managed 型別（或其泛型版本 `IComparable<T>`）會產生可傳回 `NSComparisonResult` 並接受 `nil` 引數的目標 C 易懂方法。 如此可讓產生的 API 更方便目標-C 開發人員。 例如：

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categories

Managed 擴充方法會轉換成類別目錄。 例如，下列 `Collection`的擴充方法：

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

會建立如下列的目標-C 類別：

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

當單一 managed 類型擴充數個類型時，會產生多個目標 C 類別。

### <a name="subscripting"></a>下標

Managed 索引屬性會轉換成物件注標。 例如：

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

會建立類似于的目標-C：

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

可以透過目標-C 注標語法來使用：

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

視索引子的類型而定，會在適當的位置產生索引或金鑰注標。

這[篇文章](https://nshipster.com/object-subscripting/)是注標的絕佳簡介。

## <a name="main-differences-with-net"></a>.NET 的主要差異

### <a name="constructors-vs-initializers"></a>函數與初始化運算式

在 [目標-C] 中，您可以呼叫繼承鏈中任何父類別的任何初始化運算式原型，除非它標示為 [無法使用] （`NS_UNAVAILABLE`）。

在C#中，您必須在類別內明確宣告一個「函式」成員，這表示不會繼承「函式」。

為了向目標-C 公開正確C#的 API 標記法，`NS_UNAVAILABLE` 會新增至父類別中不存在於子類別中的任何初始化運算式。

C#API

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

在這裡，`initWithId:` 已標示為無法使用。

### <a name="operator"></a>運算子

目標-C 並不支援運算子C#多載，因此運算子會轉換成類別選取器：

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

to

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

不過，有些 .NET 語言不支援運算子多載，因此除了運算子多載以外，一般也會包含「[易記](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)」的命名方法。

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

滿

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>等號比較運算子

在中C# ，一般運算子 `==` 會當做一般運算子來處理，如上面所述。

不過，如果找到「易記」 Equals 運算子，則會在產生時略過運算子 `==` 和運算子 `!=`。

### <a name="datetime-vs-nsdate"></a>DateTime 與 NSDate 的比較

從[`NSDate`](https://developer.apple.com/reference/foundation/nsdate?language=objc)檔：

> `NSDate` 物件會封裝單一時間點，獨立于任何特定的 calendrical 系統或時區。 Date 物件是不可變的，代表相對於絕對參考日期的非變異時間間隔（00:00:00 UTC 于2001年1月）。

由於 `NSDate` 參考日期，它與 `DateTime` 之間的所有轉換都必須以 UTC 來完成。

#### <a name="datetime-to-nsdate"></a>要 NSDate 的日期時間

從 `DateTime` 轉換成 `NSDate`時，`DateTime` 上的 `Kind` 屬性會列入考慮：

|類型|結果|
|---|---|
|`Utc`|轉換是使用提供的 `DateTime` 物件來執行。|
|`Local`|在提供的 `DateTime` 物件中呼叫 `ToUniversalTime()` 的結果會用於轉換。|
|`Unspecified`|提供的 `DateTime` 物件會假設為 UTC，因此 `Kind` `Utc`時，就會有相同的行為。|

轉換會使用下列公式：

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

在此公式中： 

- `NSDateReferenceDateTicks` 是根據 00:00:00 UTC 1 月2001日的 `NSDate` 參考日期來計算： 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond)定義于[`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

若要建立 `NSDate` 物件，`TimeInterval` 會與 `NSDate` [dateWithTimeIntervalSinceReferenceDate：](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc)選取器搭配使用。

#### <a name="nsdate-to-datetime"></a>NSDate 至 DateTime

從 `NSDate` 到 `DateTime` 的轉換會使用下列公式：

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

在此公式中： 

- `NSDateReferenceDateTicks` 是根據 00:00:00 UTC 1 月2001日的 `NSDate` 參考日期來計算： 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond)定義于[`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

計算 `DateTimeTicks`之後，會叫用 `DateTime` 的函式，[並將其](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_)`kind` 設定為 `DateTimeKind.Utc`。

> [!NOTE]
> `NSDate` 可以 `nil`，但 `DateTime` 是 .NET 中的結構，但無法 `null`。 如果您提供 `nil` `NSDate`，它會轉譯為預設的 `DateTime` 值，這會對應到 `DateTime.MinValue`。

`NSDate` 支援較高的最大值，以及比 `DateTime`更低的最小值。 從`NSDate` `DateTime`轉換成時，這些較高和較低的值會分別變更為int32.[Maxvalue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue)或[MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue)。`DateTime`

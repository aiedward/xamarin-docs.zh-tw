---
title: Objective C 的支援
description: 本文件提供適用於 OBJECTIVE-C 中內嵌.NET 支援的描述。 它討論自動參考計數、 NSString、 通訊協定、 NSObject 通訊協定、 例外狀況，和更多功能。
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 48caa70cf2bd408f8afc673b400f7d5a4369e108
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110715"
---
# <a name="objective-c-support"></a>Objective C 的支援

## <a name="specific-features"></a>特殊功能

Objective C 的新一代具備值得注意的幾個特殊功能。

### <a name="automatic-reference-counting"></a>自動參考計數

使用的自動參考計數 （弧線），就**必要**呼叫產生的繫結。 使用.NET 內嵌為基礎的程式庫的專案必須以編譯`-fobjc-arc`。

### <a name="nsstring-support"></a>NSString 支援

公開 （expose) 的 Api`System.String`類型會轉換為`NSString`。 這會讓記憶體管理容易時處理`char*`。

### <a name="protocols-support"></a>通訊協定支援

受管理的介面會轉換為其中的所有成員都是 OBJECTIVE-C 通訊協定`@required`。

### <a name="nsobject-protocol-support"></a>NSObject 的通訊協定支援

根據預設的預設雜湊和等號比較.NET 和 OBJECTIVE-C 執行階段會假設是可互換的因為它們共用類似的語意。

當 managed 型別會覆寫`Equals(Object)`或`GetHashCode`，這通常表示預設 (.NET) 的行為不足; 這意味著預設的 Objective C 行為很足夠任一個。

在這種情況下，產生器會覆寫[ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc)方法並[ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc)屬性中定義[`NSObject`通訊協定](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)。 這可讓從 OBJECTIVE-C 程式碼以透明的方式使用的自訂 managed 的實作。

### <a name="exceptions-support"></a>例外狀況支援

傳遞`--nativeexception`做為引數`objcgen`會將 managed 例外狀況轉換成可以捕捉及處理的 Objective C 例外狀況。 

### <a name="comparison"></a>邏輯比對

Managed 類型可實作`IComparable`(或其泛型版本`IComparable<T>`) 會產生 OBJECTIVE-C 好用的方法會傳回`NSComparisonResult`並接受`nil`引數。 這可讓產生的 API 以 OBJECTIVE-C 開發人員更輕鬆。 例如: 

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>分類

管理延伸模組方法會轉換成類別目錄。 例如，下列擴充方法上的`Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

會建立這類的 Objective C 類別：

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

當單一 managed 的類型會擴充數種類型時，則會產生 Objective C 的多個類別。

### <a name="subscripting"></a>下標

受管理的索引的屬性會轉換成物件的註標。 例如: 

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

會建立 OBJECTIVE-C 類似於：

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

這可透過 OBJECTIVE-C subscripting 語法：

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

根據您的索引子類型，在適當的地方，就會產生索引或索引鍵的註標。

這[文章](http://nshipster.com/object-subscripting/)是註標的絕佳簡介。

## <a name="main-differences-with-net"></a>使用.NET 的主要差異

### <a name="constructors-vs-initializers"></a>建構函式與初始設定式

在 OBJECTIVE-C、 您可以呼叫任何初始設定式的原型的任何父類別中的繼承鏈結，除非它標示為無法使用 (`NS_UNAVAILABLE`)。

在C#您必須明確宣告建構函式成員的類別，這表示不會繼承建構函式內。

若要公開 （expose） 的正確表示C#API 以 OBJECTIVE-C、`NS_UNAVAILABLE`新增至不是出現在子類別的父類別中的任何初始設定式。

C#API:

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

OBJECTIVE-C 呈現 API:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

在這裡，`initWithId:`已標示為無法使用。

### <a name="operator"></a>運算子

Objective C 不支援運算子多載，為C#可行，因此運算子會轉換成類別選取器：

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

設為

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

不過，某些.NET 語言不支援運算子多載，因此它也包含常見[「 易記 」](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)名為除了運算子多載的方法。

如果運算子版本和 「 易記 」 版本找不到，將產生易記的版本，它們將會產生相同的 Objective C 名稱。

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

在一般的運算子`==`在C#都被視為一般運算子述上方。

不過，如果 「 易記 」 的 Equals 運算子找到，則這兩個運算子`==`and 運算子`!=`在層代將會略過。

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

從[ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc)文件：

> `NSDate` 物件會封裝單一點的時間，依賴任何特定的 calendrical 系統或時區。 Date 物件是不可變的表示相對於絕對參考日期而異的時間間隔 (00: 00:00 UTC 1 年 1 月 2001)。

由於`NSDate`參考之間的所有轉換的日期和`DateTime`必須以 utc 格式。

#### <a name="datetime-to-nsdate"></a>NSDate 的日期時間

從轉換時`DateTime`要`NSDate`，則`Kind`上的屬性`DateTime`會納入考量：

|類型|結果|
|---|---|
|`Utc`|使用提供執行轉換`DateTime`是物件。|
|`Local`|呼叫的結果`ToUniversalTime()`中所提供`DateTime`物件用來進行轉換。|
|`Unspecified`|所提供`DateTime`物件會被假設為 UTC，因此相同的行為時`Kind`是`Utc`。|

轉換會使用下列公式：

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

在此公式： 

- `NSDateReferenceDateTicks` 根據計算`NSDate`上 1 年 1 月 2001年所參考的 00:00:00 UTC 的日期： 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) 定義上 [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

若要建立`NSDate`物件，`TimeInterval`搭配`NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc)選取器。

#### <a name="nsdate-to-datetime"></a>NSDate 為 DateTime

從轉換`NSDate`至`DateTime`會使用下列公式：

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

在此公式： 

- `NSDateReferenceDateTicks` 根據計算`NSDate`上 1 年 1 月 2001年所參考的 00:00:00 UTC 的日期： 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) 定義上 [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

計算之後`DateTimeTicks`，則`DateTime`[建構函式](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_)叫用時，設定其`kind`至`DateTimeKind.Utc`。

> [!NOTE]
> `NSDate` 可以是`nil`，但`DateTime`是在.NET 中，且定義不可以是結構`null`。 如果您賦予`nil` `NSDate`，它將會轉譯為預設值`DateTime`值，這會對應到`DateTime.MinValue`。

`NSDate` 支援更高版本的最大和較低的最小值比`DateTime`。 從轉換時`NSDate`要`DateTime`，這些較高和較低的值都會變更為`DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue)或是[MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue)分別。

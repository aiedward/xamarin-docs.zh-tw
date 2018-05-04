---
title: Objective C 的支援
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 2a4a235dcb885364fdc5add5970e61f46b6e5d08
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="objective-c-support"></a>Objective C 的支援

## <a name="specific-features"></a>特殊功能

Objective C 的產生有一些值得注意的特殊功能。

### <a name="automatic-reference-counting"></a>自動參考計數

是否要使用的自動參考計數 （弧線）**必要**呼叫產生的繫結。 使用內嵌.NET 為基礎的程式庫的專案必須使用編譯`-fobjc-arc`。

### <a name="nsstring-support"></a>NSString 支援

應用程式開發介面中公開`System.String`類型會轉換為`NSString`。 記憶體管理較容易時處理`char*`。

### <a name="protocols-support"></a>通訊協定支援

受管理的介面會轉換成 Objective C 通訊協定，其中的所有成員都是`@required`。

### <a name="nsobject-protocol-support"></a>NSObject 通訊協定支援

根據預設的預設雜湊和等號比較的.NET 和 Objective C 執行階段會假設成交換，因為它們共用類似的語意。

當 managed 型別會覆寫`Equals(Object)`或`GetHashCode`，通常表示預設 (.NET) 的行為不足夠; 這表示預設 OBJECTIVE-C 行為可能沒有足夠權限任一個。

在這種情況下，產生器會覆寫[ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc)方法和[ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc)中定義屬性[`NSObject`通訊協定](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)。 這可讓從 Objective C 程式碼以透明的方式使用的自訂 managed 的實作。

### <a name="exceptions-support"></a>例外狀況支援

傳遞`--nativeexception`做為引數`objcgen`會將 managed 例外狀況轉換成 Objective C 可以捕捉及處理的例外狀況。 

### <a name="comparison"></a>邏輯比對

Managed 類型實作`IComparable`(或其泛型版本`IComparable<T>`) 將會產生 OBJECTIVE-C 好用的方法會傳回`NSComparisonResult`並接受`nil`引數。 這樣產生的 API Objective C 的開發人員更易懂。 例如: 

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>分類

Managed 的擴充方法會轉換成類別目錄。 例如，下列的擴充方法`Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

會建立這類 Objective C 類別：

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

單一的受管理的類型會延伸幾種類型，都會產生 Objective C 的多個類別。

### <a name="subscripting"></a>下標

受管理索引的屬性會轉換成物件註標。 例如: 

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

將建立 OBJECTIVE-C 類似於：

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

這[文章](http://nshipster.com/object-subscripting/)是註標的絕佳的簡介。

## <a name="main-differences-with-net"></a>使用.NET 的主要差異

### <a name="constructors-vs-initializers"></a>建構函式與初始設定式

Objective C，在您可以呼叫任何初始設定式的原型的任何父類別中的繼承鏈結，除非標示為無法使用 (`NS_UNAVAILABLE`)。

在 C# 中，您必須明確宣告建構函式成員類別，這表示不會繼承建構函式內。

若要公開 （expose） 至 Objective C，C# API 的權限表示`NS_UNAVAILABLE`加入至不存在於子類別的父類別從任何初始設定式。

C# API:

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

Objective C 中提出 API:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

在這裡，`initWithId:`已標示為無法使用。

### <a name="operator"></a>運算子

Objective C 不支援運算子多載與 C#，因此運算子會轉換為類別選取器：

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

不過，某些.NET 語言不支援運算子多載，因此它通常也包含[「 易記 」](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)名為除了運算子多載的方法。

如果運算子版本和 「 易記 」 版本發現，好記的版本，將會產生如則會產生相同 Objective C 的名稱。

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

在一般運算子`==`C# 中以處理一般運算子，如先前所述。

不過，如果 「 易記 」 的 「 等於 」 運算子會發現，這兩個運算子`==`和運算子`!=`層代中將會略過。

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

從[ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc)文件：

> `NSDate` 物件會封裝單一點的時間，依賴任何特定 calendrical 系統或時區。 Date 物件是不可變的代表相對於絕對參考日期而異的時間間隔 (00:1 年 1 月 2001 00:00 UTC)。

因為`NSDate`參考之間的所有轉換的日期和`DateTime`必須以 utc 格式。

#### <a name="datetime-to-nsdate"></a>NSDate 日期時間

從轉換時`DateTime`至`NSDate`、`Kind`屬性`DateTime`考量：

|類型|結果|
|---|---|
|`Utc`|使用提供執行轉換`DateTime`是物件。|
|`Local`|呼叫`ToUniversalTime()`中提供`DateTime`物件用來進行轉換。|
|`Unspecified`|提供`DateTime`物件會被假設為 UTC，因此相同的行為時`Kind`是`Utc`。|

轉換會使用下列公式：

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

在此公式： 

- `NSDateReferenceDateTicks` 根據計算`NSDate`1 年 1 月 2001年上所參考的 00:00:00 UTC 日期： 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) 定義上 [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

若要建立`NSDate`物件`TimeInterval`搭配`NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc)選取器。

#### <a name="nsdate-to-datetime"></a>NSDate 為 DateTime

從轉換`NSDate`至`DateTime`會使用下列公式：

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

在此公式： 

- `NSDateReferenceDateTicks` 根據計算`NSDate`1 年 1 月 2001年上所參考的 00:00:00 UTC 日期： 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) 定義上 [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

計算後`DateTimeTicks`、 `DateTime` [建構函式](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_)叫用時，設定其`kind`至`DateTimeKind.Utc`。

> [!NOTE]
> `NSDate` 可以是`nil`，但`DateTime`在.NET 中，它的定義不能是結構`null`。 如果您提供`nil` `NSDate`，它將會轉譯成預設`DateTime`值，對應至`DateTime.MinValue`。

`NSDate` 支援更高版本的最大和較低的最小值比`DateTime`。 當轉換從`NSDate`至`DateTime`，這些較高和較低的值都會變更為`DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue)或[MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue)分別。

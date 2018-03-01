---
title: "Objective C 的支援"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: f6d19f0f6573b17dfb3feb6bf131686413d4e68f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="objective-c-support"></a>Objective C 的支援

## <a name="specific-features"></a>特殊功能

ObjC 產生具有少數的特殊功能，值得注意的。

### <a name="automatic-reference-counting"></a>自動參考計數

是否要使用的自動參考計數 （弧線）**必要**呼叫產生的繫結。 使用 embeddinator 為基礎的程式庫的專案必須使用編譯`-fobjc-arc`。

### <a name="nsstring-support"></a>NSString 支援

應用程式開發介面中公開`System.String`類型會轉換為`NSString`。 記憶體管理較容易處理`char*`。

### <a name="protocols-support"></a>通訊協定支援

受管理的介面會轉換成 ObjC 通訊協定，其中的所有成員都是`@required`。

### <a name="nsobject-protocol-support"></a>NSObject 通訊協定支援

根據預設，我們假設預設的雜湊，而相等的.net 和 ObjC 執行階段正常並且可交換它們共用類似的語意。

當 managed 型別會覆寫`Equals(Object)`或`GetHashCode`這通常代表預設 (.NET) 的行為不是最佳提案。 我們可以假設預設 OBJECTIVE-C 行為不會是。

在這種情況下產生器會覆寫[ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc)方法和[ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc)中定義屬性[`NSObject`通訊協定](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)。 這可讓從 ObjC 程式碼以透明的方式使用的自訂 managed 的實作。

### <a name="comparison"></a>邏輯比對

Managed 類型實作`IComparable`泛型版本`IComparable<T>`會產生 ObjC 好用的方法會傳回`NSComparisonResult`並接受`nil`引數。 這樣產生的 API 更易懂 ObjC 開發人員而言，例如：

```csharp
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>分類

Managed 的擴充方法會轉換成類別目錄。 例如下列的擴充方法`Collection`:

```csharp
    public static class SomeExtensions {

        public static int CountNonNull (this Collection collection) { ... }

        public static int CountNull (this Collection collection) { ... }
    }
```

會建立這類 Objective C 類別：

```csharp
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;
@end
```

當單一受管理類型會延伸幾種類型，則會產生 Objective C 的多個類別。

### <a name="subscripting"></a>下標

受管理索引的屬性會轉換成物件註標。 例如: 

```csharp
    public bool this[int index] {
        get { return c[index]; }
        set { c[index] = value; }
    }
```

將建立 OBJECTIVE-C 類似於：

```csharp
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

這可透過 OBJECTIVE-C subscripting 語法：

```csharp
    if ([intCollection [0] isEqual:@42])
        intCollection[0] = @13;
```

根據您的索引子類型，在適當的地方，就會產生索引或索引鍵的註標。

這[文章](http://nshipster.com/object-subscripting/)是註標的絕佳的簡介。

## <a name="main-differences-with-net"></a>使用.NET 的主要差異

### <a name="constructors-vs-initializers"></a>建構函式與初始設定式

Objective C，在您可以呼叫任何初始設定式的任何父類別的繼承鏈結中的原型除非標示為無法使用 (NS_UNAVAILABLE)。

在 C# 中您必須明確宣告在類別建構函式成員，這表示不會繼承建構函式。

若要公開要 Objective C 的 C# API 的權限表示法，我們加入`NS_UNAVAILABLE`至不存在於子類別的父類別從任何初始設定式。

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

```objectivec
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

這裡我們可以看到`initWithId:`已標示為無法使用。

### <a name="operator"></a>運算子

ObjC 不支援運算子多載與 C#，因此運算子會轉換為類別選取器：

```csharp
    public static AllOperators operator + (AllOperators c1, AllOperators c2)
    {
        return new AllOperators (c1.Value + c2.Value);
    }
```

設為

```csharp
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

不過，某些.NET 語言不支援運算子多載，因此它通常也包含[「 易記 」](https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx)名為除了運算子多載的方法。

如果運算子版本和 「 易記 」 版本發現，好記的版本，將會產生如則會產生相同的目標 c 名稱。

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

```csharp
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>等號比較運算子

在一般運算子 = = 在 C# 以處理一般運算子述上方。

不過，如果 「 易記 」 的 「 等於 」 運算子會發現，這兩個運算子 = = 和運算子 ！ = 會略過層代中。

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

從[NSDate 的](https://developer.apple.com/reference/foundation/nsdate?language=objc)文件：

> NSDate 物件會封裝單一點的時間，依賴任何特定 calendrical 系統或時區。 Date 物件是不可變的代表相對於絕對參考日期而異的時間間隔 (00:1 年 1 月 2001 00:00 UTC)。

因為`NSDate`參考之間的所有轉換的日期和`DateTime`必須以 utc 格式。

#### <a name="datetime-to-nsdate"></a>NSDate 日期時間

當轉換從`DateTime`至`NSDate`的日期時間`Kind`屬性納入考量。

<table>
<tr><th> 類型         </th><th> 結果                                                                                            </th></tr>
<!--tr><td> ------------ </td><td> -------------------------------------------------------------------------------------------------- </td></tr-->
<tr><td> Utc          </td><td> 使用所提供的 DateTime 物件是一樣，會執行轉換。                                  </td></tr>
<tr><td> 本機        </td><td> 呼叫`ToUniversalTime ()`中提供的 DateTime 物件用來進行轉換。 </td></tr>
<tr><td> 未指定  </td><td> 所提供的 DateTime 物件假設為 UTC，因此相同的行為類型 = = Utc。                </td></tr>
</table>

使用下列公式進行轉換：

> [!NOTE]
> **TimeInterval** = DateTimeObjectTicks - NSDateReferenceDateTicks[dt] / [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx)

一旦 TimeInterval 我們使用 NSDate 的[dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc)選取器來建立它。

#### <a name="nsdate-to-datetime"></a>NSDate 為 DateTime

從 NSDate 轉為我們假設我們越來越 NSDate DateTime 執行個體，也就是參考日期是**00:00:00 UTC 在 1 年 1 月 2001年**並使用下列公式：

> [!NOTE]
> **DateTimeTicks** = NSDateTimeIntervalSinceReferenceDate * [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx) + NSDateReferenceDateTicks[dt]

一旦我們計算**DateTimeTicks**我們使用下列 DateTime[建構函式](https://msdn.microsoft.com/en-us/library/w0d47c9c(v=vs.110).aspx)設定其`kind`至`DateTimeKind.Utc`。

您必須知道的一些考量、 NSDate 可以是`nil`但日期時間是在.NET 結構，並根據定義，它不能`null`。 如果您提供`nil`NSDate 我們將會轉譯為預設的日期時間值對應到`DateTime.MinValue`。

MinValue 與 MaxValue 也會不同，NSDate 可以支援更高和較低比 DateTime 的界限，因此每當您提供更高或較低值，我們會將它設定為 DateTime 的[MaxValue](https://msdn.microsoft.com/en-us/library/system.datetime.maxvalue(v=vs.110).aspx)或[MinValue](https://msdn.microsoft.com/en-us/library/system.datetime.minvalue(v=vs.110).aspx)分別。

**dt**: NSDate 參考日期**1 年 1 月 2001 的 00:00:00 UTC** => `new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;`

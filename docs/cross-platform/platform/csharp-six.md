---
title: C#6個新功能總覽
description: 第6版的C#語言會繼續進化語言，使其具有較少的重複使用程式碼、改善的清晰度，以及更高的一致性。 清除程式初始化語法、在 catch/finally 區塊中使用 await 的能力，以及 null 條件的功能嗎？ 運算子特別有用。
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 2a62ff12869f886e63fc5c78050e9870431e58bb
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280612"
---
# <a name="c-6-new-features-overview"></a>C#6個新功能總覽

_第6版的C#語言會繼續進化語言，使其具有較少的重複使用程式碼、改善的清晰度，以及更高的一致性。清除程式初始化語法、在 catch/finally 區塊中使用 await 的能力，以及 null 條件的功能嗎？運算子特別有用。_

> [!NOTE]
> 如需最新C#語言版本的詳細資訊–第7版–請參閱[ C# 7.0 的新功能](/dotnet/csharp/whats-new/csharp-7)文章

本檔介紹C# 6 的新功能。 Mono 編譯器完全支援它，而開發人員可以開始在所有 Xamarin 目標平臺上使用新功能。

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**C# 6 的新功能影片**

## <a name="using-c-6"></a>使用C# 6

C# 6 個編譯器會用於所有最新版本的 Visual Studio for Mac。
使用命令列編譯器的`mcs --version`那些應用程式應該確認傳回4.0 或更高版本。
Visual Studio for Mac 使用者可以藉由參考**About Visual Studio for Mac > Visual Studio for Mac > 顯示詳細資料**，檢查是否已安裝 Mono 4 （或更新版本）。

## <a name="less-boilerplate"></a>較少的樣板
### <a name="using-static"></a>使用靜態
列舉和特定類別（例如`System.Math`）主要是靜態值和函數的持有者。 在C# 6 中，您可以使用單一`using static`語句匯入類型的所有靜態成員。 比較5和C# C# 6 中的一般三角函數函數：

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static`不會讓公用`const`欄位（ `Math.PI`例如和`Math.E`）可直接存取：

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>搭配使用 static 與擴充方法

`using static`設備的運作方式與擴充方法稍有不同。 雖然擴充方法是使用`static`撰寫的，但沒有要在其上運作的實例並沒有意義。 因此， `using static`當搭配定義擴充方法的類型使用時，擴充方法會在其目標型別（方法的`this`類型）上變成可用。 例如，可以`using static System.Linq.Enumerable`用來擴充`IEnumerable<T>`物件的 API，而不需要帶入所有的 LINQ 類型：

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

上一個範例示範行為的差異：擴充方法`Enumerable.Where`與陣列相關聯，而靜態方法`String.Join`則可以在不參考該`String`類型的情況下呼叫。

### <a name="nameof-expressions"></a>nameof 運算式
有時候，您會想要參考您已提供變數或欄位的名稱。 在C# 6 中`nameof(someVariableOrFieldOrType)` ，會傳回字串`"someVariableOrFieldOrType"`。 比方說，當擲回`ArgumentException`時，您很可能想要將不正確引數命名為：

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

`nameof`運算式的主要優點是它們的類型為已核取，而且與工具支援的重整相容。 在用來動態關聯`nameof`類型的情況下`string` ，運算式的類型檢查特別歡迎。 例如，在 iOS a `string`中，是用來指定在中`UITableView`用來`UITableViewCell`原型物件的型別。 `nameof`可以確保這個關聯不會因為拼寫錯誤或這個散漫重構而失敗：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

雖然您可以將限定名稱傳遞給`nameof`，但只會傳回最後一個專案（ `.`在最後一個後面）。 例如，您可以在 Xamarin 中加入資料系結。表單：

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

對`SetBinding`的兩個呼叫會傳遞相同的`nameof(ReactiveType.StringField)`值`"StringField"`：是`"ReactiveType.StringField"` ，而不是您最初預期的方式。

## <a name="null-conditional-operator"></a>Null-條件運算子
先前的更新C#導入了可為 null 的型別和 null 聯合`??`運算子的概念，以減少處理可為 null 值時的重複採用程式碼數量。 C#6使用「null 條件運算子`?.`」繼續此主題。 當用於運算式右邊的物件時，如果物件不`null`是， `null`則 null 條件運算子會傳回成員值，否則會傳回：

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

`length0` （和`length1`都會推斷成類型`int?`）

上一個範例中的最後一行顯示`?` null 條件運算子`??`與 null 聯合運算子的組合。 新C#的6個 null 條件運算子`null`會在陣列中的第二個元素上傳回，此時 null 聯合運算子就會開始，並提供0給`lengths`陣列（當然，問題特定）。

Null 條件運算子應該可大幅減少許多應用程式中所需的重複執行 null 檢查量。

因為不明確，所以 null 條件運算子有一些限制。 您不能緊接在`?`加上括弧的引數清單之後，您可能想要使用委派來執行下列動作：

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

不過， `Invoke`可以用來分隔引數`?`清單中的，而且仍然是已標示為重複使用`null`的檢查區塊的改善：

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>字串插值
函式過去使用了索引做為格式字串中的預留位置，例如`String.Format("Expected: {0} Received: {1}.", expected, received`）。 `String.Format` 當然，在引數清單中，加入新的值時，一定會有一個令人討厭的小工作，包括計算引數、重新排序預留位置，以及將新引數插入正確的順序。

C#6的新字串插補功能大幅改善`String.Format`了。 現在，您可以在前面`$`加上的字串中，直接命名變數。 若是執行個體：

```csharp
$"Expected: {expected} Received: {received}."
```

當然，變數是已核取狀態，而拼錯或不可用的變數會造成編譯器錯誤。

預留位置不需要是簡單變數，可以是任何運算式。 在這些預留位置中，您可以使用引號，*而不會*將這些引號轉義。 例如，請注意`"s"`下列各項：

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

字串插補支援的對齊和格式化語法`String.Format`。 就像您先前所`{index, alignment:format}`撰寫的C#一樣，在`{placeholder, alignment:format}`6 中您可以撰寫：

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```

產生的結果：

```
The value is       1.00.
The value is       2.00.
The value is       3.00.
The value is       4.00.
The value is      12.00.
The value is 123,456.00.
Minimum is 1.00.
```

字串插補是的`String.Format`語法：它不能`@""`用於字串常值`const`，而且與不相容，即使沒有使用預留位置也一樣：

```csharp
const string s = $"Foo"; //Error : const requires value
```

在使用字串內插補點建立函式引數的常見使用案例中，您仍然需要特別注意有關轉義、編碼和文化特性的問題。 SQL 和 URL 查詢當然是要淨化的關鍵。 如同，字串插補會`CultureInfo.CurrentCulture`使用。 `String.Format` 使用`CultureInfo.InvariantCulture`稍微冗長：

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>初始化

C#6提供幾種簡單的方式來指定屬性、欄位和成員。

### <a name="auto-property-initialization"></a>自動屬性初始化

自動屬性現在可以用與欄位相同的簡潔方式初始化。 不可變的自動屬性只能以 getter 撰寫：

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

在此函數中，您可以設定僅限 getter 的 auto 屬性值：

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

這種自動屬性的初始化是一般的空間儲存功能，以及想要強調其物件中不可變性的開發人員所 boon 的。

### <a name="index-initializers"></a>索引初始設定式

C#6引進索引初始化運算式，這可讓您在具有索引子的類型中設定索引鍵和值。 一般來說，這是針對`Dictionary`樣式的資料結構：

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>運算式主體函數成員

Lambda 函式有數個優點，其中一個只是儲存空間。 同樣地，運算式主體類別成員允許小型函式的表示方式比在舊版C# 6 中可能更簡潔。

運算式主體函數成員會使用 lambda 箭號語法，而不是傳統的區塊語法：

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

請注意，lambda 箭號語法不會使用明確`return`的。 針對傳回的函式，運算式也必須是語句：`void`

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

運算式主體成員仍然受限於方法所支援的規則`async` ，而不是屬性：

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>例外狀況

有兩種方法：例外狀況處理很難正確地取得。 6的C#新功能讓例外狀況處理更具彈性且一致。

### <a name="exception-filters"></a>例外狀況篩選條件

根據定義，例外狀況會在不尋常的情況中發生，而且可能會很難以撰寫特定類型*例外的原因*和程式碼。 C#6引進了使用執行時間評估的篩選準則來保護執行處理常式的功能。 這是藉由在一般`when (bool)` `catch(ExceptionType)`宣告之後加入模式來完成。 在下列中，篩選器會區分與`date`參數相關的剖析錯誤，而不是其他剖析錯誤。

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>catch 中的 await 。最後 。

5 `async`中C#引進的功能是語言的遊戲變更器。 在C# 5 `await`中，在和`finally`區塊`catch`中不允許，因為`async/await`功能的值，而無干擾。 C#6移除這項限制，讓非同步結果能夠以一致的方式透過程式等候，如下列程式碼片段所示：

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>總結

此C#語言會持續演進，讓開發人員更具生產力，同時也能推廣良好的實務和支援的工具。 本檔概述6中C#的新語言功能，並簡要示範其使用方式。

## <a name="related-links"></a>相關連結

- [6中的C#新語言功能](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)


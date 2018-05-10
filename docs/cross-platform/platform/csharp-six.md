---
title: C# 6 的新功能概觀
description: 最新版的 C# 語言 – 第 6 版 – 會持續發展成具有較少的未定案、 改良的清晰度和較佳的一致性的語言。 清理初始化語法，能夠使用 await catch/finally 區塊和 null 條件嗎？ 運算子時特別有用。
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: e120b917a106d01067e073219bbe2688c2811448
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="c-6-new-features-overview"></a>C# 6 的新功能概觀

_最新版的 C# 語言 – 第 6 版 – 會持續發展成具有較少的未定案、 改良的清晰度和較佳的一致性的語言。清理初始化語法，能夠使用 await catch/finally 區塊和 null 條件嗎？運算子時特別有用。_

本文件介紹 C# 6 的新功能。 單聲道編譯器完全支援和開發人員可以開始使用所有 Xamarin 目標平台的新功能。

本文包含 C# 6 程式碼的簡短說明使用基本的片段。
範例應用程式是命令列程式可執行所有 Xamarin 目標平台，並執行各項功能。


> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**最新消息在 C# 6 中，藉由[Xamarin 大學](https://university.xamarin.com/)**


## <a name="development-environment"></a>開發環境

### <a name="mac"></a>Mac

* **Visual Studio for Mac**支援 C# 6： 您可以建立並編譯 Xamarin 應用程式使用 C# 6 的功能。
  深入了解[Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/)。

### <a name="windows"></a>Windows

* **Visual Studio 2015 和 2017年**及更新版本完全支援 C# 6。 舊版的 Visual Studio 將不支援 C# 6。

* **適用於 Windows 的 Xamarin Studio**不支援在編輯器中的 C# 6 的功能。



## <a name="compiler"></a>編譯器

單聲道 C# 6 編譯器隨附於 Mono 4.0 和更新版本，也就是[免費下載](http://www.mono-project.com/download/)。
適用於 Mac 的 visual Studio 會自動更新系統上的安裝單聲道。

Windows 使用者必須擁有[Visual Studio 2015 或 2017年 ^](https://www.visualstudio.com/)安裝 （即使您選擇適用於 Windows 的 Xamarin Studio 為您的 IDE） 編譯 C# 6 程式碼。

^ 或 *[Microsoft 建置工具 2015年](http://www.microsoft.com/download/details.aspx?id=48159)* 命令列編譯或組建伺服器，例如。

## <a name="using-c-6"></a>使用 C# 6

C# 6 編譯器用於最新版 Visual Studio for mac。
使用命令列編譯器應該確認`mcs --version`傳回 4.0 或更新版本。
Visual Studio，針對 Mac 使用者可以檢查是否有 4 個 （或更新版本） 的 Mono 安裝藉由參考**有關 Visual Studio for Mac > Visual Studio for Mac > 顯示詳細資料**。

## <a name="less-boilerplate"></a>較少的重複使用
### <a name="using-static"></a>使用靜態
列舉型別和特定類別，例如`System.Math`，是主要的靜態值和函式的持有者。 在 C# 6 中，您可以匯入具有單一類型的所有靜態成員`using static`陳述式。 比較 C# 5 和 C# 6 中的典型三角函式：

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

`using static` 不會讓公用`const`欄位，例如`Math.PI`和`Math.E`、 直接存取：

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>擴充方法與使用靜態

`using static`設備運作的方式稍有不同的擴充方法使用。 雖然擴充方法撰寫使用`static`，它們不合理沒有運算執行個體。 因此`using static`定義擴充方法，其目標類型上可用的擴充方法的類型搭配使用 (方法的`this`類型)。 比方說，`using static System.Linq.Enumerable`可用於延伸的 API`IEnumerable<T>`物件，而不將所有 LINQ 類型結合：

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

前一個範例示範的行為差異： 擴充方法`Enumerable.Where`相關聯的陣列，靜態方法時`String.Join`可以不使用參考呼叫`String`型別。

### <a name="nameof-expressions"></a>nameof 運算式
您想要參照的某些情況下，您已獲得變數或欄位的名稱。 在 C# 6 中`nameof(someVariableOrFieldOrType)`會傳回字串`"someVariableOrFieldOrType"`。 比方說，當擲回`ArgumentException`您很可能會想要命名的引數無效：

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

主要優點`nameof`運算式是它們會檢查類型，且會與工具供電重構相容。 類型檢查的`nameof`運算式是在情況下特別褖畫惎其中`string`來動態建立的關聯型別。 例如，在 iOS`string`用來指定類型，用來原型`UITableViewCell`中的物件`UITableView`。 `nameof` 可讓這個關聯不會因為拼字錯誤或草率重構失敗：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

雖然您可以將傳遞的限定的名稱`nameof`，最後一個項目 (最後一個之後`.`) 會傳回。 比方說，您可以加入資料繫結，在 Xamarin.Forms 中：

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

兩個呼叫`SetBinding`傳遞相同的值：`nameof(ReactiveType.StringField)`是`"StringField"`，而非`"ReactiveType.StringField"`如您一開始所預期。

## <a name="null-conditional-operator"></a>Null 條件運算子
C# 的舊版更新導入可為 null 的類型和 null 聯合運算子的概念`??`減少時處理可為 null 值的未定案程式碼數量。 C# 6 會繼續使用 「 null 條件運算子 」 這個主題`?.`。 Null 條件運算子右手邊的運算式上的物件上使用時，會傳回成員值的物件是否不`null`和`null`否則：

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(兩者`length0`和`length1`推斷的型別`int?`)

前一個範例中的最後一行`?`null 條件運算子結合`??`null 聯合運算子。 新的 C# 6 null 條件運算子傳回`null`此時 null 聯合運算子會介入並提供 0 的陣列中的第 2 個項目`lengths`陣列 （無論，或不是，當然，特定的問題）。

Null 條件運算子應該可大幅減少在許多應用程式中的未定案 null 檢查所需。

有一些限制，因為模稜兩可的 null 條件運算子。 您不能緊接`?`括號括住引數清單，為您可能希望將委派：

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

不過，`Invoke`可以用來分隔`?`從引數清單，而且仍然是透過標示的改進`null`-檢查重複的區塊：

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>字串插值
`String.Format`函式具有傳統上使用索引做為預留位置，在格式字串中，例如`String.Format("Expected: {0} Received: {1}.", expected, received`)。 當然，加入新的值有牽涉到引數的總數、 預留位置，重新編號和引數清單中向右的順序插入新的引數造成困擾小工作。

C# 6 的新字串插值功能可大幅改善`String.Format`。 現在，您可以直接名稱前面加上字串中的變數`$`。 若是執行個體：

```csharp
$"Expected: {expected} Received: {received}."
```

當然，檢查變數及拼字錯誤或非可用的變數會造成編譯器錯誤。

不需要是簡單的變數，預留位置，它們可以是任何運算式。 在這些預留位置，您可以使用引號*沒有*逸出這些引號內。 例如，請注意`"s"`下列：

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

字串插值支援的對齊方式和格式化語法`String.Format`。 如同先前所說明的`{index, alignment:format}`，在您撰寫 C# 6 `{placeholder, alignment:format}`:

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

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

字串插值會針對語法捷徑`String.Format`： 它不能與`@""`字串常值和不相容`const`，即使沒有預留位置可用：

```csharp
const string s = $"Foo"; //Error : const requires value
```

在常見使用案例的建置與字串插值的函式引數，您仍然需要謹慎逸出、 編碼和文化特性的問題。 SQL 和 URL 查詢不可或缺，當然淨化。 如同`String.Format`，字串插值使用`CultureInfo.CurrentCulture`。 使用`CultureInfo.InvariantCulture`是稍微喘：

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>初始化

C# 6 提供簡潔的方式來指定屬性、 欄位和成員的數字。

### <a name="auto-property-initialization"></a>Auto 屬性初始設定

現在可以在欄位相同簡潔的方式初始化 auto 屬性。 不可變的 auto 屬性可以寫入時會包含只 getter:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

在建構函式，您可以設定僅限 getter 的 auto 屬性的值：

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

一般的節省空間的功能而開發人員想要強調其物件中的不變性一大自動屬性的這個初始設定。

### <a name="index-initializers"></a>索引初始設定式

C# 6 導入了索引初始設定式，讓您設定索引鍵和值都有索引子的型別中。 一般而言，這是針對`Dictionary`-樣式的資料結構：

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

### <a name="expression-bodied-function-members"></a>運算式主體的函式成員

Lambda 函式具有幾項優點，其中只會儲存空間。 同樣地，運算式主體的類別成員允許稍微更簡潔的方式比在舊版的 C# 6 中表示的小型函式。

運算式主體的函式成員使用 lambda 箭號語法，而不是傳統的區塊語法：

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

請注意，lambda 箭號語法不會使用明確`return`。 為函式，會傳回`void`，運算式也必須在陳述式：

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

運算式主體的成員仍然受到此規則的`async`支援的方法，但不是屬性：

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>例外狀況

沒有任何資訊，請參閱的兩種方式： 例外狀況處理，因而難以正確。 在 C# 6 中的新功能可讓例外狀況處理更有彈性且一致。

### <a name="exception-filters"></a>例外狀況篩選條件

根據定義，在不常見的情況下，都會發生例外狀況，而且可以很難原因和相關的程式碼*所有*可能會發生特定類型的例外狀況的方式。 C# 6 導入的能力來保護執行處理常式與執行階段評估篩選條件。 這是藉由新增`when (bool)`之後標準模式`catch(ExceptionType)`宣告。 下列程式碼，在篩選中區分相關的剖析錯誤`date`相對於其他剖析錯誤的參數。

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

### <a name="await-in-catchfinally"></a>在 catch 中...等候最後...

`async` C# 5 中引入的功能已被遊戲換片機語言。 在 C# 5 中，`await`中不允許`catch`和`finally`封鎖了，給定的值煩人`async/await`功能。 C# 6 會移除這項限制，讓非同步結果可等候以一致的方式透過計劃中的下列程式碼片段所示：

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

C# 語言會持續發展時也升級很好的作法和支援的工具，讓開發人員更具生產力。 這份文件已在 C# 6 中提供的新語言功能的概觀和簡單示範如何使用它們。

## <a name="related-links"></a>相關連結

- [在 C# 6 中的新語言功能](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)
- [使用 C# 6 活頁簿](https://developer.xamarin.com/workbooks/csharp/csharp6/csharp6.workbook)

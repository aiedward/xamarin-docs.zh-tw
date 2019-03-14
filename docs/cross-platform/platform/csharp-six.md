---
title: C#6 的新功能概觀
description: 最新版的C#語言 – 第 6 版 – 持續發展的語言有較少的未定案、 改善的清晰度和較佳的一致性。 更簡潔的初始化語法，能夠使用 catch/finally 區塊，以及 null 條件中的 await 嗎？ 運算子會特別有用。
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: d5478a09c461ec8f1bf51efaa7b4dc2f862d69b4
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668931"
---
# <a name="c-6-new-features-overview"></a>C#6 的新功能概觀

_最新版的C#語言 – 第 6 版 – 持續發展的語言有較少的未定案、 改善的清晰度和較佳的一致性。更簡潔的初始化語法，能夠使用 catch/finally 區塊，以及 null 條件中的 await 嗎？運算子會特別有用。_

本文件介紹的新功能C#6。 Mono 編譯器完全支援和開發人員就可以開始使用所有 Xamarin 目標平台的新功能。

這篇文章包含簡短的程式碼片段的C#6 的程式碼，說明基本用法。
範例應用程式是執行所有的 Xamarin 目標平台，並執行各種功能的命令列程式。


> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**新功能C#6，依[Xamarin University](https://university.xamarin.com/)**


## <a name="development-environment"></a>開發環境

### <a name="mac"></a>Mac

* **Visual Studio for Mac**支援C#6： 您可以建置和編譯使用的 Xamarin 應用程式C#6 的功能。
  深入了解[Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/)。

### <a name="windows"></a>Windows

* **Visual Studio 2015 和 2017年**及更新版本完全支援C#6。 不支援舊版的 Visual Studio C# 6。

* **Xamarin Studio for Windows**不支援C#6 的功能，在編輯器中。



## <a name="compiler"></a>編譯器

MonoC#隨附於 Mono 4.0 和更新版本，也就是 6 編譯器[供免費下載](https://www.mono-project.com/download/)。
Visual Studio for Mac 會自動更新您的系統上的 Mono 安裝。

Windows 使用者必須擁有[Visual Studio 2015 或 2017年 ^](https://visualstudio.microsoft.com/)安裝成編譯C#6 程式碼 （即使您選擇用於 Windows 的 Xamarin Studio，為您的 IDE）。

^ 或 *[Microsoft Build Tools 2015](https://www.microsoft.com/download/details.aspx?id=48159)* 命令列編譯或組建伺服器，例如。

## <a name="using-c-6"></a>使用C#6

C# 6 編譯器可在所有的最新版本的 Visual Studio for mac。
使用命令列編譯器應該確認`mcs --version`傳回 4.0 或更新版本。
Visual Studio for Mac 使用者可以檢查是否有 4 個 （或更新版本） 的 Mono 安裝藉由參考**關於 Visual Studio for Mac > Visual Studio for Mac > 顯示詳細資料**。

## <a name="less-boilerplate"></a>少的樣板
### <a name="using-static"></a>使用靜態
列舉和特定類別，例如`System.Math`，是主要的靜態值和函式的持有者。 在C#6，您可以匯入單一類型的所有靜態成員`using static`陳述式。 比較中的典型三角函式C#5 和C#6:

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

`using static` 不會公開`const`欄位，例如`Math.PI`和`Math.E`、 直接存取：

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>擴充方法使用靜態

`using static`設備運作稍有不同的擴充方法。 雖然擴充方法撰寫使用`static`，它們也沒有意義沒有運算執行個體。 因此當`using static`定義擴充方法，其目標類型上可用的擴充方法的類型搭配使用 (方法的`this`型別)。 比方說，`using static System.Linq.Enumerable`可用來擴充的 API`IEnumerable<T>`物件，而不將所有 LINQ 類型：

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

前一個範例示範的行為差異： 擴充方法`Enumerable.Where`陣列中的靜態方法時相關聯`String.Join`可以不使用參考呼叫`String`型別。

### <a name="nameof-expressions"></a>nameof 運算式
您想要參考的某些情況下，您已獲得的變數或欄位的名稱。 在C#第 6`nameof(someVariableOrFieldOrType)`會傳回字串`"someVariableOrFieldOrType"`。 比方說，在擲回時`ArgumentException`就很可能會想要命名的引數無效：

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

主要的優點`nameof`運算式就是它們會檢查類型，並使用重構工具架構相容。 類型檢查的`nameof`運算式會在情況下特別歡迎其中`string`用來以動態方式將類型產生關聯。 例如，在 iOS`string`用來指定用來建立原型的型別`UITableViewCell`中的物件`UITableView`。 `nameof` 可確保此關聯不會因拼字錯誤或草率重構而失敗：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

雖然您可以傳遞限定的名稱得以`nameof`，最後一個項目 (最後一個之後`.`) 會傳回。 比方說，您也可以在 Xamarin.Forms 中加入資料繫結：

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

這兩個呼叫`SetBinding`傳遞相同的值：`nameof(ReactiveType.StringField)`是`"StringField"`，而非`"ReactiveType.StringField"`如您一開始所預期。

## <a name="null-conditional-operator"></a>Null 條件運算子
先前的更新C#導入的概念，可為 null 的型別以及 null 聯合運算子`??`減少時處理可為 null 值的未定案程式碼數量。 C#6 會繼續使用 「 null 條件運算子 」 此佈景主題`?.`。 如果物件不是 null 條件運算子運算式的右側物件上使用時，會傳回成員值`null`和`null`否則：

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(兩者`length0`並`length1`推斷為類型`int?`)

在上一個範例所示的最後一行`?`null 條件運算子結合`??`null 聯合運算子。 新C#6 個 null 條件運算子會傳回`null`第 2 個項目，在陣列中，此時 null 聯合運算子就會執行並提供 0 上`lengths`陣列 （無論是適當或不是，當然，問題-專用）。

Null 條件運算子應該可大幅減少在許多應用程式中的未定案 null 檢查所需。

有一些限制，因為模稜兩可的 null 條件運算子。 您無法立即遵循`?`使用括號括住的引數清單中，您可能希望如何處理委派：

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

不過，`Invoke`可以用來分隔`?`引數清單和移轉仍是標示的改進`null`-檢查未定案的區塊：

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>字串插值
`String.Format`函式具有傳統上使用索引做為預留位置的格式字串，例如`String.Format("Expected: {0} Received: {1}.", expected, received`)。 當然，加入新的值時，一律具有涉及起跳引數及重新編號的預留位置，以正確的順序，引數清單中插入新的引數的煩人小工作。

C#6 的新字串內插補點功能可大幅改良`String.Format`。 現在，您可以直接名稱前面會加上字串中的變數`$`。 若是執行個體：

```csharp
$"Expected: {expected} Received: {received}."
```

當然，檢查變數和拼字錯誤或非可用的變數會造成編譯器錯誤。

預留位置不需要是簡單的變數，它們可以是任何運算式。 這些預留位置，在中，您可以使用引號*而不需要*逸出這些引號。 比方說，請注意`"s"`如下：

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

字串內插補點支援的對齊方式和格式化語法`String.Format`。 就如同您先前撰寫`{index, alignment:format}`，請在C#6 您撰寫`{placeholder, alignment:format}`:

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
中的結果：

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

字串內插補點時的語法而已`String.Format`： 它不能搭配`@""`字串常值和與不相容`const`，即使沒有預留位置會使用：

```csharp
const string s = $"Foo"; //Error : const requires value
```

在常見的使用案例的建置使用字串插補的函式引數，您仍然需要小心逸出、 編碼及文化特性的問題。 SQL 和 URL 的查詢至關重要，當然，若要處理。 如同`String.Format`，字串內插補點會使用`CultureInfo.CurrentCulture`。 使用`CultureInfo.InvariantCulture`是稍微冗長：

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>初始化

C#6 提供簡潔的方式，來指定屬性、 欄位和成員數目。

### <a name="auto-property-initialization"></a>Auto 屬性初始設定

現在可以在相同的簡潔方式，做為欄位初始化 auto 屬性。 不可變的 auto 屬性可以使用只有 getter 撰寫：

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

在建構函式，您可以設定的僅限 getter 的 auto 屬性的值：

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

此初始化 auto 屬性是一般的節省空間的功能和開發人員想要強調其物件中的不變性一大福音。

### <a name="index-initializers"></a>索引初始設定式

C#6 引進索引初始設定式，可讓您設定索引子的型別中的金鑰和值。 一般而言，這是針對`Dictionary`-樣式資料結構：

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

Lambda 函式有幾項優點，其中只節省空間。 同樣地，運算式主體的類別成員允許小型函式，來表示有點更簡潔的方式比在舊版的C#6。

運算式主體的函式成員使用 lambda 箭號語法，而不是傳統的區塊的語法：

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

請注意，lambda 箭號語法不會使用明確`return`。 針對函式，會傳回`void`，運算式也必須在陳述式：

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

運算式主體成員仍受限於此規則的`async`方法，而不是屬性的支援：

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>例外狀況

沒有任何相關的兩種方式： 例外狀況處理很難正確。 中的新功能C#6 進行例外狀況處理，更有彈性且一致。

### <a name="exception-filters"></a>例外狀況篩選條件

根據定義，在不尋常的情況下，都會發生例外狀況，而且可能原因和相關的程式碼很難*所有*可能發生特定類型的例外狀況的方式。 C#6 引進了保護的執行處理常式與執行階段評估篩選條件。 這是藉由新增`when (bool)`之後的一般模式`catch(ExceptionType)`宣告。 在下列程式碼，篩選區分相關的剖析錯誤`date`相對於其他剖析錯誤的參數。

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

### <a name="await-in-catchfinally"></a>在 catch 中等候的...最後...

`async`中引進的功能C#5 已遊戲換片機語言。 在C#第 5`await`中不允許`catch`並`finally`區塊中使用，惱人的值指派給`async/await`功能。 C#6 會移除這項限制，允許非同步等候以一致的方式透過計劃中的下列程式碼片段所示的結果：

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

C#語言持續發展，讓開發人員的生產力，同時升級的最佳做法，並支援工具。 本文件提供的新語言功能的概觀C#6 和簡短示範如何使用它們。

## <a name="related-links"></a>相關連結

- [中的新語言功能C#6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)


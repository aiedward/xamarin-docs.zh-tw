---
title: 非同步總覽
description: C# 語言 – 第 5 版 – 的最新版本導入了兩個新的關鍵字來表示非同步作業： async 和 await。 這些關鍵字可讓您在另一個執行緒中撰寫簡單的程式碼，它會利用工作平行程式庫執行長時間執行的作業 （例如網路存取），並輕鬆地存取在完成的結果。 最新版的 Xamarin.iOS 和 Xamarin.Android 支援 async 和 await-本文件提供說明和使用新語法中使用 Xamarin 的範例。
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0ecad6259cb0d472ac39afb0a6be980d4582812c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="async-support-overview"></a>非同步支援概觀

_C# 語言 – 第 5 版 – 的最新版本導入了兩個新的關鍵字來表示非同步作業： async 和 await。這些關鍵字可讓您在另一個執行緒中撰寫簡單的程式碼，它會利用工作平行程式庫執行長時間執行的作業 （例如網路存取），並輕鬆地存取在完成的結果。最新版的 Xamarin.iOS 和 Xamarin.Android 支援 async 和 await-本文件提供說明和使用新語法中使用 Xamarin 的範例。_

Xamarin 的非同步支援單聲道 3.0 基礎上建置，並從正在行動設備友善的 Silverlight 版本是.NET 4.5 的行動設備友善版本升級應用程式開發介面的設定檔。

## <a name="overview"></a>總覽

這份文件導入了新的非同步和 await 關鍵字，則實作非同步方法中 Xamarin.iOS 和 Xamarin.Android 一些簡單的案例會逐步解說。

如需 C# 5 （包括許多範例和不同使用案例） 的新非同步功能的更完整討論，請參閱 MSDN 文件[使用 Async 和 Await 進行非同步程式設計](http://msdn.microsoft.com/en-us/library/vstudio/hh191443.aspx)。

範例應用程式提出簡單非同步的 web 要求 （而不會封鎖主執行緒），然後以下載的 html 和字元計數更新 UI。

 [![](async-images/AsyncAwait_427x368.png "範例應用程式而不會封鎖主執行緒會簡單非同步的 web 要求，然後以下載的 html 和字元計數更新 UI")](async-images/AsyncAwait.png#lightbox)

Xamarin 的非同步支援單聲道 3.0 基礎上建置，並從正在行動設備友善的 Silverlight 版本是.NET 4.5 的行動設備友善版本升級應用程式開發介面的設定檔。

## <a name="requirements"></a>需求

C# 5 功能都需要包含在 Xamarin.iOS 6.4 和 Xamarin.Android 4.8 的 Mono 3.0。 系統會提示您升級您 Mono、 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 利用它。

## <a name="using-async-amp-await"></a>使用 async &amp; await

 `async` 和`await`都是新 C# 語言功能搭配使用工作平行程式庫可簡化撰寫執行長時間執行工作，而不會封鎖您的應用程式的主執行緒的執行緒程式碼。

## <a name="async"></a>async

### <a name="declaration"></a>宣告

`async`關鍵字會放在方法宣告中 （或匿名方法或 lambda 上），表示它包含程式碼，以非同步的方式，可以執行 ie。 不會封鎖呼叫端執行緒。

以標記方法`async`應該至少包含一個 await 運算式或陳述式。 如果沒有`await`s 不存在的方法中，則它會以同步方式執行 (相同有沒有`async`修飾詞)。 這也會導致編譯器警告 （但不是錯誤）。

### <a name="return-types"></a>傳回型別

非同步方法應傳回`Task`，`Task<TResult>`或`void`。

指定`Task`傳回型別，如果方法未傳回任何其他值。

指定`Task<TResult>`如果方法必須傳回值，其中`TResult`所傳回的類型 (例如`int`，例如)。

`void`傳回類型主要用於將事件處理常式需要它。 呼叫傳回 void 的非同步方法的程式碼不能`await`的結果。

### <a name="parameters"></a>參數

非同步方法無法宣告`ref`或`out`參數。

## <a name="await"></a>await

Await 運算子可以套用至標示為非同步方法內的工作。 它會導致方法停止執行在該點，然後等候直到工作完成。

使用 await 不會封鎖呼叫端執行緒 – 而不是將控制權傳回給呼叫端。 這表示，不會封鎖呼叫執行緒，因此範例使用者介面執行緒將不會封鎖當等候的工作。

工作完成時，此方法會繼續執行程式碼中的相同位置。 這包括返回再試一次在區塊的範圍再試一次為 try-catch-finally （如果有的話）。 await 不能用在 catch 或 finally 區塊。

深入了解[await MSDN 上](http://msdn.microsoft.com/en-us/library/vstudio/hh156528.aspx)。

## <a name="exception-handling"></a>例外狀況處理

在非同步方法內發生的例外狀況會儲存在工作，並擲回的工作時`await`ed。 這些例外狀況可以捕捉及處理 try catch 區塊內。

## <a name="cancellation"></a>取消

需要很長的時間才能完成的非同步方法應支援取消。 一般而言，取消作業會叫用，如下所示：

- A`CancellationTokenSource`建立物件。
- `CancellationTokenSource.Token`執行個體傳遞給可取消的非同步方法。
- 藉由呼叫要求取消`CancellationTokenSource.Cancel`方法。

工作然後自行取消，並確認取消。

如需取消的詳細資訊，請參閱[如何取消非同步工作](http://msdn.microsoft.com/en-us/library/vstudio/jj155761.aspx)MSDN 上。

## <a name="example"></a>範例

下載[範例中的 Xamarin 方案](https://developer.xamarin.com/samples/mobile/AsyncAwait/)（適用於 iOS 和 Android） 的工作範例，請參閱`async`和`await`行動裝置應用程式。 本節中的更詳細地討論範例程式碼。

### <a name="writing-an-async-method"></a>撰寫非同步方法

下列方法將示範如何以程式碼`async`方法`await`ed 工作：

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("http://xamarin.com"); // async method!

    // await! control returns to the caller and the task continues to run on another thread
    string contents = await contentsTask;

    ResultEditText.Text += "DownloadHomepage method continues after async call. . . . .\n";

    // After contentTask completes, you can calculate the length of the string.
    int exampleInt = contents.Length;

    ResultEditText.Text += "Downloaded the html and found out the length.\n\n\n";

    ResultEditText.Text += contents; // just dump the entire HTML

    return exampleInt; // Task<TResult> returns an object of type TResult, in this case int
}
```

請注意下列重點：

-  方法宣告包含`async`關鍵字。
-  傳回型別是`Task<int>`讓呼叫的程式碼可存取`int`中這個方法計算的值。
-  Return 陳述式是`return exampleInt;`即整數物件 – 此方法會傳回事實`Task<int>`是語言改善功能的一部分。


### <a name="calling-an-async-method-1"></a>呼叫非同步方法 1

這個按鈕的 click 事件處理常式可以在 Android 範例應用程式呼叫上面所討論的方法：

```csharp
GetButton.Click += async (sender, e) => {

    Task<int> sizeTask = DownloadHomepage();

    ResultTextView.Text = "loading...";
    ResultEditText.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await sizeTask;

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultTextView.Text = "Length: " + intResult ;
    // "returns" void, since it's an event handler
};
```

附註：

-  匿名委派具有 async 關鍵字前置詞。
-  DownloadHomepage 的非同步方法傳回的工作<int>sizeTask 變數中儲存。
-  程式碼等候 sizeTask 變數上。  *這*方法會暫停，直到非同步工作完成其自己的執行緒上將控制權傳回給呼叫程式碼的位置。
-  執行沒有*不*時正在建立工作，儘管那里所建立的工作、 方法的第一行上暫停。 Await 關鍵字表示已暫停執行的位置。
-  非同步工作完成時，設定 intResult 並繼續，從 await 列在原始執行緒上執行。


### <a name="calling-an-async-method-2"></a>呼叫非同步方法 2

在 iOS 範例應用程式範例會寫入而有稍微不同示範的替代方式。 而是使用在匿名委派比這個範例會宣告`async`指派像一般事件處理常式的事件處理常式：

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

然後定義事件處理常式方法，如下所示：

```csharp
async void HandleTouchUpInside (object sender, EventArgs e)
{
    ResultLabel.Text = "loading...";
    ResultTextView.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await DownloadHomepage();

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultLabel.Text = "Length: " + intResult ;
}
```

一些重點：

-  方法會標示為`async`但傳回`void`。 這通常只是事件處理常式 (否則會傳回`Task`或`Task<TResult>`)。
-  程式碼`await`上的是 s`DownloadHomepage`方法直接在指派至變數 ( `intResult` ) 不同於前一個範例使用中繼`Task<int>`變數進行參考的工作。  *這*是其中將控制權傳回給呼叫端的非同步方法完成之前完成另一個執行緒的位置。
-  當非同步方法完成，並傳回時，繼續執行`await`表示整數結果會傳回並再呈現 UI widget 中。


## <a name="summary"></a>總結

使用 async 和 await 大幅簡化繁衍 （spawn） 而不會封鎖主執行緒的背景執行緒上的長時間執行作業所需的程式碼。 它們也讓您輕鬆存取工作已完成的結果。

這份文件已針對 Xamarin.iOS 和 Xamarin.Android 提供新的語言關鍵字和範例的概觀。



## <a name="related-links"></a>相關連結

- [AsyncAwait （範例）](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [為我們層代的回呼，請移至陳述式](http://tirania.org/blog/archive/2013/Aug-15.html)
- [資料 (iOS) （範例）](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) （範例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) （範例）](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [網路研討會： C# 非同步在 iOS 和 Android （影片）](http://xamarin.wistia.com/medias/k27mc627xz)
- [非同步程式設計使用 Async 和 Await (MSDN)](http://msdn.microsoft.com/en-us/library/vstudio/hh191443.aspx)
- [微調非同步應用程式 (MSDN)](http://msdn.microsoft.com/en-us/library/vstudio/jj155761.aspx)
- [等候，以及 UI 和死結 （deadlock） ！喔我 ！(MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx)
- [處理工作完成 (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2012/08/02/processing-tasks-as-they-complete.aspx)
- [工作式非同步模式 (TAP)](http://msdn.microsoft.com/en-us/library/hh873175.aspx)
- [C# 5 （Eric Lippert 部落格 –） 中的非同步有關關鍵字的簡介](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)

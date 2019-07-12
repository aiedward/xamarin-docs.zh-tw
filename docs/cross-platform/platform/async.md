---
title: 非同步支援概觀
description: 本文件描述程式設計使用 async 和 await 進行中, 導入的概念C#5，以輕鬆地撰寫非同步程式碼。
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: cca147f0c5dd1a217f464ffbed2a1ad2618c9b80
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830186"
---
# <a name="async-support-overview"></a>非同步支援概觀

_C#5 導入兩個關鍵字，來簡化非同步程式設計： async 和 await。這些關鍵字可讓您撰寫簡單的程式碼會使用工作平行程式庫來執行長時間執行的作業 （例如網路存取），另一個執行緒，並輕鬆地存取完成的結果。Xamarin.iOS 和 Xamarin.Android 的最新版本支援 async 和 await-本文提供說明和使用 Xamarin 的新語法的範例。_

Xamarin 的非同步支援 Mono 3.0 基礎上建置，並防止行動設備友善的 Silverlight 版本是.NET 4.5 的行動設備友善版本升級的 API 設定檔。

## <a name="overview"></a>總覽

這份文件導入了新的 async 和 await 關鍵字，然後透過一些簡單的範例實作非同步方法在 Xamarin.iOS 和 Xamarin.Android 會逐步引導。

如需新非同步功能的更完整討論C#5 （包括許多範例和不同的使用案例），請參閱文章[非同步程式設計](https://docs.microsoft.com/dotnet/csharp/async)。

範例應用程式進行簡單的非同步 web 要求 （而不會封鎖主執行緒），然後使用已下載的 html 和字元計數來更新 UI。

 [![](async-images/AsyncAwait_427x368.png "範例應用程式而不會封鎖主執行緒發出簡單的非同步 web 要求，然後使用已下載的 html 和字元計數來更新 UI")](async-images/AsyncAwait.png#lightbox)

Xamarin 的非同步支援 Mono 3.0 基礎上建置，並防止行動設備友善的 Silverlight 版本是.NET 4.5 的行動設備友善版本升級的 API 設定檔。

## <a name="requirements"></a>需求

C#5 個功能都需要包含在 Xamarin.iOS 6.4 和 Xamarin.Android 4.8 的 Mono 3.0。 系統會提示您升級您 Mono、 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 才會使用它。

## <a name="using-async-amp-await"></a>使用 async &amp; await

 `async` 並`await`熟悉C#搭配使用工作平行程式庫讓您輕鬆撰寫執行緒的程式碼來執行長時間執行的工作，而不會封鎖您的應用程式的主執行緒的語言功能。

## <a name="async"></a>async

### <a name="declaration"></a>宣告

`async`關鍵字會放在方法宣告中 （或在 lambda 或匿名方法），表示它包含以非同步的方式，可以執行的程式碼即。 不會封鎖呼叫端的執行緒。

以標記方法`async`應該至少包含一個 await 運算式或陳述式。 如果沒有`await`s 不存在於方法，則它會以同步方式執行 (相同好像沒有`async`修飾詞)。 這也會導致編譯器警告 （但不是錯誤）。

### <a name="return-types"></a>傳回型別

非同步方法應該傳回`Task`，`Task<TResult>`或`void`。

指定`Task`傳回型別，如果方法未傳回任何其他值。

指定`Task<TResult>`如果此方法需要傳回值，其中`TResult`所傳回的類型 (例如`int`，例如)。

`void`傳回型別主要用於將事件處理常式需要它。 呼叫傳回 void 的非同步方法的程式碼無法`await`的結果。

### <a name="parameters"></a>參數

非同步方法無法宣告`ref`或`out`參數。

## <a name="await"></a>await

Await 運算子可以套用至標示為非同步方法內的工作。 它會導致方法停止在該點執行，並等候工作完成。

使用 await 不會封鎖呼叫端執行緒 – 而不是控制回到呼叫端。 這表示，將不會封鎖呼叫執行緒，因此範例使用者介面執行緒將不會封鎖等候工作時。

工作完成時，此方法會繼續執行程式碼中的相同位置。 這包括回到 catch 的 try-finally 區塊的 try 範圍 （如果有的話）。 await 不能在 catch 或 finally 區塊。

深入了解[await](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/await) Microsoft 文件上。

## <a name="exception-handling"></a>例外狀況處理

在非同步方法內發生的例外狀況會儲存在工作和工作時，擲回`await`ed。 這些例外狀況可以捕捉及處理在 try / catch 區塊內。

## <a name="cancellation"></a>取消

需要很長的時間才能完成的非同步方法應支援取消。 一般而言，取消作業會叫用，如下所示：

- A`CancellationTokenSource`建立物件。
- `CancellationTokenSource.Token`執行個體傳遞給可取消的非同步方法。
- 藉由呼叫要求取消`CancellationTokenSource.Cancel`方法。

然後，工作會自行取消，並確認取消。

如需取消的詳細資訊，請參閱[微調非同步應用程式 (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)。

## <a name="example"></a>範例

下載[範例中的 Xamarin 方案](https://developer.xamarin.com/samples/mobile/AsyncAwait/)（適用於 iOS 和 Android） 的工作範例，請參閱`async`和`await`行動裝置應用程式。 在這一節中詳細討論的範例程式碼。

### <a name="writing-an-async-method"></a>撰寫非同步方法

下列方法示範如何撰寫程式碼`async`方法使用`await`ed 工作：

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
-  傳回的型別是`Task<int>`讓呼叫的程式碼可以存取`int`中這個方法，計算的值。
-  Return 陳述式是`return exampleInt;`這是整數物件 – 此方法會傳回事實`Task<int>`的語言改善功能的一部分。


### <a name="calling-an-async-method-1"></a>呼叫非同步方法 1

此按鈕的 click 事件處理常式可在 Android 範例應用程式呼叫上述方法：

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

-  匿名委派有 async 關鍵字前置詞。
-  DownloadHomepage 的非同步方法傳回的工作<int>sizeTask 變數中儲存。
-  SizeTask 變數上，等候程式碼。  *這*方法會暫停，直到非同步工作完成自己的執行緒上將控制項傳回給呼叫程式碼的位置。
-  執行會*不*暫停方法中，儘管那里所建立之工作的第一行上建立工作時。 Await 關鍵字表示已暫停執行的位置。
-  當非同步工作完成時，intResult 會設定，而且從 await 列的原始執行緒會繼續執行。


### <a name="calling-an-async-method-2"></a>呼叫非同步方法 2

IOS 範例應用程式中的範例會寫入方式稍有不同示範另一個方法。 而是不要使用匿名委派這個範例會宣告`async`指派等的一般事件處理常式的事件處理常式：

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

有一些重要事項：

-  方法會標示`async`但傳回`void`。 這通常只會進行事件處理常式 (否則您將會傳回`Task`或`Task<TResult>`)。
-  程式碼`await`上的是 s`DownloadHomepage`直接在指派值給變數上的方法 ( `intResult` ) 不同於前一個範例是使用中繼`Task<int>`變數來參考工作。  *這*是，程式控制權回到呼叫端的非同步方法完成之前另一個執行緒的位置。
-  當非同步方法完成，並傳回時，執行會繼續在`await`表示整數結果會傳回並再呈現 UI widget 中。


## <a name="summary"></a>總結

使用 async 和 await，大幅簡化了繁衍 （spawn） 而不會封鎖主執行緒的背景執行緒上的長時間執行作業所需的程式碼。 它們也容易存取結果，當工作完成為止。

這份文件已針對 Xamarin.iOS 和 Xamarin.Android 提供新的語言關鍵字和範例的概觀。



## <a name="related-links"></a>相關連結

- [AsyncAwait （範例）](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [為我們層代的回呼移至陳述式](https://tirania.org/blog/archive/2013/Aug-15.html)
- [資料 (iOS) （範例）](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) （範例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) （範例）](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [非同步程式設計](https://docs.microsoft.com/dotnet/csharp/async)
- [微調非同步應用程式 (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)
- [等候時，UI，以及死結 （deadlock） ！對了我 ！](https://devblogs.microsoft.com/pfxteam/await-and-ui-and-deadlocks-oh-my/)
- [它們完成時，處理工作）](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
- [工作式非同步模式 (TAP)](https://msdn.microsoft.com/library/hh873175.aspx)
- [在非同步C#5 （Eric Lippert 的部落格） – 相關關鍵字的簡介](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)

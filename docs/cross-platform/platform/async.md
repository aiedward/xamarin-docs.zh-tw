---
title: 非同步支援概觀
description: '本檔說明如何使用 async 和 await 進行程式設計，c # 5 中引進的概念可讓您更輕鬆地撰寫非同步程式碼。'
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 3bb2ba863913c2cc3098a2481ebd034c78eabdea
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938852"
---
# <a name="async-support-overview"></a>非同步支援概觀

_C # 5 引進了兩個關鍵字來簡化非同步程式設計： async 和 await。這些關鍵字可讓您撰寫簡單的程式碼，利用工作平行程式庫在另一個執行緒中執行長時間執行的作業（例如網路存取），並在完成時輕鬆地存取結果。最新版本的 Xamarin 和 Xamarin 支援 async 和 await-本檔提供使用新語法與 Xamarin 的說明和範例。_

Xamarin 的非同步支援建置於 Mono 3.0 基礎上，並將 API 設定檔從以行動方便的 Silverlight 版本升級為適合行動使用的 .NET 4.5 版本。

## <a name="overview"></a>概觀

本檔介紹新的 async 和 await 關鍵字，然後逐步解說一些在 Xamarin 和 Xamarin 中執行非同步方法的簡單範例。

如需更完整討論 c # 5 的新異步功能（包括許多範例和不同的使用案例），請參閱非同步程式[設計](https://docs.microsoft.com/dotnet/csharp/async)一文。

範例應用程式會進行簡單的非同步 web 要求（不會封鎖主執行緒），然後使用下載的 html 和字元計數來更新 UI。

 [![範例應用程式會進行簡單的非同步 web 要求，而不會封鎖主執行緒，然後使用下載的 html 和字元計數來更新 UI](async-images/AsyncAwait_427x368.png)](async-images/AsyncAwait.png#lightbox)

Xamarin 的非同步支援建置於 Mono 3.0 基礎上，並將 API 設定檔從以行動方便的 Silverlight 版本升級為適合行動使用的 .NET 4.5 版本。

## <a name="requirements"></a>需求

C # 5 功能需要包含在 Xamarin. iOS 6.4 和 Xamarin. Android 4.8 中的 Mono 3.0。 系統會提示您升級 Mono、Xamarin. iOS、Xamarin 和 Xamarin，以利用它。

## <a name="using-async-amp-await"></a>使用 async &amp; await

 `async`和 `await` 是新的 c # 語言功能，可與工作平行程式庫搭配使用，讓您輕鬆撰寫執行緒程式碼來執行長時間執行的工作，而不會封鎖應用程式的主要執行緒。

## <a name="async"></a>async

### <a name="declaration"></a>宣告

`async`關鍵字會放在方法宣告（或 lambda 或匿名方法），以表示它包含可以非同步執行的程式碼，亦即不封鎖呼叫端的執行緒。

標記為的方法 `async` 應該包含至少一個 await 運算式或語句。 如果方法中沒有任何語句，則 `await` 會同步執行（與沒有修飾詞相同 `async` ）。 這也會導致編譯器警告（但不是錯誤）。

### <a name="return-types"></a>傳回型別

非同步方法應該會傳回 `Task` 、 `Task<TResult>` 或 `void` 。

`Task`如果方法不會傳回任何其他值，請指定傳回類型。

指定 `Task<TResult>` 方法是否需要傳回值，其中 `TResult` 是要傳回的型別（例如 `int` ）。

傳回 `void` 型別主要用於需要它的事件處理常式。 呼叫傳回 void 之非同步方法的程式碼不能 `await` 出現在結果上。

### <a name="parameters"></a>參數

非同步方法不能宣告 `ref` 或 `out` 參數。

## <a name="await"></a>await

Await 運算子可以套用至標記為 async 之方法內的工作。 它會使方法在該時間點停止執行，並等候工作完成。

使用 await 並不會封鎖呼叫端的執行緒，而是會將控制權傳回給呼叫者。 這表示不會封鎖呼叫執行緒，例如，等候工作時，不會封鎖使用者介面執行緒。

當工作完成時，方法會繼續在程式碼中的同一個位置執行。 這包括返回 try-catch-finally 區塊的 try 範圍（如果有的話）。 await 不能用在 catch 或 finally 區塊中。

深入瞭解[await](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/await) on Microsoft Docs。

## <a name="exception-handling"></a>例外狀況處理

在非同步方法內發生的例外狀況會儲存在工作中，並在工作為 ed 時擲回 `await` 。 這些例外狀況可以在 try-catch 區塊內攔截和處理。

## <a name="cancellation"></a>取消

需要很長時間才能完成的非同步方法應可支援取消。 通常會叫用取消，如下所示：

- `CancellationTokenSource`建立物件。
- `CancellationTokenSource.Token`實例會傳遞至可取消非同步方法。
- 藉由呼叫方法來要求取消 `CancellationTokenSource.Cancel` 。

工作接著會取消自己並認可取消。

如需取消的詳細資訊，請參閱[微調非同步應用程式 (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)。

## <a name="example"></a>範例

下載[範例 Xamarin 解決方案](https://docs.microsoft.com/samples/xamarin/mobile-samples/asyncawait/)（適用于 IOS 和 Android），以在行動裝置 `async` 應用程式中查看和的實用範例 `await` 。 本章節將更詳細地討論範例程式碼。

### <a name="writing-an-async-method"></a>撰寫非同步方法

下列方法示範如何使用 ed 工作來撰寫方法的程式碼 `async` `await` ：

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("https://visualstudio.microsoft.com/xamarin"); // async method!

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

請注意下列幾點：

- 方法宣告包含 `async` 關鍵字。
- 傳回型別是 `Task<int>` ，因此呼叫程式碼可以存取 `int` 在這個方法中計算的值。
- Return 語句是 `return exampleInt;` 一個整數物件–方法傳回的事實 `Task<int>` 是語言改善的一部分。

### <a name="calling-an-async-method-1"></a>呼叫非同步方法1

您可以在 Android 範例應用程式中找到此按鈕 click 事件處理常式，以呼叫上面討論的方法：

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

注意：

- 匿名委派具有 async 關鍵字前置詞。
- 非同步方法 DownloadHomepage \<int> 會傳回儲存在 sizeTask 變數中的工作。
- 程式碼會等候 sizeTask 變數。  *這*是方法暫止的位置，控制權會傳回給呼叫程式碼，直到非同步工作在自己的執行緒上完成為止。
- 當工作在方法的第一行建立時，*執行不會暫停，* 而是在其中建立工作。 Await 關鍵字表示暫停執行的位置。
- 當非同步工作完成時，會從 await 行設定 intResult 並繼續執行原始執行緒。

### <a name="calling-an-async-method-2"></a>呼叫非同步方法2

在 iOS 範例應用程式中，範例的撰寫方式稍有不同，以示範替代方法。 這個範例不會使用匿名委派，而是宣告 `async` 指派為一般事件處理常式的事件處理常式：

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

接著會定義事件處理常式方法，如下所示：

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

- 方法已標記為， `async` 但傳回 `void` 。 這通常只會針對事件處理常式完成（否則，您會傳回 `Task` 或 `Task<TResult>` ）。
- `await`方法上的關鍵字會 `DownloadHomepage` 直接指派給變數（ `intResult` ），與先前的範例不同，我們使用了中繼 `Task<int>` 變數來參考工作。  *這*是在非同步方法于另一個執行緒上完成之前，將控制權傳回給呼叫者的位置。
- 當非同步方法完成並傳回時，會繼續執行， `await` 這表示會傳回整數結果，然後在 UI widget 中呈現。

## <a name="summary"></a>總結

使用 async 和 await 可大幅簡化在背景執行緒上產生長時間執行作業所需的程式碼，而不會封鎖主執行緒。 當工作完成時，它們也可讓您輕鬆地存取結果。

本檔概述了適用于 Xamarin 和 Xamarin 的新語言關鍵字和範例。

## <a name="related-links"></a>相關連結

- [AsyncAwait （範例）](https://docs.microsoft.com/samples/xamarin/mobile-samples/asyncawait/)
- [回呼做為世代的 Go To 語句](https://tirania.org/blog/archive/2013/Aug-15.html)
- [資料（iOS）（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/data/)
- [HttpClient （iOS）（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/httpclient/)
- [MapKitSearch （iOS）（範例）](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [非同步程式設計](https://docs.microsoft.com/dotnet/csharp/async)
- [微調非同步應用程式 (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)
- [Await 和 UI，以及鎖死！哦！](https://devblogs.microsoft.com/pfxteam/await-and-ui-and-deadlocks-oh-my/)
- [完成時處理工作）](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
- [以工作為基礎的非同步模式 (TAP)](https://msdn.microsoft.com/library/hh873175.aspx)
- [C # 5 中的非同步（Eric Lippert 的 blog）–關於關鍵字的引進](https://blogs.msdn.microsoft.com/ericlippert/2010/11/11/asynchrony-in-c-5-part-six-whither-async/)

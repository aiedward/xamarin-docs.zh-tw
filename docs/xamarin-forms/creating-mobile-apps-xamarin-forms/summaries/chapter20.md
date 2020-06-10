---
title： "第20章的摘要。 非同步和檔案 i/o 「描述：」使用下列方式建立 Mobile Apps Xamarin.Forms ：第20章的摘要。 Async 和 file i/o "ms-chap： xamarin ms. 技術： assetid： D595862D-64FD-4C0D-B0AD-C1F440564247 author： davidbritch ms-chap： dabritch ms. date： 07/18/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-20-async-and-file-io"></a>第20章的摘要。 非同步與檔案 I/O

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

 圖形化使用者介面必須依序回應使用者輸入事件。 這表示使用者輸入事件的所有處理都必須發生在單一執行緒中，通常稱為*主要執行緒*或*UI 執行緒*。

使用者希望圖形使用者介面能夠回應。 這表示程式必須快速處理使用者輸入事件。 如果無法這麼做，則必須將處理常式歸入執行的次要執行緒。

本書中有數個範例程式已使用 [`WebRequest`](xref:System.Net.WebRequest) 類別。 在此類別中， [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) 方法會啟動背景工作執行緒，以在完成時呼叫回呼函數。 不過，該回呼函式會在背景工作執行緒中執行，因此程式必須呼叫 [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) 方法來存取使用者介面。

> [!NOTE]
> Xamarin.Forms程式應該使用， [`HttpClient`](xref:System.Net.Http.HttpClient) 而不是透過 [`WebRequest`](xref:System.Net.WebRequest) 網際網路存取檔案。 `HttpClient`支援非同步作業。

.NET 和 c # 提供非同步處理的更現代化方法。 這牽涉到 [`Task`](xref:System.Threading.Tasks.Task) 和 [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) 類別，以及和命名空間中的其他類型 [`System.Threading`](xref:System.Threading) [`System.Threading.Tasks`](xref:System.Threading.Tasks) ，以及 c # 5.0 `async` 和 `await` 關鍵字。 這就是這一章的重點。

## <a name="from-callbacks-to-await"></a>從回呼到 await

`Page`類別本身包含三個可顯示警示方塊的非同步方法：

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))傳回 `Task` 物件
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String))傳回 `Task<bool>` 物件
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))傳回 `Task<string>` 物件

`Task`物件指出這些方法會實作為以工作為基礎的非同步模式，稱為「點按」。 這些 `Task` 物件會從方法快速傳回。 傳回 `Task<T>` 值會構成「承諾」， `TResult` 當工作完成時，類型的值將可供使用。 `Task`傳回值表示非同步動作，將會完成，但不會傳回任何值。

在所有這些情況下， `Task` 當使用者關閉警示方塊時，就會完成。  

### <a name="an-alert-with-callbacks"></a>具有回呼的警示

[**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks)範例會示範如何 `Task<bool>` 使用回呼方法來處理傳回物件和 `Device.BeginInvokeOnMainThread` 呼叫。

### <a name="an-alert-with-lambdas"></a>使用 lambda 的警示

[**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas)範例會示範如何使用匿名 lambda 函數來處理 `Task` 和 `Device.BeginInvokeOnMainThread` 呼叫。  

### <a name="an-alert-with-await"></a>使用 await 的警示

更直接的方法牽涉到 `async` `await` c # 5 中引進的和關鍵字。 [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait)範例會示範其用法。

### <a name="an-alert-with-nothing"></a>沒有任何內容的警示

如果非同步方法傳回 `Task` ，而不是 `Task<TResult>` ，則程式不需要在非同步工作完成時使用任何這些技術。 [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert)範例會示範這種情況。

### <a name="saving-program-settings-asynchronously"></a>以非同步方式儲存程式設定

[**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings)範例會示範如何使用的 [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) 方法 `Application` 來儲存程式設定，而不需要覆寫 `OnSleep` 方法。

### <a name="a-platform-independent-timer"></a>與平臺無關的計時器

您可以使用建立與 [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) 平臺無關的計時器。 [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock)範例會示範這種情況。

## <a name="file-inputoutput"></a>檔案輸入/輸出

傳統上，.NET [`System.IO`](xref:System.IO) 命名空間已經是檔案 i/o 支援的來源。 雖然此命名空間中的某些方法支援非同步作業，但大部分的情況下都是如此。 命名空間也支援數個可執行複雜檔案 i/o 功能的簡單方法呼叫。

### <a name="good-news-and-bad-news"></a>好消息和壞消息

支援的所有平臺都支援應用程式專用的應用 Xamarin.Forms 程式本機儲存體 &mdash; 儲存體。

Xamarin 和 Xamarin 程式庫包含 Xamarin 已針對這兩種平臺明確量身打造的 .NET 版本。 這些包含的類別 `System.IO` ，可讓您用來在這兩個平臺上使用應用程式本機儲存體來執行檔案 i/o。

不過，如果您 `System.IO` 在 PCL 中搜尋這些類別 Xamarin.Forms ，就找不到它們。 問題在於，Microsoft 完全改頭換面了 Windows 執行階段 API 的檔案 i/o。 以 Windows 8.1、Windows Phone 8.1 和通用 Windows 平臺為目標的程式，不適 `System.IO` 用於檔案 i/o。

這表示您必須使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) （第9章中所討論的內容） [**。**](chapter09.md)用來執行檔案 i/o 的平臺特定 API 呼叫。

> [!NOTE]
> 可移植的類別 Libaries 已被 .NET Standard 2.0 程式庫取代，.NET Standard 2.0 支援 [`System.IO`](xref:System.IO) 所有平臺的類型 Xamarin.Forms 。 您不再需要 `DependencyService` 針對大部分的檔案 i/o 工作使用。 如需更現代化的檔案 i/o 方法，請參閱[中 Xamarin.Forms ](~/xamarin-forms/data-cloud/data/files.md)的檔案處理。

### <a name="a-first-shot-at-cross-platform-file-io"></a>跨平臺檔案 i/o 的第一次快照

[**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout)範例會定義檔案 [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) i/o 的介面，以及所有平臺中此介面的執行。 不過，Windows 執行階段的執行不會使用此介面中的方法，因為 Windows 執行階段檔案 i/o 方法是非同步。

### <a name="accommodating-windows-runtime-file-io"></a>容納 Windows 執行階段檔案 i/o

在 Windows 執行階段之下執行的程式會使用 [`Windows.Storage`](/uwp/api/Windows.Storage) 和命名空間中的類別 [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) 來取得檔案 i/o，包括應用程式本機儲存體。 因為 Microsoft 判斷任何需要超過50毫秒的作業都應該是非同步，以避免封鎖 UI 執行緒，所以這些檔案 i/o 方法大多是非同步。

示範這個新方法的程式碼將會在程式庫中，讓其他應用程式可以使用它。

## <a name="platform-specific-libraries"></a>平台特定程式庫

將可重複使用的程式碼儲存在程式庫中是很有利的。 當可重複使用程式碼的不同部分適用于完全不同的作業系統時，這顯然會比較棘手。

[**FormsBook 平臺**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案示範了一種方法。 此解決方案包含七個不同的專案：

- [**FormsBook 平臺**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform)，一般 Xamarin.Forms PCL
- [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS)是 ios 類別庫，
- [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android)，這是 android 類別庫
- [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP)，這是通用 Windows 類別庫
- [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT)，這是所有 Windows 平臺通用的程式碼共用專案

所有個別平臺專案（但**FormsBook**除外）都有**FormsBook**的參考。」（可能為平臺）。 這三個 Windows 專案具有**FormsBook**的參考。

所有專案都包含靜態 `Toolkit.Init` 方法，以確保在應用程式方案中的專案未直接參考程式庫時，會載入該程式庫 Xamarin.Forms 。

**FormsBook 平臺**專案包含新的 [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) 介面。 所有方法現在都有 `Async` 尾碼和傳回物件的名稱 `Task` 。

**FormsBook**的專案包含 [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) Windows 執行階段的類別（class）。

**FormsBook**的 ios 專案包含 [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) 適用于 iOS 的類別。 這些方法現在必須是非同步。 某些方法會使用和中所定義之方法的非同步 `StreamWriter` 版本 `StreamReader` ： [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) 和 [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync) 。 其他則會使用方法，將結果轉換成 `Task` 物件 [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) 。

**FormsBook**專案包含類似于 [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) android 的類別。

**FormsBook 平臺**專案也包含一個 [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) 可簡化物件使用的類別 `DependencyService` 。

若要使用這些程式庫，應用程式方案必須在**FormsBook 平臺**方案中包含所有專案，而且每個應用程式專案都必須具有**FormsBook**中對應程式庫的參考。

[**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync)解決方案會示範如何使用**FormsBook 平臺**程式庫。 每個專案都有對的呼叫 `Toolkit.Init` 。 應用程式會使用非同步檔案 i/o 函數。

### <a name="keeping-it-in-the-background"></a>將它保留在背景中

程式庫中呼叫多個非同步方法 &mdash; （例如 `WriteFileAsync` Windows 執行階段類別中的和方法）的方法， `ReadFileASync` [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) &mdash; 可以使用 [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) 方法來避免切換回使用者介面執行緒，這樣做會更有效率。

### <a name="dont-block-the-ui-thread"></a>不要封鎖 UI 執行緒！

有時候，您可以在 `ContinueWith` `await` 方法上使用屬性來避免使用或，這是很吸引人的 [`Result`](xref:System.Threading.Tasks.Task`1.Result) 。 應該避免這種情況，它可以封鎖 UI 執行緒，甚至讓應用程式停止回應。

## <a name="your-own-awaitable-methods"></a>您自己的可等候方法

您可以透過將程式碼傳遞給其中一個方法，以非同步方式執行 [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) 。 您可以 `Task.Run` 在處理一些額外負荷的非同步方法中呼叫。

以下將討論各種不同的 `Task.Run` 模式。

### <a name="the-basic-mandelbrot-set"></a>基本 Mandelbrot 集

若要即時繪製 Mandelbrot 集，則為[** Xamarin.Forms 。工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)組程式庫具有 [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) 類似命名空間中的結構 `System.Numerics` 。

[**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet)範例 `CalculateMandeblotAsync` 在其程式碼後置檔案中有一個方法，其會計算基本的黑白 Mandelbrot 集，並使用將 [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) 它放在點陣圖上。

### <a name="marking-progress"></a>標示進度

若要從非同步方法報告進度，您可以具現化 [`Progress<T>`](xref:System.Progress`1) 類別，並定義非同步方法，使其具有類型的引數 [`IProgress<T>`](xref:System.IProgress`1) 。 這會在[**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)範例中示範。

### <a name="cancelling-the-job"></a>正在取消工作

您也可以撰寫要可取消的非同步方法。 您一開始會使用名為的類別 [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource) 。 [`Token`](xref:System.Threading.CancellationTokenSource.Token)屬性是類型的值 [`CancellationToken`](xref:System.Threading.CancellationToken) 。 這會傳遞至非同步函式。 程式會呼叫的 [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) 方法 `CancellationTokenSource` （通常是為了回應使用者所採取的動作）來取消非同步函式。

非同步方法可以定期檢查的 [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) 屬性， `CancellationToken` 並在屬性為時 `true` 結束，或只是呼叫 [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) 方法，在此情況下，方法會以結尾 [`OperationCancelledException`](xref:System.OperationCanceledException) 。

[**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation)範例示範如何使用可取消函數。

### <a name="an-mvvm-mandelbrot"></a>MVVM Mandelbrot

[**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF)範例有更廣泛的使用者介面，而且大部分都是以 [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) 和類別為基礎 [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) ：

[![Mandelbrot X F 的三向螢幕擷取畫面](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>回到網路

[`WebRequest`](xref:System.Net.WebRequest)在某些範例中使用的類別會使用稱為非同步程式設計模型或 APM 的舊式非同步通訊協定。 您可以使用類別中的其中一個方法，將這類類別轉換成現代化的分路通訊協定 `FromAsync` [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) 。 [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap)範例會示範這種情況。

## <a name="related-links"></a>相關連結

- [第20章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [第20章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [處理檔案](~/xamarin-forms/data-cloud/data/files.md)

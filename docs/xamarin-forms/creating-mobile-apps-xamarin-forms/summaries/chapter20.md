---
title: 第20章的摘要。 Async 和 file i/o
description: 使用 Xamarin 建立 Mobile Apps：第20章的摘要。 Async 和 file i/o
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771044"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>第20章的摘要。 Async 和 file i/o

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> 此頁面上的附注指出 Xamarin 從書籍中呈現的材料中分歧的區域。

 圖形化使用者介面必須依序回應使用者輸入事件。 這表示使用者輸入事件的所有處理都必須發生在單一執行緒中，通常稱為*主要執行緒*或*UI 執行緒*。

使用者希望圖形使用者介面能夠回應。 這表示程式必須快速處理使用者輸入事件。 如果無法這麼做，則必須將處理常式歸入執行的次要執行緒。

本書中的數個範例程式已使用[`WebRequest`](xref:System.Net.WebRequest)類別。 在此類別中， [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object))方法會啟動背景工作執行緒，在完成時呼叫回呼函數。 不過，該回呼函式會在背景工作執行緒中執行，因此程式必須呼叫[`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法，才能存取使用者介面。

> [!NOTE]
> Xamarin 程式應該使用[`HttpClient`](xref:System.Net.Http.HttpClient) ，而不是透過網際網路存取檔案的[`WebRequest`](xref:System.Net.WebRequest) 。 `HttpClient` 支援非同步作業。

.NET 和C#中有提供非同步處理的更現代化方法。 這牽涉到[`Task`](xref:System.Threading.Tasks.Task)和[`Task<TResult>`](xref:System.Threading.Tasks.Task`1)類別，以及[`System.Threading`](xref:System.Threading)和[`System.Threading.Tasks`](xref:System.Threading.Tasks)命名空間中的其他類型，以及C# 5.0 `async` 和 `await` 關鍵字。 這就是這一章的重點。

## <a name="from-callbacks-to-await"></a>從回呼到 await

`Page` 類別本身包含三個可顯示警示方塊的非同步方法：

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))會傳回 `Task` 物件
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String))會傳回 `Task<bool>` 物件
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))會傳回 `Task<string>` 物件

`Task` 物件指出這些方法會實作為以工作為基礎的非同步模式，稱為「點按」。 這些 `Task` 的物件會從方法快速傳回。 `Task<T>` 傳回值會構成「承諾」，當工作完成時，`TResult` 類型的值將可供使用。 `Task` 傳回值表示將會完成，但未傳回任何值的非同步動作。

在所有這些情況下，當使用者關閉 [警示] 方塊時，`Task` 就完成了。  

### <a name="an-alert-with-callbacks"></a>具有回呼的警示

[**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks)範例會示範如何使用回呼方法來處理 `Task<bool>` 傳回物件和 `Device.BeginInvokeOnMainThread` 呼叫。

### <a name="an-alert-with-lambdas"></a>使用 lambda 的警示

[**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas)範例會示範如何使用匿名 lambda 函數來處理 `Task` 和 `Device.BeginInvokeOnMainThread` 呼叫。  

### <a name="an-alert-with-await"></a>使用 await 的警示

更直接的方法牽涉到在5中C#引進的 `async` 和 `await` 關鍵字。 [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait)範例會示範其用法。

### <a name="an-alert-with-nothing"></a>沒有任何內容的警示

如果非同步方法傳回 `Task` 而不是 `Task<TResult>`，則程式不需要在非同步工作完成時使用任何這些技術。 [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert)範例會示範這種情況。

### <a name="saving-program-settings-asynchronously"></a>以非同步方式儲存程式設定

[**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings)範例會示範如何使用 `Application` 的[`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync)方法，在程式設定變更時加以儲存，而不會覆寫 `OnSleep` 方法。

### <a name="a-platform-independent-timer"></a>與平臺無關的計時器

您可以使用[`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32))來建立與平臺無關的計時器。 [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock)範例會示範這種情況。

## <a name="file-inputoutput"></a>檔案輸入/輸出

傳統上，.NET [`System.IO`](xref:System.IO)命名空間是檔案 i/o 支援的來源。 雖然此命名空間中的某些方法支援非同步作業，但大部分的情況下都是如此。 命名空間也支援數個可執行複雜檔案 i/o 功能的簡單方法呼叫。

### <a name="good-news-and-bad-news"></a>好消息和壞消息

Xamarin 支援的所有平臺都支援應用程式的本機儲存體 &mdash; 的儲存體，這是應用程式的私用。

Xamarin 和 Xamarin 程式庫包含 Xamarin 已針對這兩種平臺明確量身打造的 .NET 版本。 其中包括來自 `System.IO` 的類別，您可以在這兩個平臺中用來以應用程式本機儲存體執行檔案 i/o。

不過，如果您在 Xamarin. Forms PCL 中搜尋這些 `System.IO` 類別，就找不到它們。 問題在於，Microsoft 完全改頭換面了 Windows 執行階段 API 的檔案 i/o。 以 Windows 8.1、Windows Phone 8.1 和通用 Windows 平臺為目標的程式，不會使用 `System.IO` 進行檔案 i/o。

這表示您必須使用[`DependencyService`](xref:Xamarin.Forms.DependencyService) （第9章中會先討論過） [ **。** ](chapter09.md)用來執行檔案 i/o 的平臺特定 API 呼叫。

> [!NOTE]
> 可移植的類別 Libaries 已被 .NET Standard 2.0 程式庫取代，.NET Standard 2.0 支援所有 Xamarin 的[`System.IO`](xref:System.IO)類型。 您不再需要針對大部分的檔案 i/o 工作使用 `DependencyService`。 如需更現代化的檔案 i/o 方法，請參閱[Xamarin 中](~/xamarin-forms/data-cloud/data/files.md)的檔案處理。

### <a name="a-first-shot-at-cross-platform-file-io"></a>跨平臺檔案 i/o 的第一次快照

[**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout)範例會定義檔案 i/o 的[`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs)介面，以及所有平臺中此介面的執行。 不過，Windows 執行階段的執行不會使用此介面中的方法，因為 Windows 執行階段檔案 i/o 方法是非同步。

### <a name="accommodating-windows-runtime-file-io"></a>容納 Windows 執行階段檔案 i/o

在 Windows 執行階段之下執行的程式會使用[`Windows.Storage`](/uwp/api/Windows.Storage)中的類別和檔案 i/o 的[`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams)命名空間，包括應用程式本機儲存體。 因為 Microsoft 判斷任何需要超過50毫秒的作業都應該是非同步，以避免封鎖 UI 執行緒，所以這些檔案 i/o 方法大多是非同步。

示範這個新方法的程式碼將會在程式庫中，讓其他應用程式可以使用它。

## <a name="platform-specific-libraries"></a>平台特定程式庫

將可重複使用的程式碼儲存在程式庫中是很有利的。 當可重複使用程式碼的不同部分適用于完全不同的作業系統時，這顯然會比較棘手。

[**FormsBook 平臺**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案示範了一種方法。 此解決方案包含七個不同的專案：

- [**FormsBook 平臺**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform)，一般 Xamarin. 表單 PCL
- [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS)是 ios 類別庫，
- [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android)，這是 android 類別庫
- [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP)，這是通用 Windows 類別庫
- [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT)，這是所有 Windows 平臺通用的程式碼共用專案

所有個別平臺專案（但**FormsBook**除外）都有**FormsBook**的參考。」（可能為平臺）。 這三個 Windows 專案具有**FormsBook**的參考。

所有專案都包含靜態 `Toolkit.Init` 方法，以確保如果程式庫不是由 Xamarin. Forms 應用程式方案中的專案直接參考，則會載入。

**FormsBook 平臺**專案包含新的[`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs)介面。 所有方法現在都具有 `Async` 尾碼的名稱，並傳回 `Task` 物件。

**FormsBook**的專案包含 Windows 執行階段的[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)類別。

**FormsBook** ios 專案包含適用于 iOS 的[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs)類別。 這些方法現在必須是非同步。 某些方法會使用 `StreamWriter` 和 `StreamReader`中定義之方法的非同步版本： [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String))和[`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync)。 有些人會使用[`FromResult`](xref:System.Threading.Tasks.Task.FromResult*)方法，將結果轉換成 `Task` 物件。

**FormsBook**專案包含類似于 android 的[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs)類別。

**FormsBook 平臺**專案也包含[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs)類別，可讓您更輕鬆地使用 `DependencyService` 物件。

若要使用這些程式庫，應用程式方案必須在**FormsBook 平臺**方案中包含所有專案，而且每個應用程式專案都必須具有**FormsBook**中對應程式庫的參考。

[**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync)解決方案會示範如何使用**FormsBook 平臺**程式庫。 每個專案都有 `Toolkit.Init`的呼叫。 應用程式會使用非同步檔案 i/o 函數。

### <a name="keeping-it-in-the-background"></a>將它保留在背景中

程式庫中呼叫多個非同步方法的方法 &mdash; 例如 Windows 執行階段[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)類別 &mdash; 中的 `WriteFileAsync` 和 `ReadFileASync` 方法，可以更有效率地使用[`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean))方法，以避免切換回使用者介面執行緒。

### <a name="dont-block-the-ui-thread"></a>不要封鎖 UI 執行緒！

有時候，您可以使用方法上的[`Result`](xref:System.Threading.Tasks.Task`1.Result)屬性來避免使用 `ContinueWith` 或 `await`。 應該避免這種情況，它可以封鎖 UI 執行緒，甚至讓應用程式停止回應。

## <a name="your-own-awaitable-methods"></a>您自己的可等候方法

您可以透過將程式碼傳遞給其中一個[`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action))方法，以非同步方式執行。 您可以在處理一些額外負荷的非同步方法中呼叫 `Task.Run`。

以下將討論各種不同的 `Task.Run` 模式。

### <a name="the-basic-mandelbrot-set"></a>基本 Mandelbrot 集

若要即時繪製 Mandelbrot 集，[Xamarin][**工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)組程式庫具有類似于 `System.Numerics` 命名空間中的[`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs)結構。

[**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet)範例在其程式碼後置檔案中有一個 `CalculateMandeblotAsync` 方法，其會計算基本的黑白 Mandelbrot 集，並使用[`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)將它放在點陣圖上。

### <a name="marking-progress"></a>標示進度

若要從非同步方法報告進度，您可以將[`Progress<T>`](xref:System.Progress`1)類別具現化，並定義非同步方法，使其具有[`IProgress<T>`](xref:System.IProgress`1)類型的引數。 這會在[**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)範例中示範。

### <a name="cancelling-the-job"></a>正在取消工作

您也可以撰寫要可取消的非同步方法。 您一開始會使用名為[`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource)的類別。 [`Token`](xref:System.Threading.CancellationTokenSource.Token)屬性是[`CancellationToken`](xref:System.Threading.CancellationToken)類型的值。 這會傳遞至非同步函式。 程式會呼叫 `CancellationTokenSource` 的[`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel)方法（通常是為了回應使用者的動作），以取消非同步函式。

非同步方法可以定期檢查 `CancellationToken` 的[`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested)屬性，並在屬性 `true`時結束，或直接呼叫[`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested)方法，在此情況下，方法會以[`OperationCancelledException`](xref:System.OperationCanceledException)做為結尾。

[**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation)範例示範如何使用可取消函數。

### <a name="an-mvvm-mandelbrot"></a>MVVM Mandelbrot

[**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF)範例有更廣泛的使用者介面，而且大部分都是以[`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs)和[`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)類別為基礎：

[![Mandelbrot X F 的三向螢幕擷取畫面](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>回到網路

在某些範例中使用的[`WebRequest`](xref:System.Net.WebRequest)類別會使用稱為非同步程式設計模型或 APM 的舊式非同步通訊協定。 您可以使用[`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1)類別中的其中一種 `FromAsync` 方法，將這類類別轉換成新式的分路通訊協定。 [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap)範例會示範這種情況。

## <a name="related-links"></a>相關連結

- [第20章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [第20章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [使用檔案](~/xamarin-forms/data-cloud/data/files.md)

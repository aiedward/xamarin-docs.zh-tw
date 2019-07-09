---
title: 第 20 章的摘要。 非同步與檔案 I/O
description: 使用 Xamarin.Forms 建立行動應用程式：第 20 章的摘要。 非同步與檔案 I/O
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: cf870cebfdd29e679b767728d1bea723f9310b17
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650457"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>第 20 章的摘要。 非同步與檔案 I/O

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> 在此頁面上的附註表示其中 Xamarin.Forms 有分歧活頁簿中所呈現的題材的區域。

 圖形化使用者介面必須以循序方式回應使用者輸入事件。 這表示所有使用者輸入事件的處理必須都存在於單一執行緒，通常稱為*主執行緒*或*UI 執行緒*。

使用者會預期有回應的圖形化使用者介面。 這表示程式必須快速處理使用者輸入事件。 如果不可行，然後處理必須是屈就將文件執行的次要執行緒。

這個活頁簿中的數個範例程式已經使用[ `WebRequest` ](xref:System.Net.WebRequest)類別。 此類別中[ `BeginGetResponse` ](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object))方法會啟動背景工作執行緒，完成時呼叫的回呼函式。 不過，該回撥函式以執行背景工作執行緒，因此，程式必須呼叫[ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))存取使用者介面的方法。

> [!NOTE]
> Xamarin.Forms 程式應該使用[ `HttpClient` ](xref:System.Net.Http.HttpClient)而非[ `WebRequest` ](xref:System.Net.WebRequest)透過網際網路存取的檔案。 `HttpClient` 支援非同步作業。

使用.NET 和 C# 中以更現代化的方法來非同步處理。 這牽涉到[ `Task` ](xref:System.Threading.Tasks.Task)並[ `Task<TResult>` ](xref:System.Threading.Tasks.Task`1)類別和其他類型中的[ `System.Threading` ](xref:System.Threading)並[ `System.Threading.Tasks` ](xref:System.Threading.Tasks)命名空間，並將C#5.0`async`並`await`關鍵字。 這就是這一章的重點。

## <a name="from-callbacks-to-await"></a>從 await 的回撥

`Page`類別本身包含三個非同步方法，可顯示警示的方塊：

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 傳回`Task`物件
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) 傳回`Task<bool>`物件
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) 傳回`Task<string>`物件

`Task`物件會指出這些方法會實作工作式非同步模式，稱為點選。 這些`Task`快速地從方法傳回物件。 `Task<T>`傳回值構成 「 承諾 」 的型別值`TResult`將可在工作完成時。 `Task`傳回值表示非同步動作的會完成，但不是含值傳回。

在這些情況下，`Task`已完成，當使用者關閉的警示方塊。  

### <a name="an-alert-with-callbacks"></a>具有回呼的警示

[ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks)範例示範如何處理`Task<bool>`傳回的物件和`Device.BeginInvokeOnMainThread`使用回呼方法的呼叫。

### <a name="an-alert-with-lambdas"></a>使用 lambda 警示

[ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas)範例示範如何使用匿名 lambda 函式來處理`Task`和`Device.BeginInvokeOnMainThread`呼叫。  

### <a name="an-alert-with-await"></a>使用 await 警示

更直接的方法涉及`async`和`await`關鍵字在 C# 5 中引進。 [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait)範例會示範其使用。

### <a name="an-alert-with-nothing"></a>沒有警示

如果非同步方法傳回`Task`而非`Task<TResult>`，則程式不需要使用任何一種方法，如果它不需要知道當非同步工作完成時。 [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert)為範例。

### <a name="saving-program-settings-asynchronously"></a>儲存計劃設定以非同步的方式

[ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings)範例示範如何使用[ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync)方法`Application`儲存程式的設定，而不需要變更時覆寫`OnSleep`方法。

### <a name="a-platform-independent-timer"></a>平台無關的計時器

您可使用[ `Task.Delay` ](xref:System.Threading.Tasks.Task.Delay(System.Int32))來建立獨立於平台的計時器。 [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock)為範例。

## <a name="file-inputoutput"></a>檔案輸入/輸出

傳統上，.NET [ `System.IO` ](xref:System.IO)命名空間已經過的檔案 I/O 支援的來源。 雖然此命名空間中的某些方法支援非同步作業，大部分則否。 命名空間也支援執行複雜的檔案 I/O 函式的數個簡單的方法呼叫。

### <a name="good-news-and-bad-news"></a>好消息和壞消息

Xamarin.Forms 支援應用程式本機儲存體所支援的平台&mdash;是私用應用程式的儲存體。

Xamarin.iOS 和 Xamarin.Android 程式庫包含 Xamarin 已明確為量身訂作這些兩個平台的.NET 的版本。 其中包括從類別`System.IO`可用來在這些兩個平台上執行檔案 I/O 與應用程式的本機儲存體。

不過，如果您搜尋這些`System.IO`Xamarin.Forms PCL 中的類別，您將找不到它們。 問題是 Microsoft 完全改寫 file I/O 適用於 Windows 執行階段 API。 以 Windows 8.1、 Windows Phone 8.1 和通用 Windows 平台為目標的程式並不使用`System.IO`針對檔案 I/O。

這表示您必須使用[ `DependencyService` ](xref:Xamarin.Forms.DependencyService) (先討論[**第 9 章。平台特定 API 呼叫**](chapter09.md)實作檔案 I/O。

> [!NOTE]
> 可攜式類別庫已取代為.NET Standard 2.0 程式庫，並支援.NET Standard 2.0 [ `System.IO` ](xref:System.IO)適用於所有的 Xamarin.Forms 平台的型別。 已不再需要使用`DependencyService`大部分的檔案 I/O 工作。 請參閱[Xamarin.Forms 中的檔案處理](~/xamarin-forms/data-cloud/data/files.md)檔案 I/O 以更現代化的方法。

### <a name="a-first-shot-at-cross-platform-file-io"></a>在跨平台檔案 I/O 的第一個擷取畫面

[ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout)範例會定義[ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs)檔案 I/O，以及在所有平台上的這個介面的實作的介面。 不過，Windows 執行階段實作不適用這個介面中的方法，因為 Windows 執行階段檔案 I/O 方法為非同步。

### <a name="accommodating-windows-runtime-file-io"></a>配合 Windows 執行階段檔案 I/O

Windows 執行階段下執行的程式使用中的類別[ `Windows.Storage` ](/uwp/api/Windows.Storage)並[ `Windows.Storage.Streams` ](/uwp/api/Windows.Storage.Streams)檔案 I/O，包括應用程式的本機儲存體的命名空間。 Microsoft 認為需要超過 50 毫秒應該以非同步方式來避免封鎖 UI 執行緒的任何作業，因為這些檔案 I/O 方法大部分是非同步的。

示範這個新方法的程式碼會在文件庫中，供其他應用程式。

## <a name="platform-specific-libraries"></a>平台特定程式庫

建議您使用可重複使用的程式碼儲存在程式庫。 可重複使用的程式碼的不同片段完全不同的作業系統時，這是很明顯地更困難。

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案示範一種方法。 此解決方案包含七個不同的專案：

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform)，正常的 Xamarin.Forms PCL
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS)，iOS 類別庫
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android)，Android 類別庫
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP)，通用 Windows 類別庫
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT)，通用於所有 Windows 平台的程式碼的共用的專案

所有個別的平台專案 (除了**Xamarin.FormsBook.Platform.WinRT**) 有參考**Xamarin.FormsBook.Platform**。 三個 Windows 專案有參考**Xamarin.FormsBook.Platform.WinRT**。

所有專案都包含靜態`Toolkit.Init`方法，以確保如果 Xamarin.Forms 應用程式方案中的專案未直接參考它，就會載入程式庫。

**Xamarin.FormsBook.Platform**專案中包含的新[ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs)介面。 所有方法現在都已具有名稱`Async`尾碼，並傳回`Task`物件。

**Xamarin.FormsBook.Platform.WinRT**專案中包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)適用於 Windows 執行階段類別。

**Xamarin.FormsBook.Platform.iOS**專案中包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs)適用於 iOS 的類別。 這些方法必須是非同步。 有些方法使用非同步版本中所定義的方法`StreamWriter`並`StreamReader`: [ `WriteAsync` ](xref:System.IO.StreamWriter.WriteAsync(System.String))並[ `ReadToEndAsync` ](xref:System.IO.StreamReader.ReadToEndAsync)。 其他人將轉換結果`Task`物件使用[ `FromResult` ](xref:System.Threading.Tasks.Task.FromResult*)方法。

**Xamarin.FormsBook.Platform.Android**專案包含類似[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs)適用於 Android 的類別。

**Xamarin.FormsBook.Platform**專案也包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs)類別，可以使`DependencyService`物件。

若要使用這些程式庫，應用程式方案必須包括中的所有專案**Xamarin.FormsBook.Platform**方案，而每個應用程式專案必須具有對應的程式庫中的參考**Xamarin.FormsBook.Platform**。

[ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync)解決方案示範如何使用**Xamarin.FormsBook.Platform**程式庫。 每個專案都有呼叫`Toolkit.Init`。 應用程式會使用非同步檔案 I/O 函式。

### <a name="keeping-it-in-the-background"></a>讓它保持在背景中

在 程式庫，讓多個非同步方法呼叫的方法&mdash;這類`WriteFileAsync`和`ReadFileASync`方法，在 Windows 執行階段[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)類別&mdash;可以進行某種程度使用更有效率[ `ConfigureAwait` ](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean))方法來避免切換回使用者介面執行緒。

### <a name="dont-block-the-ui-thread"></a>不會封鎖 UI 執行緒 ！

有時候您會嘗試避免使用`ContinueWith`或`await`利用[ `Result` ](xref:System.Threading.Tasks.Task`1.Result)方法上的屬性。 這應該避免使用它可能會封鎖 UI 執行緒，或甚至是停止回應的應用程式。

## <a name="your-own-awaitable-methods"></a>您自己的 awaitable 方法

您可以將它傳遞至其中一個，以非同步方式執行一些程式碼[ `Task.Run` ](xref:System.Threading.Tasks.Task.Run(System.Action))方法。 您可以呼叫`Task.Run`內處理的一些負荷的非同步方法。

各種`Task.Run`下面會討論的模式。

### <a name="the-basic-mandelbrot-set"></a>基本 Mandelbrot 集合

要繪製設定即時 Mandelbrot [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)文件庫具有[ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs)結構類似於`System.Numerics`命名空間。

[ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet)範例有`CalculateMandeblotAsync`計算基本的黑白 Mandelbrot 集合，並使用其程式碼後置檔案中的方法[ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)若要將它放在一個點陣圖。

### <a name="marking-progress"></a>標示進度

報告進度的非同步方法，您可以具現化[ `Progress<T>` ](xref:System.Progress`1)類別，並定義您的非同步方法具有類型的引數[ `IProgress<T>` ](xref:System.IProgress`1)。 這示範於[ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)範例。

### <a name="cancelling-the-job"></a>正在取消工作

您也可以撰寫可取消的非同步方法。 開始使用名為類別[ `CancellationTokenSource` ](xref:System.Threading.CancellationTokenSource)。 [ `Token` ](xref:System.Threading.CancellationTokenSource.Token)屬性是類型的值[ `CancellationToken` ](xref:System.Threading.CancellationToken)。 這會傳遞至非同步函式。 程式會呼叫[ `Cancel` ](xref:System.Threading.CancellationTokenSource.Cancel)方法`CancellationTokenSource`（通常以回應使用者動作） 來取消非同步函式。

非同步方法可以定期檢查[ `IsCancellationRequested` ](xref:System.Threading.CancellationToken.IsCancellationRequested)屬性`CancellationToken`並結束屬性是否`true`，或只是呼叫[ `ThrowIfCancellationRequested` ](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested)方法，請在這種情況下之方法的結尾[ `OperationCancelledException` ](xref:System.OperationCanceledException)。

[ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation)範例示範如何使用可取消的函式。

### <a name="an-mvvm-mandelbrot"></a>MVVM Mandelbrot

[ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF)範例具有更廣泛的使用者介面，而且它主要根據[ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs)並[ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)類別：

[![Mandelbrot X F 的三個螢幕擷取畫面](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>回到 web

[ `WebRequest` ](xref:System.Net.WebRequest)一些範例中所使用的類別會使用稱為 「 非同步程式設計模型 」 或 「 APM 的舊式非同步通訊協定。 您可以將這類類別轉換成使用其中一種新式的點選通訊協定`FromAsync`中的方法[ `TaskFactory` ](xref:System.Threading.Tasks.TaskFactory`1)類別。 [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap)為範例。



## <a name="related-links"></a>相關連結

- [第 20 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [第 20 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [使用檔案](~/xamarin-forms/data-cloud/data/files.md)

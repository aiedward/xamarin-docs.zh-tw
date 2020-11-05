---
title: 第20章摘要。 非同步與檔案 I/O
description: 建立 Mobile Apps Xamarin.Forms ：第20章的摘要。 非同步與檔案 I/O
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 75c79c7a5300cf5708bb46740bec11f84b59c786
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374013"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>第20章摘要。 非同步與檔案 I/O

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

圖形化使用者介面必須依序回應使用者輸入事件。 這表示使用者輸入事件的所有處理都必須在單一線程中發生，通常稱為 *主執行緒* 或 *UI 執行緒* 。

使用者預期圖形使用者介面的回應速度。 這表示程式必須快速處理使用者輸入事件。 如果無法這麼做，則必須將處理常式降級至執行的次要執行緒。

本書中的數個範例程式都使用了 [`WebRequest`](xref:System.Net.WebRequest) 類別。 在這個類別中， [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) 方法會啟動背景工作執行緒，這會在完成時呼叫回呼函式。 不過，該回呼函式會在背景工作執行緒中執行，因此程式必須呼叫 [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) 方法來存取使用者介面。

> [!NOTE]
> Xamarin.Forms 程式應使用， [`HttpClient`](xref:System.Net.Http.HttpClient) 而不是透過 [`WebRequest`](xref:System.Net.WebRequest) 網際網路存取檔案。 `HttpClient` 支援非同步作業。

.NET 和 c # 中有更新式的非同步處理方法。 這包括 [`Task`](xref:System.Threading.Tasks.Task) 和 [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) 類別以及和命名空間中的其他類型 [`System.Threading`](xref:System.Threading) ，以及 [`System.Threading.Tasks`](xref:System.Threading.Tasks) c # 5.0 `async` 和 `await` 關鍵字。 這就是本章的重點所在。

## <a name="from-callbacks-to-await"></a>從回呼到 await

`Page`類別本身包含三個可顯示警示方塊的非同步方法：

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 傳回 `Task` 物件
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) 傳回 `Task<bool>` 物件
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) 傳回 `Task<string>` 物件

`Task`這些物件表示這些方法會執行以工作為基礎的非同步模式，稱為點一下。 這些 `Task` 物件會從方法快速傳回。 傳回 `Task<T>` 值會構成「承諾」， `TResult` 當工作完成時，就可以使用類型的值。 `Task`傳回值表示將完成但未傳回任何值的非同步動作。

在這些情況下， `Task` 當使用者關閉警示方塊時，就會完成。  

### <a name="an-alert-with-callbacks"></a>具有回呼的警示

[**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks)範例會示範如何 `Task<bool>` 使用回呼方法來處理傳回物件和 `Device.BeginInvokeOnMainThread` 呼叫。

### <a name="an-alert-with-lambdas"></a>Lambda 的警示

[**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas)範例示範如何使用匿名 lambda 函數進行處理 `Task` 和 `Device.BeginInvokeOnMainThread` 呼叫。  

### <a name="an-alert-with-await"></a>使用 await 的警示

更簡單的方法牽涉到 `async` `await` c # 5 中引進的和關鍵字。 [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait)範例會示範其用途。

### <a name="an-alert-with-nothing"></a>不含任何內容的警示

如果非同步方法傳回 `Task` 而非 `Task<TResult>` ，則程式不需要在非同步工作完成時使用任何這些技術。 [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert)範例會示範這一點。

### <a name="saving-program-settings-asynchronously"></a>以非同步方式儲存程式設定

[**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings)範例 [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) 會示範如何使用方法 `Application` 來儲存程式設定，而不需要覆寫 `OnSleep` 方法。

### <a name="a-platform-independent-timer"></a>與平臺無關的計時器

您可以使用 [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) 來建立與平臺無關的計時器。 [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock)範例會示範這一點。

## <a name="file-inputoutput"></a>檔案輸入/輸出

傳統上，.NET [`System.IO`](xref:System.IO) 命名空間是檔案 i/o 支援的來源。 雖然此命名空間中的某些方法支援非同步作業，但大部分都沒有。 命名空間也支援數個簡單的方法呼叫，以執行複雜的檔案 i/o 函數。

### <a name="good-news-and-bad-news"></a>好消息和壞消息

支援的所有平臺 Xamarin.Forms 都支援應用程式專用的應用程式本機儲存體 &mdash; 儲存體。

Xamarin 和 Xamarin. Android 程式庫包含 Xamarin 已針對這兩個平臺明確量身打造的 .NET 版本。 這些類別包括 `System.IO` 您可以用來在這兩個平臺的應用程式本機儲存體執行檔案 i/o 的類別。

但是，如果您 `System.IO` 在 PCL 中搜尋這些類別 Xamarin.Forms ，就不會找到它們。 問題在於 Microsoft 完全改頭換面 Windows 執行階段 API 的檔案 i/o。 以 Windows 8.1、Windows Phone 8.1 和通用 Windows 平臺為目標的程式不會用於檔案 `System.IO` i/o。

這表示您必須使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) (第9章所述的 [**。**](chapter09.md) 用來執行檔案 i/o 的平臺特定 API 呼叫。

> [!NOTE]
> 便攜類別 Libaries 已取代為 .NET Standard 2.0 程式庫，.NET Standard 2.0 支援 [`System.IO`](xref:System.IO) 所有平臺的類型 Xamarin.Forms 。 您不再需要 `DependencyService` 針對大部分的檔案 i/o 工作使用。 如需更新式的檔案 i/o 方法，請參閱[中 Xamarin.Forms ](~/xamarin-forms/data-cloud/data/files.md)的檔案處理。

### <a name="a-first-shot-at-cross-platform-file-io"></a>跨平臺檔案 i/o 的第一次快照

[**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout)範例會針對檔案 [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) i/o 定義介面，並在所有平臺上定義此介面的介面。 不過，Windows 執行階段的實作為無法使用此介面中的方法，因為 Windows 執行階段檔案 i/o 方法是非同步。

### <a name="accommodating-windows-runtime-file-io"></a>Windows 執行階段檔案 i/o 的容納

在 Windows 執行階段下執行的程式會使用 [`Windows.Storage`](/uwp/api/Windows.Storage) 和 [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) 命名空間中的類別來儲存 i/o，包括應用程式本機儲存體。 因為 Microsoft 判斷需要超過50毫秒的任何作業都應該是非同步，以避免封鎖 UI 執行緒，所以這些檔案 i/o 方法大多是非同步。

示範這個新方法的程式碼將會在程式庫中，讓其他應用程式可以使用它。

## <a name="platform-specific-libraries"></a>平台特定程式庫

在程式庫中儲存可重複使用的程式碼是很有利的。 當可重複使用的程式碼的不同部分適用于完全不同的作業系統時，這顯然更為困難。

「 [**Xamarin.Forms 平臺**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)」解決方案示範一種方法。 此解決方案包含七個不同的專案：

- 《 Platform，a normal [**Xamarin.Forms 》**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform) Xamarin.FormsPcl
- [**Xamarin.Forms Book**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS)（ios 類別庫）
- [**Xamarin.Forms Book**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android)、android 類別庫
- [**Xamarin.Forms Book**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP)（通用 Windows 類別庫）
- [**Xamarin.Forms Book**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT)，適用于所有 Windows 平臺通用程式碼的共用專案

所有個別的平臺專案都 (，但 **Xamarin.Forms 本書** 的) 參考的是 **Xamarin.Forms book. platform** 。 這三個 Windows 專案都參考了 **Xamarin.Forms Book. WinRT** 。

所有專案都包含一個靜態 `Toolkit.Init` 方法，以確保當應用程式方案中的專案未直接參考該程式庫時，會載入該程式庫 Xamarin.Forms 。

**Xamarin.Forms Book** 專案包含新的 [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) 介面。 所有方法現在都具有 `Async` 尾碼和傳回物件的名稱 `Task` 。

**Xamarin.Forms Book. WinRT** 專案包含 [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) Windows 執行階段的類別。

**Xamarin.Forms Book. ios** 專案包含 [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) 適用于 ios 的類別。 這些方法現在必須是非同步。 某些方法會使用在和中定義之方法的非同步 `StreamWriter` 版本 `StreamReader` ： [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) 和 [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync) 。 其他人會使用方法將結果轉換成 `Task` 物件 [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) 。

此 **Xamarin.Forms . android** 專案包含適用于 android 的類似 [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) 類別。

**Xamarin.Forms Book** 專案也包含 [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) 可簡化物件使用的類別。 `DependencyService`

若要使用這些程式庫，應用程式方案必須包含該 **Xamarin.Forms 書籍** 中的所有專案，而且每個應用程式專案都必須參考 **Xamarin.Forms book** 中對應的程式庫。

[**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync)解決方案示範如何使用「 **Xamarin.Forms 平臺** 程式庫」。 每個專案都有的呼叫 `Toolkit.Init` 。 應用程式會使用非同步檔案 i/o 函數。

### <a name="keeping-it-in-the-background"></a>將它保持在背景

程式庫中的方法會呼叫多個非同步方法， &mdash; 例如 `WriteFileAsync` `ReadFileASync` Windows 執行階段類別中的和方法， [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) &mdash; 可以使用 [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) 方法避免切換回使用者介面執行緒，以更有效率的方式進行。

### <a name="dont-block-the-ui-thread"></a>不要封鎖 UI 執行緒！

有時候，避免使用 `ContinueWith` 或 `await` 使用方法上的屬性，會很吸引人 [`Result`](xref:System.Threading.Tasks.Task`1.Result) 。 您應該避免這種情況，因為它可以封鎖 UI 執行緒，甚至讓應用程式停止回應。

## <a name="your-own-awaitable-methods"></a>您自己的可等候方法

您可以藉由將程式碼傳遞至其中一個方法，以非同步方式執行程式碼 [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) 。 您可以 `Task.Run` 在處理一些額外負荷的非同步方法中呼叫。

以下將討論各種不同的 `Task.Run` 模式。

### <a name="the-basic-mandelbrot-set"></a>基本 Mandelbrot 集

若要即時繪製 Mandelbrot 集，則為 [**Xamarin.Forms 。工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)組程式庫具有 [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) 類似命名空間中的結構 `System.Numerics` 。

[**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet)範例的 `CalculateMandeblotAsync` 程式碼後置檔案中有一個方法，它會計算基本的黑色和白色 Mandelbrot 集，並使用將 [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) 其置於點陣圖上。

### <a name="marking-progress"></a>標記進度

若要從非同步方法報告進度，您可以具現化 [`Progress<T>`](xref:System.Progress`1) 類別，並將您的非同步方法定義為具有類型的引數 [`IProgress<T>`](xref:System.IProgress`1) 。 [**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)範例會示範這一點。

### <a name="cancelling-the-job"></a>正在取消作業

您也可以撰寫要可取消的非同步方法。 您要從名為的類別開始 [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource) 。 [`Token`](xref:System.Threading.CancellationTokenSource.Token)屬性是類型的值 [`CancellationToken`](xref:System.Threading.CancellationToken) 。 這會傳遞至非同步函式。 程式通常會呼叫 [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) (的方法， `CancellationTokenSource` 以回應使用者) 取消非同步函式的動作。

非同步方法可以定期檢查的 [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) 屬性 `CancellationToken` ，如果屬性為，則會 `true` 結束，或直接呼叫 [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) 方法，在此情況下，方法會以進行結尾 [`OperationCancelledException`](xref:System.OperationCanceledException) 。

[**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation)範例會示範如何使用可取消函數。

### <a name="an-mvvm-mandelbrot"></a>MVVM Mandelbrot

[**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF)範例有更廣泛的使用者介面，而且大部分都是根據 [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) 和 [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) 類別：

[![Mandelbrot X F 的三重螢幕擷取畫面](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>返回 web

[`WebRequest`](xref:System.Net.WebRequest)某些範例中使用的類別會使用稱為非同步程式設計模型或 APM 的舊的非同步通訊協定。 您可以使用類別中的其中一個方法，將這類類別轉換成新式的分路通訊協定 `FromAsync` [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) 。 [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap)範例會示範這一點。

## <a name="related-links"></a>相關連結

- [第20章全文 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [第20章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [處理檔案](~/xamarin-forms/data-cloud/data/files.md)

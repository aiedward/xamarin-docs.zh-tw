---
title: "第 20 的摘要。 非同步和檔案 I/O"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: f7c81cfb77772af219fe28f081e7f8636e118fb1
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>第 20 的摘要。 非同步和檔案 I/O

 圖形化使用者介面必須循序回應使用者輸入事件。 這表示所有使用者輸入事件的處理必須都發生在單一執行緒，通常稱為*主執行緒*或*UI 執行緒*。

使用者會預期有回應的圖形化使用者介面。 這表示程式必須快速處理使用者輸入事件。 如果不可行，然後處理必須被貶為執行的次要執行緒。

這個活頁簿中的數個範例程式已經使用[ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/)類別。 此類別中[ `BeginGetReponse` ](https://developer.xamarin.com/api/member/System.Net.WebRequest.BeginGetResponse/p/System.AsyncCallback/System.Object/)方法會啟動背景工作執行緒，完成時呼叫的回呼函式。 不過，該回呼函式會執行中的背景工作執行緒，因此程式必須呼叫[ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/)方法來存取使用者介面。

.NET 和 C# 中使用非同步處理的更現代方法。 這項作業包括[ `Task` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task/)和[ `Task<TResult>` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task%3CTResult%3E/)類別，以及在其他類型[ `System.Threading` ](https://developer.xamarin.com/api/namespace/System.Threading/)和[ `System.Threading.Tasks` ](https://developer.xamarin.com/api/namespace/System.Threading.Tasks/)命名空間，以及 C# 5.0`async`和`await`關鍵字。 這是這一章著重於。

## <a name="from-callbacks-to-await"></a>從等候回呼

`Page`本身的類別包含三個非同步方法，以顯示警示方塊：

- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) 傳回`Task`物件
- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/System.String/) 傳回`Task<bool>`物件
- [`DisplayActionSheet`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet/p/System.String/System.String/System.String/System.String[]/) 傳回`Task<string>`物件

`Task`物件表示，這些方法會實作工作式非同步模式，稱為點選。 這些`Task`快速從方法傳回物件。 `Task<T>`傳回值構成 「 承諾 」 之型別的值`TResult`將可在工作完成時使用。 `Task`傳回值表示非同步動作的就是完成，但不是含值傳回。

在這些情況下，`Task`已完成，當使用者關閉警示的方塊。  

### <a name="an-alert-with-callbacks"></a>具有回呼的警示

[ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks)範例示範如何處理`Task<bool>`傳回物件和`Device.BeginInvokeOnMainThread`使用回呼方法的呼叫。

### <a name="an-alert-with-lambdas"></a>Lambda 與警示

[ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas)範例示範如何使用匿名 lambda 函式以處理`Task`和`Device.BeginInvokeOnMainThread`呼叫。  

### <a name="an-alert-with-await"></a>與 await 警示

更直接的方法涉及`async`和`await`C# 5 中的關鍵字。 [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait)範例將示範如何使用。

### <a name="an-alert-with-nothing"></a>沒有警示

如果非同步方法傳回`Task`而不是`Task<TResult>`，則程式不需要使用任何這些技術不需要知道當非同步工作完成。 [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert)範例為其示範。

### <a name="saving-program-settings-asynchronously"></a>儲存程式設定以非同步的方式

[ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings)範例將示範如何使用[ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/)方法`Application`儲存程式設定，因為它們不會變更覆寫`OnSleep`方法。

### <a name="a-platform-independent-timer"></a>平台無關計時器

您可使用[ `Task.Delay` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Delay/p/System.Int32/)建立獨立於平台的計時器。 [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock)範例為其示範。

## <a name="file-inputoutput"></a>檔案輸入/輸出

傳統上，.NET [ `System.IO` ](https://developer.xamarin.com/api/namespace/System.IO/)命名空間已經過的檔案 I/O 支援的來源。 雖然此命名空間中的某些方法支援非同步作業，大部分則否。 命名空間也支援執行複雜的檔案 I/O 功能的幾個簡單的方法呼叫。

### <a name="good-news-and-bad-news"></a>好消息和壞處

Xamarin.Forms 支援應用程式本機儲存體 & #x 2014; 支援的平台應用程式的私人儲存體。

Xamarin.iOS 和 Xamarin.Android 程式庫包含 Xamarin 已明確量身訂做的這些兩個平台的.NET 的版本。 其中包括從類別`System.IO`可用來執行與應用程式的本機儲存體的檔案 I/O 的這兩個平台。

不過，如果您搜尋這些`System.IO`Xamarin.Forms PCL 類別時，您將找不到它們。 問題是適用於 Windows 執行階段 API，完全改寫的 Microsoft 檔案 I/O。 以 Windows 8.1、 Windows Phone 8.1 和通用 Windows 平台為目標的程式並不使用`System.IO`用於檔案 I/O。

這表示，您必須使用[ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) (先討論[**第 9 章。平台專屬的 API 呼叫**](chapter09.md)實作檔案 I/O。

### <a name="a-first-shot-at-cross-platform-file-io"></a>在跨平台檔案 I/O 的第一個擷取畫面

[ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout)範例會定義[ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs)檔案 I/O 和實作這個介面，在所有平台的介面。 不過，Windows 執行階段實作不適用於這個介面中方法的因為 Windows 執行階段檔案 I/O 方法是非同步。

### <a name="accommodating-windows-runtime-file-io"></a>包容的 Windows 執行階段檔案 I/O

Windows 執行階段執行的程式使用中的類別[ `Windows.Storage` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.aspx)和[ `Windows.Storage.Streams` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.streams.aspx)適用於檔案 I/O，包括應用程式的本機儲存體命名空間。 Microsoft 認為需要超過 50 毫秒應該以非同步方式來避免封鎖 UI 執行緒的任何作業，因為這些檔案 I/O 方法大多是非同步的。

示範這種新方法的程式碼會在文件庫中，以便供其他應用程式。

## <a name="platform-specific-libraries"></a>平台特定程式庫

它的好處是，文件庫中儲存可重複使用的程式碼。 完全不同的作業系統不同可重複使用程式碼片段時，這會很明顯地更困難。

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案示範一種方法。 此方案包含七個不同的專案：

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform)，一般的 Xamarin.Forms PCL
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS)，iOS 類別程式庫
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android)，Android 類別程式庫
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP)，通用 Windows 類別程式庫
- [**Xamarin.FormsBook.Platform.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Windows)，Windows 8.1 的 PCL。
- [**Xamarin.FormsBook.Platform.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinPhone), a PCL for Windows Phone 8.1
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT)，共通用於所有 Windows 平台的程式碼專案

所有個別的平台專案 (除了**Xamarin.FormsBook.Platform.WinRT**) 具有其參照**Xamarin.FormsBook.Platform**。 三個 Windows 專案有參考**Xamarin.FormsBook.Platform.WinRT**。

所有專案都包含靜態`Toolkit.Init`方法，以確保如果 Xamarin.Forms 應用程式方案中的專案未直接參考，確定已載入文件庫。

**Xamarin.FormsBook.Platform**專案已包含新[ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs)介面。 所有方法現在都具有名稱`Async`後置字元和傳回`Task`物件。

**Xamarin.FormsBook.Platform.WinRT**專案包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)為 Windows 執行階段類別。

**Xamarin.FormsBook.Platform.iOS**專案包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs)適用於 iOS 的類別。 這些方法必須是非同步。 某些方法的使用中定義方法的非同步版本`StreamWriter`和`StreamReader`: [ `WriteAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamWriter.WriteAsync/p/System.String/)和[ `ReadToEndAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamReader.ReadToEndAsync()/)。 其他人將轉換結果以`Task`物件使用[ `FromResult` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.FromResult%7BTResult%7D/p/TResult/)方法。

**Xamarin.FormsBook.Platform.Android**專案包含類似[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs)適用於 Android 的類別。

**Xamarin.FormsBook.Platform**專案也包含[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs)易於使用的類別`DependencyService`物件。

若要使用這些程式庫，應用程式方案必須包含中的所有專案**Xamarin.FormsBook.Platform**解決方案，以及每個應用程式的專案必須有對應的程式庫中的參考**Xamarin.FormsBook.Platform**。

[ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync)方案會示範如何使用**Xamarin.FormsBook.Platform**文件庫。 每個專案有呼叫`Toolkit.Init`。 應用程式會使用非同步檔案 I/O 函式。

### <a name="keeping-it-in-the-background"></a>保留在背景中

對多個非同步方法 & #x 2014; 發出呼叫的程式庫中的方法例如`WriteFileAsync`和`ReadFileASync`Windows 執行階段中的方法[ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)類別 & #x 2014; 可以進行稍微更有效率地使用[ `ConfigureAwait` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task%3CTResult%3E.ConfigureAwait/p/System.Boolean/)方法避免在使用者介面執行緒中切換回。

### <a name="dont-block-the-ui-thread"></a>不會阻擋 UI 執行緒 ！

有時候很容易避免使用`ContinueWith`或`await`使用[ `Result` ](https://developer.xamarin.com/api/property/System.Threading.Tasks.Task%3CTResult%3E.Result/)方法上的屬性。 這應該避免可以封鎖 UI 執行緒，或甚至停止回應應用程式。

## <a name="your-own-awaitable-methods"></a>您自己的 awaitable 方法

您可以執行一些程式碼以非同步方式將它傳遞給其中一個[ `Task.Run` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Run/p/System.Action/)方法。 您可以呼叫`Task.Run`處理部分的額外負荷的非同步方法內。

各種`Task.Run`模式於下方討論。

### <a name="the-basic-mandelbrot-set"></a>Mandelbrot 組基本

要繪製設定即時 Mandelbrot [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)媒體櫃具有[ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs)結構類似於`System.Numerics`命名空間。

[ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet)範例具有`CalculateMandeblotAsync`計算基本黑白 Mandelbrot 集合，並使用其程式碼後置檔案中的方法[ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)放點陣圖。

### <a name="marking-progress"></a>標示進度

報告進度的非同步方法，您可以具現化[ `Progress<T>` ](https://developer.xamarin.com/api/type/System.Progress%3CT%3E/)類別，並定義您的非同步方法的型別引數[ `IProgress<T>` ](https://developer.xamarin.com/api/type/System.IProgress%3CT%3E/)。 這示範於[ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)範例。

### <a name="cancelling-the-job"></a>取消的工作

您也可以撰寫可取消非同步方法。 您開始使用類別，名為[ `CancellationTokenSource` ](https://developer.xamarin.com/api/type/System.Threading.CancellationTokenSource/)。 [ `Token` ](https://developer.xamarin.com/api/property/System.Threading.CancellationTokenSource.Token/)屬性為型別的值[ `CancellationToken` ](https://developer.xamarin.com/api/type/System.Threading.CancellationToken/)。 這會傳遞至非同步函式。 程式會呼叫[ `Cancel` ](https://developer.xamarin.com/api/member/System.Threading.CancellationTokenSource.Cancel()/)方法`CancellationTokenSource`（通常以回應使用者動作） 取消非同步函式。

非同步方法可以定期檢查[ `IsCancellationRequested` ](https://developer.xamarin.com/api/property/System.Threading.CancellationToken.IsCancellationRequested/)屬性`CancellationToken`並結束如果屬性是`true`，或只是呼叫[ `ThrowIfCancellationRequested` ](https://developer.xamarin.com/api/member/System.Threading.CancellationToken.ThrowIfCancellationRequested()/)方法，請在以這種情況下方法結束[ `OperationCancelledException` ](https://developer.xamarin.com/api/type/System.OperationCanceledException/)。

[ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation)範例將示範如何使用取消的函式。

### <a name="an-mvvm-mandelbrot"></a>MVVM Mandelbrot

[ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF)範例具有更廣泛的使用者介面，而且它主要基礎[ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs)和[ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)類別：

[![Mandelbrot X F 的三個螢幕擷取畫面](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>回到網站

[ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/)某些範例中所使用的類別會使用稱為 「 非同步程式設計模型 」 或 「 APM 舊式非同步通訊協定。 您可以將這種類別轉換為現代點選通訊協定使用其中一種`FromAsync`方法[ `TaskFactory` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskFactory%3CTResult%3E/)類別。 [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap)範例為其示範。



## <a name="related-links"></a>相關連結

- [第 20 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [第 20 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [使用檔案](~/xamarin-forms/app-fundamentals/files.md)

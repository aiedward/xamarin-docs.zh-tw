---
title: 第20章摘要。 非同步與檔案 I/O
description: 使用 Xamarin.表單創建行動應用程式:第 20 章摘要。 非同步與檔案 I/O
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771044"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>第20章摘要。 非同步與檔案 I/O

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

 圖形使用者介面必須按順序回應使用者輸入事件。 這意味著使用者輸入事件的所有處理都必須發生在單個線程中,通常稱為*主線程*或*UI 執行緒*。

使用者希望圖形使用者介面能夠回應。 這意味著程式必須快速處理使用者輸入事件。 如果這是不可能的,則必須將處理降級為執行的輔助線程。

本書中的幾個示例程式使用了該[`WebRequest`](xref:System.Net.WebRequest)類。 在此類中,[`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object))該方法啟動輔助線程,該線程在完成時調用回調函數。 但是,該回調函數在輔助線程中運行,因此程序必須調用[`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action))方法才能訪問使用者介面。

> [!NOTE]
> Xamarin.Forms 程式[`HttpClient`](xref:System.Net.Http.HttpClient)應該[`WebRequest`](xref:System.Net.WebRequest)使用, 而不是在網路上存取檔。 `HttpClient`支援非同步操作。

在 .NET 和 C# 中提供了更現代的非同步處理方法。 這涉及[`Task`](xref:System.Threading.Tasks.Task)[`Task<TResult>`](xref:System.Threading.Tasks.Task`1)和[`System.Threading`](xref:System.Threading)類[`System.Threading.Tasks`](xref:System.Threading.Tasks)以及和命名空間中的其他類型的,以及 C#`async``await`5.0 和關鍵字。 這就是本章的重點。

## <a name="from-callbacks-to-await"></a>從回檔到等待

類別`Page`包含三種非同步方法來顯示警報框:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))傳回`Task`物件
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String))傳回`Task<bool>`物件
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))傳回`Task<string>`物件

這些`Task`物件指示這些方法實現基於任務的非同步模式,稱為 TAP。 這些`Task`物件將從方法快速返回。 `Task<T>`返回值構成"承諾",當任務完成時,類型`TResult`值將可用。 返回`Task`值指示將完成但沒有返回值的非同步操作。

在所有這些情況下,當使用者關閉`Task`警報框時,即已完成。  

### <a name="an-alert-with-callbacks"></a>具有回檔的警報

[**警報回調**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks)示例演示如何使用回調方法`Task<bool>`處理 返回`Device.BeginInvokeOnMainThread`物件和調用。

### <a name="an-alert-with-lambdas"></a>帶有羊羔的警報

[**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas)範例展示如何使用匿名 lambda 函`Task`數`Device.BeginInvokeOnMainThread`來處理和呼叫。  

### <a name="an-alert-with-await"></a>等待的警示

更直接的方法涉及 C# 5`async`中`await`介紹的 和 關鍵字。 [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait)示例演示了它們的使用。

### <a name="an-alert-with-nothing"></a>一條沒有內容的警報

如果非同步方法返回`Task``Task<TResult>`而不是返回,則如果程式不需要使用任何這些技術(如果不需要知道非同步任務何時完成)。 [**"無警報"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert)範例演示了這一點。

### <a name="saving-program-settings-asynchronously"></a>非同步儲存程式設定

[**SaveProgramChange**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings)範例展示在程式設定[`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync)更改`Application`時 保存程式設定而不`OnSleep`重寫 該方法的方法。

### <a name="a-platform-independent-timer"></a>獨立於平台的計時器

可以使用[`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32))來創建獨立於平臺的計時器。 [**任務延遲時鐘**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock)示例演示了這一點。

## <a name="file-inputoutput"></a>檔案輸入/輸出

傳統上,.NET[`System.IO`](xref:System.IO)命名空間一直是檔 I/O 支援的來源。 儘管此命名空間中的某些方法支援非同步操作,但大多數不支援。 命名空間還支援幾個執行複雜檔 I/O 函數的簡單方法調用。

### <a name="good-news-and-bad-news"></a>好消息和壞訊息

Xamarin.Forms 支援的所有平台都支援應用程式專用的應用程式本地&mdash;儲存 。

Xamarin.iOS 和 Xamarin.Android 庫包含 Xamarin 已明確為這兩個平臺定製的 .NET 版本。 這些類包括可用於`System.IO`在這兩個平臺中使用應用程式本地儲存執行檔 I/O 的類。

但是,如果在 Xamarin.Forms PCL`System.IO`中搜索這些 類,則找不到它們。 問題是微軟完全修改了 Windows 運行時 API 的檔 I/O。 面向 Windows 8.1、Windows Phone 8.1`System.IO`和通用 Windows 平臺的程式不用於檔 I/O。

這意味著您需要使用[`DependencyService`](xref:Xamarin.Forms.DependencyService)(第 9 章中首先討論[**)。特定於平臺的 API 呼叫**](chapter09.md)以實現檔 I/O。

> [!NOTE]
> 可攜式類 Libs 已取代為 .NET 標準 2.0[`System.IO`](xref:System.IO)庫,.NET 標準 2.0 支援所有 Xamarin.Forms 平台的類型。 對大多數檔案 I/O`DependencyService`工作 ,不再需要使用 。 有關檔案 I/O 的更現代方法,請參閱[Xamarin.Forms 中的檔案處理](~/xamarin-forms/data-cloud/data/files.md)。

### <a name="a-first-shot-at-cross-platform-file-io"></a>跨平台檔 I/O 的第一張照片

[**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout)範例為[`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs)所有平臺中的檔案 I/O 以及此介面的實現定義了介面。 但是,Windows 運行時實現不在此介面中的方法使用,因為 Windows 運行時檔 I/O 方法是非同步的。

### <a name="accommodating-windows-runtime-file-io"></a>調整 Windows 執行時檔案 I/O

在 Windows 執行時下執行的程式[`Windows.Storage`](/uwp/api/Windows.Storage)[`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams)使用 檔案 I/O 和命名空間中的類,包括應用程式本地儲存。 由於 Microsoft 確定任何需要 50 毫秒以上的操作都應該是異步的,以避免阻塞 UI 線程,因此這些檔 I/O 方法大多是異步的。

演示此新方法的代碼將位於庫中,以便其他應用程式可以使用它。

## <a name="platform-specific-libraries"></a>平台特定程式庫

將可重用的代碼存儲在庫中是有利的。 當可重用代碼的不同部分適用於完全不同的操作系統時,這顯然更加困難。

[**Xamarin.FormsBook.平台**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解決方案演示了一種方法。 此解決方案包含七個不同的專案:

- [**Xamarin.FormsBook.Platform,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform)一個正常的Xamarin.FormsPCL
- [**Xamarin.FormsBook.Platform.iOS,iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS)類庫
- [**Xamarin.FormsBook.Platform.安卓**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android),安卓類圖書館
- [**Xamarin.FormsBook.Platform.UWP,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP)通用Windows類庫
- [**Xamarin.FormsBook.Platform.WinRT,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT)一個共用的代碼專案,是所有 Windows 平臺共有的代碼

所有單獨的平台專案 **(Xamarin.FormsBook.Platform.WinRT**除外)都引用**Xamarin.FormsBook.Platform**. 這三個 Windows 專案都引用了**Xamarin.FormsBook.Platform.WinRT**。

所有專案都包含一個`Toolkit.Init`靜態方法,以確保在 Xamarin.Forms 應用程式解決方案中的專案未直接引用庫時載入庫。

**Xamarin.FormsBook.Platform**專案包含[`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs)新的介面。 所有方法現在都有帶`Async`後綴和`Task`返回 物件的名稱。

**Xamarin.FormsBook.Platform.WinRT**專案包含[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)Windows 運行時的類。

**Xamarin.FormsBook.Platform.iOS**專案包含[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs)iOS 類。 這些方法現在必須是異步的。 某些方法使用`StreamWriter`在`StreamReader`[`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String))和 中定義的方法的非[`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync)同步版本。 其他則使用方法`Task`[`FromResult`](xref:System.Threading.Tasks.Task.FromResult*)將結果轉換為物件。

**Xamarin.FormsBook.Platform.Android**專案包含[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs)Android 的類似類。

**Xamarin.FormsBook.Platform**專案還包含一[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs)個 類,該類可`DependencyService`簡化 物件的使用。

要使用這些庫,應用程式解決方案必須包括**Xamarin.FormsBook.Platform**解決方案中的所有專案,並且每個應用程式專案都必須對**Xamarin.FormsBook.Platform**中的相應庫具有引用。

[**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync)解決方案展示如何使用**Xamarin.FormsBook.Platform**庫。 每個項目都有一個呼叫`Toolkit.Init`。 應用程式使用非同步檔 I/O 函數。

### <a name="keeping-it-in-the-background"></a>將保留在後臺

&mdash;庫中對多個非同步方法(如 Windows`WriteFileAsync``ReadFileASync`[`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs)執行&mdash;時類中的和方法)進行調用的方法,[`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean))透過使用方法避免切換回使用者介面線程,可以提高效率。

### <a name="dont-block-the-ui-thread"></a>不要阻止 UI 線程!

有時,避免使用`ContinueWith``await`或使用方法的[`Result`](xref:System.Threading.Tasks.Task`1.Result)屬性 是誘人的。 應避免這樣做,因為它可以阻止UI線程,甚至掛起應用程式。

## <a name="your-own-awaitable-methods"></a>您自己的可等待方法

通過將某些代碼傳遞給其中一個方法,[`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action))可以非同步運行某些程式碼。 可以在處理某些`Task.Run`開銷的異步方法內調用。

下面將討論`Task.Run`各種模式。

### <a name="the-basic-mandelbrot-set"></a>基本曼德布羅特集

要即時繪製曼德爾布羅特設置[**,Xamarin.Forms.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫的結構與命名[`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs)`System.Numerics`空間中的 結構類似。

[**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet)示例在其`CalculateMandeblotAsync`代碼 背後的檔中有一個方法,用於計算基本的黑白曼德爾布羅特集,並用[`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)它來將其放在位圖上。

### <a name="marking-progress"></a>標記進度

要報告非同步方法的進度,可以實例化[`Progress<T>`](xref:System.Progress`1)類別並將非同步方法定義為具有類型的[`IProgress<T>`](xref:System.IProgress`1)參數。 這一點在[**曼德布羅特進步示例中**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)進行了演示。

### <a name="cancelling-the-job"></a>取消工作

您還可以編寫非同步方法以進行可存取。 從名為[`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource)的類開始。 屬性[`Token`](xref:System.Threading.CancellationTokenSource.Token)是類型[`CancellationToken`](xref:System.Threading.CancellationToken)的值。 這將傳遞給異步函數。 程式呼叫[`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel)方法`CancellationTokenSource`(通常回應使用者的操作)以取消非同步函數。

非[`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested)同步方法可以定期檢查`CancellationToken`和退出的屬性(如果屬性`true`為 ,或者只是[`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested)調用方法,在這種情況下,該方法以結[`OperationCancelledException`](xref:System.OperationCanceledException)尾)

[**曼德布羅特取消**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation)示例演示了可撤銷函數的使用。

### <a name="an-mvvm-mandelbrot"></a>MVVM 曼德爾布羅特

[**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF)範例具有更廣泛的使用者介面,它主要基於[`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs)[`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)和類別:

[![曼德布羅特 X F 的三重螢幕截圖](images/ch20fg13-small.png "MVVM 曼德爾布羅特")](images/ch20fg13-large.png#lightbox "MVVM 曼德爾布羅特")

## <a name="back-to-the-web"></a>回傳網路

某些[`WebRequest`](xref:System.Net.WebRequest)範例中使用的類使用稱為非同步程式設計模型或 APM 的老式非同步協定。 您可以使用`FromAsync`[`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1)類中的一種方法將此類轉換為現代 TAP 協定。 [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap)示例演示了這一點。

## <a name="related-links"></a>相關連結

- [第20章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [第20章樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [處理檔案](~/xamarin-forms/data-cloud/data/files.md)

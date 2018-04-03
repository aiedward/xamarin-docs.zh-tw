---
title: 跨平台效能
description: 有許多技巧可增加利用 Xamarin 平台建置之應用程式的效能。 這些技巧可共同大幅減少 CPU 所執行的工作量和應用程式所耗用的記憶體數量。 本文將描述並討論這些技巧。
ms.topic: article
ms.prod: xamarin
ms.assetid: 9ce61f18-22ac-4b93-91be-5b499677d661
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: e8b597221e806c2338d6f1965d3d151f998a3011
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="cross-platform-performance"></a>跨平台效能

_有許多技巧可增加利用 Xamarin 平台建置之應用程式的效能。這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。本文將描述並討論這些技巧。_

不佳的應用程式效能會以許多方式表現。 它可能會讓應用程式看起來沒有回應、造成捲動緩慢，以及減少電池壽命。 不過，最佳化效能不僅僅只牽涉到實作有效率的程式碼而已。 同時也必須考量使用者對於應用程式效能的體驗。 例如，確保作業能在不封鎖使用者執行其他活動的情況下執行，將可以協助改善使用者體驗。


<a name="profiler" />

## <a name="use-the-profiler"></a>使用分析工具

在開發應用程式時，在程式碼經過分析之後才進行最佳化是非常重要的。 分析是一個決定程式碼在何處進行最佳化會帶來最大效果，進而減少效能問題的技術。 分析工具會追蹤應用程式的記憶體使用量，並記錄應用程式中方法的執行時間。 這項資料可協助巡覽應用程式的執行路徑，以及程式碼的執行成本，以讓您發現最佳化的最佳機會。

Xamarin Profiler 會測量、評估及協助尋找應用程式中與效能相關的問題。 它可用來在 Visual Studio for Mac 或 Visual Studio 中分析 Xamarin.iOS 和 Xamarin.Android 應用程式。 如需 Xamarin Profiler 的詳細資訊，請參閱 [Xamarin Profiler 簡介](~/tools/profiler/index.md)。

當在分析應用程式時，建議採取下列最佳做法：

- 避免在模擬器中分析應用程式，因為模擬器可能會扭曲應用程式效能。
- 在理想情況下，分析應在不同的裝置上分別執行，因為在一種裝置上測量效能不會總是顯示其他裝置的效能特性。 然而，以最低需求來說，分析應在擁有最低預期規格的裝置上執行。
- 關閉所有其他的應用程式，以確保欲分析之應用程式的完整影響都會獲得測量，而非其他應用程式。

<a name="idisposable" />

## <a name="release-idisposable-resources"></a>釋放 IDisposable 資源

`IDisposable` 介面提供了一個釋放資源的機制。 它提供了 `Dispose` 方法，應實作該方法以明確釋放資源。 `IDisposable` 並非一個解構函式，因此應僅在下列情況下實作：

- 當類別擁有未受控 (Unmanaged) 資源時。 通常需要釋放的未受控資源包含檔案、串流和網路連線。
- 當類別具有受控 `IDisposable` 資源時。

類型消費者接著便可以呼叫 `IDisposable.Dispose` 實作來在不再需要執行個體時釋放資源。 有兩種方法可以達到這個目的：

- 藉由在 `IDisposable` 陳述式中包裝 `using` 物件。
- 藉由將呼叫 `IDisposable.Dispose` 的段落包裝進 `try`/`finally` 區塊中。

### <a name="wrapping-the-idisposable-object-in-a-using-statement"></a>在 using 陳述式中包裝 IDisposable 物件

下列程式碼範例顯示了如何將 `IDisposable` 物件包裝在 `using` 陳述式中：

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  using (StreamReader reader = new StreamReader (filename)) {
    text = reader.ReadToEnd ();
  }
  ...
}
```

`StreamReader` 類別實作了 `IDisposable`，而 `using` 陳述式提供了一個在 `StreamReader` 物件離開範圍前呼叫物件上 `StreamReader.Dispose` 方法的便利語法。 在 `using` 區塊內，`StreamReader` 物件是唯讀的，無法重新指派。 `using` 陳述式同時也會確保即使發生例外狀況時，`Dispose` 方法仍會獲得呼叫，因為編譯器會為 `try`/`finally` 區塊實作中繼語言 (IL)。

### <a name="wrapping-the-call-to-idisposabledispose-in-a-tryfinally-block"></a>將 IDisposable.Dispose 的呼叫包裝於 Try/Finally 區塊中

下列程式碼範例顯示了如何將 `IDisposable.Dispose` 的呼叫包裝於 `try`/`finally` 區塊中：

```csharp
public void ReadText (string filename)
{
  ...
  string text;
  StreamReader reader = null;
  try {
    reader = new StreamReader (filename);
    text = reader.ReadToEnd ();
  } finally {
    if (reader != null) {
      reader.Dispose ();
    }
  }
  ...
}
```

`StreamReader` 類別會實作 `IDisposable`，而 `finally` 區塊則會呼叫 `StreamReader.Dispose` 方法來釋放資源。

如需詳細資訊，請參閱 [IDisposable 介面](https://developer.xamarin.com/api/type/System.IDisposable/)。

<a name="events" />

## <a name="unsubscribe-from-events"></a>取消訂閱事件

若要避免記憶體流失，在訂閱者遭到處置前，應先取消訂閱事件。 在取消訂閱事件之前，發行物件的事件委派都會具有一個封裝訂閱者事件處理常式委派的參考。 只要發行物件還有該參考，記憶體回收就不會回收訂閱者物件的記憶體。

下列程式碼範例示範如何取消訂閱事件：

```csharp
public class Publisher
{
  public event EventHandler MyEvent;

  public void OnMyEventFires ()
  {
    if (MyEvent != null) {
      MyEvent (this, EventArgs.Empty);
    }
  }
}

public class Subscriber : IDisposable
{
  readonly Publisher publisher;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    publisher.MyEvent += OnMyEventFires;
  }

  void OnMyEventFires (object sender, EventArgs e)
  {
    Debug.WriteLine ("The publisher notified the subscriber of an event");
  }

  public void Dispose ()
  {
    publisher.MyEvent -= OnMyEventFires;
  }
}
```

`Subscriber` 類別在其 `Dispose` 方法中取消訂閱事件。

參考回收通常會在使用事件處理常式及 Lambda 語法時發生，因為 Lambda 運算式可以取得參考並使物件繼續存留。 因此，匿名方法的參考可以儲存在欄位中，並用來取消訂閱事件，如下列程式碼範例：

```csharp
public class Subscriber : IDisposable
{
  readonly Publisher publisher;
  EventHandler handler;

  public Subscriber (Publisher publish)
  {
    publisher = publish;
    handler = (sender, e) => {
      Debug.WriteLine ("The publisher notified the subscriber of an event");
    };
    publisher.MyEvent += handler;
  }

  public void Dispose ()
  {
    publisher.MyEvent -= handler;
  }
}
```

`handler` 欄位保有匿名方法的參考，並會用於訂閱事件及取消訂閱事件。

<a name="weakreferences" />

## <a name="use-weak-references-to-prevent-immortal-objects"></a>使用弱式參考以防止 Immortal 物件

> [!NOTE]
> iOS 開發人員應檢閱有關[在 iOS 中避免循環參考](~/ios/deploy-test/performance.md#avoidcircularreferences)的文件，以確保其應用程式有效率地使用記憶體。

<a name="lazy" />

## <a name="delay-the-cost-of-creating-objects"></a>延遲建立物件的成本

延遲初始化可用來延遲物件的建立，直到第一次使用物件時。 這項技術主要用於改善效能、避免計算，以及減少記憶體需求。


建議您在下列兩種案例下，考慮針對建立過程相當耗費資源的物件使用延遲初始化：

- 應用程式可能不會使用到物件。
- 在建立該物件前，有其他耗費資源的的作業需要完成。

`Lazy<T>` 類別可用於定義延遲初始化的類型，如下列程式碼範例中所示：

```csharp
void ProcessData(bool dataRequired = false)
{
  Lazy<double> data = new Lazy<double>(() =>
  {
    return ParallelEnumerable.Range(0, 1000)
                 .Select(d => Compute(d))
                 .Aggregate((x, y) => x + y);
  });

  if (dataRequired)
  {
    if (data.Value > 90)
    {
      ...
    }
  }
}

double Compute(double x)
{
  ...
}
```

延遲初始化會在第一次存取 `Lazy<T>.Value` 屬性時發生。 包裝於其中的類型會在第一次存取時建立及傳回，並會儲存起來供任何未來的存取使用。

如需延遲初始化的詳細資訊，請參閱[延遲初始設定](https://msdn.microsoft.com/en-us/library/dd997286(v=vs.110).aspx)。

<a name="async" />

## <a name="implement-asynchronous-operations"></a>實作非同步作業

.NET 針對其許多的 API 提供了非同步的版本。 與同步 API 不同，非同步 API 會確保作用中的執行緒永遠不會封鎖呼叫執行緒很長一段時間。 因此，當從 UI 執行緒呼叫 API 時，建議您使用非同步 API (若可用的話)。 這可讓 UI 執行緒保持在未遭封鎖的狀態，協助改善使用者使用應用程式的體驗。

此外，長時間的作業應在背景執行緒中執行，以避免封鎖 UI 執行緒。 .NET 提供了 `async` 及 `await` 關鍵字，可讓您撰寫於背景執行緒中執行長時間作業的非同步程式碼，並在完成時存取執行結果。 然而，雖然長時間作業可使用 `await` 關鍵字非同步執行，但這仍然不會保證作業會在背景執行緒中執行。 因此，您可以將長時間作業傳遞給 `Task.Run` 來完成這項操作，如下列程式碼範例中所示：

```csharp
public class FaceDetection
{
  ...
  async void RecognizeFaceButtonClick(object sender, EventArgs e)
  {
    await Task.Run(() => RecognizeFace ());
    ...
  }

  async Task RecognizeFace()
  {
    ...
  }
}
```

`RecognizeFace` 方法會在背景執行緒中執行，`RecognizeFaceButtonClick` 方法則會等待 `RecognizeFace` 方法完成之後才會繼續。

長時間作業也應支援取消。 例如，若使用者在應用程式中巡覽，可能沒有必要繼續長時間的作業。 實作取消的模式如下所示：

- 建立 `CancellationTokenSource` 執行個體。 這個執行個體會管理及傳送取消通知。
- 將 `CancellationTokenSource.Token` 屬性值傳遞給每個可取消的工作。
- 針對每個工作提供機制，以回應取消。
- 呼叫 `CancellationTokenSource.Cancel` 方法來提供取消通知。

> [!IMPORTANT]
> `CancellationTokenSource` 類別實作 `IDisposable` 介面，因此當 `CancellationTokenSource` 執行個體完成工作時，應叫用 `CancellationTokenSource.Dispose` 方法。



如需詳細資訊，請參閱[非同步支援概觀](~/cross-platform/platform/async.md)。

<a name="sgen" />

## <a name="use-the-sgen-garbage-collector"></a>使用 SGen 記憶體回收行程

受控語言 (例如 C#) 會使用記憶體回收來回收任何已配置給不再使用之物件的記憶體。 Xamarin 平台使用的兩種記憶體回收行程為：

- [**SGen**](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/) – 這是新一代的記憶體回收行程，並且是 Xamarin 平台上的預設記憶體回收行程。
- [**Boehm**](http://www.hboehm.info/gc/) – 這是較為保守，非新一代的記憶體回收行程。 這是使用 Classic API 的 Xamarin.iOS 應用程式所使用的預設記憶體回收行程。

SGen 會利用三個堆積中的其中一個來為物件配置空間：

-  **托兒所 (The Nursery)** – 這是配置新小型物件的地方。 當托兒所的空間不足時，便會發生次要記憶體回收。 任何存留中的物件都會移動至主要堆積中。
-  **主要堆積** – 這是長時間執行的物件存放的地方。 若主要堆積中的記憶體不足，便會發生主要記憶體回收。 若主要記憶體回收無法釋放足夠的記憶體，則 SGen 便會向系統要求更多記憶體。
-  **大型物件空間** – 這是存放需要大於 8000 位元組之物件的地方。 大型物件不會從托兒所開始，而是會直接在此堆積中進行配置。

SGen 的其中一個優點便是執行次要記憶體回收所需要的時間與自上一次次要記憶體回收之後建立的新作用中物件之數量成比例。 這可減少記憶體回收對應用程式效能造成的影響，因為通常這些次要記憶體回收所需要耗費的時間比主要記憶體回收少。 主要記憶體回收仍會發生，只是頻率較低。

### <a name="reducing-pressure-on-the-garbage-collector"></a>降低記憶體回收行程的壓力

當 SGen 啟動記憶體回收時，它會在回收記憶體時停止應用程式的執行緒。 當記憶體獲得回收時，應用程式可能會出現短暫的暫停或在 UI 中出現間斷。 此暫停能夠被察覺到的程度取決於兩個因素：

1. **頻率** – 記憶體回收發生的頻率。 記憶體回收的頻率會在於集合之間配置更多記憶體時增加。
1. **期間** – 每個個別的記憶體回收所要耗費的時間。 這大約會與收集的作用中物件數量成比例。

共同上來說，當許多物件獲得配置，但未保持存留時，便會發生許多短暫的記憶體回收。 相反的，當配置新物件的過程緩慢且物件都保持存留時，便會發生較少但時間較長的記憶體回收。

若要降低記憶體回收行程的壓力，建議您遵循下列方針：

- 使用物件集區來避免在緊湊的迴圈中進行記憶體回收。 這對遊戲來說特別重要，因為遊戲中需要事先建立大部分的物件。
- 當不再需要時明確的釋放資源，例如：串流、網路連線、大型記憶體區塊及檔案。 如需詳細資訊，請參閱[釋放 IDisposable 資源](#idisposable)。
- 當不再需要時取消註冊事件處理常式，使物件可供收集。 如需詳細資訊，請參閱[取消訂閱事件](#events)。

<a name="linker" />

## <a name="reduce-the-size-of-the-application"></a>減少應用程式的大小

了解每個平台上的編譯處理序相當重要，如此您才能了解應用程式可執行檔的大小從何而來：

- iOS 應用程式會預先 (AOT) 編譯成 ARM 組合語言。 其中會包含 .NET Framework，並只有在啟用適當的連結器選項時才會去除沒用到的類別。
- Android 應用程式會編譯成中繼語言 (IL)，並與 MonoVM 和 Just-In-Time (JIT) 編譯一同封裝。 只有在啟用適當的連結器選項時才會去除沒用到的架構類別。
- Windows Phone 應用程式會編譯成 IL 並由內建的執行階段執行。

此外，若應用程式大量使用了泛型，則最終可執行檔的大小會進一步的增加，因為其中會包含原生編譯版本的泛型支援。

為了協助減少應用程式的大小，Xamarin 平台提供了一個連結器，作為建置工具的一部分。 根據預設，連結器為停用，您必須為應用程式在專案選項中啟用。 在建置時，它會執行應用程式的靜態分析，以決定應用程式實際使用到的類型和成員有哪些。 接著便會從應用程式移除任何未使用到的類型和方法。

下列螢幕擷取畫面顯示了 Visual Studio for Mac 中針對 Xamarin.iOS 專案的連結器選項：

![](memory-perf-best-practices-images/linker-options-ios.png)

下列螢幕擷取畫面顯示了 Visual Studio for Mac 中針對 Xamarin.Android 專案的連結器選項：

![](memory-perf-best-practices-images/linker-options-droid.png)

連結器提供了三種不同的設定，用來控制其行為：

-  **不要連結** – 未使用到的類型和方法會由連結器移除。 基於效能考量，這是偵錯組建的預設設定。
-  **僅連結 Framework SDK/SDK 組件** – 這項設定只會減少 Xamarin 隨附的組件大小。 使用者程式碼不會受到影響。
-  **連結所有組件** – 這是更為積極的最佳化選項。該選項會以 SDK 組件和使用者程式碼為目標。 針對繫結，此選項會移除未使用到的支援欄位，以使得每個執行個體 (或繫結物件) 變得更小，取用更少記憶體。

「連結所有組件」應謹慎使用，因為其可能會使得應用程式以非預期的方式執行。 連結器執行的靜態分析可能會無法正確識別所有必要的程式碼，導致太多的程式碼從編譯後的應用程式中移除。 這個情況只有在執行階段，當應用程式當機時才會顯現。 正因為如此，建議您在變更連結器行為前徹底的測試應用程式。

若測試確實揭露了連結器不正確的移除了類別或方法，您可以標記非靜態參考的類型或方法，但應用程式要求您必須使用下列其中一個屬性：

-  `Xamarin.iOS.Foundation.PreserveAttribute` – 此屬性用於 Xamarin.iOS 專案。
-  `Android.Runtime.PreserveAttribute` – 此屬性用於 Xamarin.Android 專案。

例如，您可能需要保留動態執行個體化類型的預設建構函式。 此外，使用 XML 序列化也可能需要保留類型的屬性。

如需詳細資訊，請參閱 [iOS 連結器](~/ios/deploy-test/linker.md)及 [Android 連結器](~/android/deploy-test/linker.md)。

### <a name="additional-size-reduction-techniques"></a>其他減少大小的技術

行動裝置有各種不同的 CPU 架構。 因此，Xamarin.iOS 和 Xamarin.Android 會產生「龐大的二進位檔案」，當中包含了適用於每一種 CPU 架構的應用程式編譯版本。 這可確保行動應用程式可在裝置上執行，而不需考慮到 CPU 架構。

下列步驟可用來進一步減少應用程式可執行檔的大小：

- 確定已產生發行組建。
- 減少應用程式為其建置的架構數量，避免產生龐大的二進位檔案。
- 確認已使用 LLVM 編譯器，以產生更進一步最佳化的可執行檔。
- 減少應用程式受控程式碼的大小。 這可透過在每個組件上啟用連結器 (iOS 專案使用「全部連結」，Android 專案使用「連結所有組件」) 來達成。

Android 應用程式也可以將每個 ABI (「架構」) 分割成個別的 APK。
在此部落格文章中進一步了解：[How To Keep Your Android App Size Down](http://motzcod.es/post/112072508362/how-to-keep-your-android-app-size-down) (如何降低您的 Android 應用程式大小)。

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>最佳化影像資源

影像是應用程式所使用之資源中成本最高的一種資源，且經常以高解析度擷取。 雖然這可以建立充滿細節的鮮明影像，但顯示這種影像的應用程式通常會需要更多 CPU 使用量來解碼影像，以及更多的記憶體來儲存解碼後的影像。 當影像可以縮小以供顯示之用時，在記憶體中解碼高解析度的影像便相當浪費。 您可以藉由為儲存的影像建立多個接近預測顯示大小的解析度版本，來減少 CPU 使用量和記憶體使用量。 例如，相較於在全螢幕中顯示的影像，在清單檢視中顯示的影像應為較低的解析度。 此外，載入高解析度影像的縮小版本也能更有效率的進行顯示，且只會對記憶體造成極小的影響。 如需詳細資訊，請參閱[有效率的載入大型點陣圖](https://developer.xamarin.com/recipes/android/resources/general/load_large_bitmaps_efficiently/)。

與影像解析度無關，顯示影像資源可能會大幅增加應用程式的記憶體使用量。 因此應該只有在必要時才建立它們，且應在應用程式不再需要它們時應立即釋出。

<a name="activationperiod" />

## <a name="reduce-the-application-activation-period"></a>減少應用程式的啟用期間

所有應用程式都會有一個「啟用期間」，即應用程式啟動時與應用程式準備好可供使用時之間的時間長度。 此啟用期間會成為使用者對應用程式的第一印象，因此為了博得使用者對應用程式良好的第一印象，減少啟用期間並讓使用者察覺到是非常重要的。

在應用程式顯示其初始 UI 前，應用程式應提供啟動顯示畫面來向使用者表示應用程式正在啟動中。 若應用程式無法快速的顯示其初始 UI，啟動顯示畫面應用於向使用者提示啟用期間的進度，好讓使用者能夠放心並知道應用程式並未停止回應。 這個讓使用者放心的措施可以是一個進度列或類似的控制項。

在啟用期間，應用程式會執行啟用邏輯，通常包含載入及處理資源。 啟用期間可透過確認必要的資源已和應用程式一同封裝，而非從遠端擷取來減少。 例如，在某些情況下，在啟用期間載入本機儲存的預留位置資料可能會是合適的選擇。 然後，當初始 UI 顯示時，使用者便可以與應用程式互動，並且預留位置資料也能從遠端來源逐漸取代。 此外，應用程式的啟用邏輯應僅執行足以讓使用者開始使用應用程式的必要工作。 延遲載入其他組件可能會有幫助，因為組件會在第一次使用時才進行載入。

<a name="webservicecommunication" />

## <a name="reduce-web-service-communication"></a>減少 Web 服務通訊

從應用程式連線到 Web 服務可能會對應用程式效能造成影響。 例如，增加網路頻寬的使用可能會導致裝置電池的使用量增加。 此外，使用者可能會在頻寬遭到限制的環境中使用應用程式。 因此，限制應用程式與 Web 服務間的頻寬使用率是相當合理的。

其中一個減少應用程式頻寬使用率的方法便是在透過網路傳送資料前先進行壓縮。 然而，壓縮過程所造成的額外 CPU 使用量也可能會導致電池的使用量增加。 因此，您應謹慎評估，再決定是否要在網路中移動壓縮後的資料。

需要考慮的另一個問題便是在應用程式與 Web 服務間移動之資料的格式。 兩種主要的格視為可延伸標記語言 (XML) 和 JavaScript 物件標記法 (JSON)。 XML 是可產生相對較大資料裝載的文字式交換格式，因為其包含了大量的格式化字元。 JSON 是可產生壓縮資料裝載的文字式交換格式，可在傳送及接收資料時減少頻寬的需求。 因此，通常針對行動應用程式，JSON 是偏好使用的格式。

通常會建議在應用程式與 Web 服務間傳輸資料時使用資料傳輸物件 (DTO)。 DTO 包含了用於在網路上傳輸的一組資料。 藉由使用 DTO，在單一遠端呼叫中便可以傳輸更多資料，進而協助減少應用程式進行遠端呼叫的次數。 一般而言，帶有較大資料裝載的遠端呼叫所需要耗費的時間與僅帶有較小資料裝載的呼叫相似。

從 Web 服務擷取的資料應在本機進行快取，並使用快取後的資料，而非重複從 Web 服務擷取資料。 然而，當採用這個方法時，您應實作適當的快取策略，來在 Web 服務上的資料變更時更新本機快取內的資料。

## <a name="summary"></a>總結

本文已描述與討論用來增加以 Xamarin 平台建置之應用程式效能的技巧。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。

## <a name="related-links"></a>相關連結

- [Xamarin.iOS 效能](~/ios/deploy-test/performance.md)
- [Xamarin.Android 效能](~/android/deploy-test/performance.md)
- [Xamarin Profiler 簡介](~/tools/profiler/index.md)
- [Xamarin.Forms 效能](~/xamarin-forms/deploy-test/performance.md)
- [非同步支援概觀](~/cross-platform/platform/async.md)
- [IDisposable](https://developer.xamarin.com/api/type/System.IDisposable/)
- [在 Xamarin 應用程式中避免常見錯誤 (影片)](https://university.xamarin.com/guestlectures/avoiding-common-pitfalls-in-xamarin-apps) \(英文\)

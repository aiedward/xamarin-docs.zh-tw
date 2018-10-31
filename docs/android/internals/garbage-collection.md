---
title: 記憶體回收
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 814e975f57023424618c5ea403126f36f87467a7
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50235008"
---
# <a name="garbage-collection"></a>記憶體回收

Xamarin.Android 會使用 Mono[簡單新一代的記憶體回收行程](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/)。 這是具有兩個層代的標示並清除記憶體回收行程並*大型物件空間*，有兩種類型的集合： 

-   次要的集合 （收集 Gen0 堆積） 
-   主要 （收集 Gen1 和大型物件空間堆積） 的集合。 

> [!NOTE]
> 如果沒有明確的集合，透過[GC。Collect （)](xref:System.GC.Collect)集合*隨選*根據堆積配置。 *這不是使用參考計數系統*; 物件*將不會收集如有任何未完成的參考*，或當範圍已結束。 次要的堆積的新配置的記憶體不足時，會執行 GC。 如果不有任何配置，它不會執行。


次要集合便宜且頻繁，而且用來收集最近配置的無作用的物件。 之後每幾 MB 的已配置的物件執行了次要的集合。 次要的集合可能會藉由呼叫手動執行[GC。收集 (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

主要集合是昂貴且較不頻繁，並用來回收無作用的所有物件。 記憶體耗盡的目前的堆積大小 （在之前調整堆積的大小） 之後，會執行主要的集合。 主要的集合可能會藉由呼叫手動執行[GC。收集 （）](xref:System.GC.Collect)或藉由呼叫[GC。收集 (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_)引數與[GC。MaxGeneration](xref:System.GC.MaxGeneration)。 



## <a name="cross-vm-object-collections"></a>跨 VM 物件的集合

有三種類別的物件類型。

-   **受管理物件**： 完成這件事的型別*不*繼承自[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) ，例如[System.String](xref:System.String)。 
    這些會正常收集 GC。 

-   **Java 物件**: Java 型別有 Android 執行階段 VM 內，但不是公開給 Mono VM。 這些相當枯燥的而且不會進一步討論。 這些會正常收集 Android 執行階段的虛擬機器。 

-   **物件對等互連**： 型別，能夠實作[IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) ，例如所有[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)並[Java.Lang.Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/)子類別。 這些類型的執行個體具有兩個 「 halfs"*受管理的對等*並*原生對等*。 受管理的對等是的執行個體C#類別。 原生的對等是 VM，在 Android 執行階段內的 Java 類別的執行個體和C# [IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/)屬性包含原生的對等的 JNI 全域參考。 


有兩種類型的原生的對等：

-   **Framework 對等**:"Normal"Java 類型，例如 Xamarin.Android 中，完全不知道[android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)。

-   **使用者對等**: [Android 可呼叫包裝函式](~/android/platform/java-integration/working-with-jni.md)產生在建置階段針對每個應用程式內出現的 Java.Lang.Object 子類別。


因為有兩個 Vm 的 Xamarin.Android 程序中，有兩種類型的記憶體回收：

-   Android 執行階段集合 
-   Mono 的集合 

Android 執行階段集合運作正常，但有一點要注意： JNI 全域參考會被視為 GC 根目錄。 因此，如果沒有 JNI 全域參考緊握 VM 物件，該物件的 Android 執行階段*無法*收集，即使它不符合記憶體回收資格。

Mono 的集合是有趣的地方。 正常回收受控的物件。 對等物件會收集藉由執行下列程序：

1.  Mono 集合符合資格的所有對等物件已被取代的 JNI 弱式全域參考其 JNI 全域參考。 

2.  VM GC Android 執行階段會叫用。 可能會收集任何原生對等執行個體。 

3.  會檢查 JNI 弱式全域參考 (1) 中所建立。 如果已經收集弱式參考，會收集的對等物件。 如果弱式參考具有*不*已收集，然後弱式參考會取代 JNI 全域參考並不會收集的對等物件。 注意： 在 API 14 +，這表示，從傳回的值`IJavaObject.Handle`GC 之後可能會變更。 

最終結果，這是對等物件的執行個體將會存留，只要它由其中一個參考 managed 程式碼 (例如儲存在`static`變數) 或 Java 程式碼所參考。 此外，原生的對等的存留期將會延伸超過什麼它們就 live、 原生對等電腦無法可回收，原生的對等和受管理的對等，可回收之前。


## <a name="object-cycles"></a>物件循環

對等物件會以邏輯方式出現在 Android 執行階段和 Mono VM 中。 例如， [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)受管理的對等執行個體將會有對應[android.app.Activity](http://developer.android.com/reference/android/app/Activity.html) framework 對等 Java 執行個體。 所有物件，繼承自[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)可預期有這兩個 Vm 中的表示法。 

表示在這兩個 Vm 的所有物件都會都有延伸也就是只在單一 VM 內存在的物件相較之下的存留期 (例如[ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601))。 呼叫[GC。收集](xref:System.GC.Collect)Xamarin.Android GC 需求以確保的其中一個 VM 未參考的物件之前收集，則一定不會收集這些物件。 

若要縮短物件存留期[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)應叫用。 這將會手動 「 斷絕 」 兩個 Vm 所釋放的全域的參考，因此要收集更快的物件之間的物件上的連線。 


## <a name="automatic-collections"></a>自動的集合

開頭[發行 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0)，Xamarin.Android 會自動執行完整的 GC 時超出 grf 閾值。 此閾值為 90%的已知的最大 grefs 平台： 1800 grefs (2000 最大)，在模擬器上的和 46800 grefs 硬體 (最大 52000) 上的。 *注意︰* Xamarin.Android 只會計算由 grefs [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)，並不會知道在處理程序中建立的任何其他 grefs。 這是啟發學習法*只*。 

執行自動收集時，偵錯記錄檔將會顯示類似下面的訊息：

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

這個的相符項目是不具決定性，並可能會發生在種減輕不適當的時間 （例如中間圖形轉譯）。 如果您看到此訊息，您可能想要執行明確的集合，以在其他地方，或您可能想要嘗試[減少對等物件的存留期](#Helping_the_GC)。 

## <a name="gc-bridge-options"></a>GC 橋接器選項

Xamarin.Android 提供透明的記憶體管理與 Android 和 Android 的執行階段。 它會實作為 Mono 的記憶體回收行程呼叫的延伸模組*GC 橋接器*。 

GC 橋接的運作期間 Mono 進行記憶體回收和出對等物件需要包含 Android 執行階段堆積驗證其 「 作用 」 的數字。 GC 橋接器會執行下列步驟 （依順序） 來判斷：

1.  引發到 Java 物件，其代表的不可能執行到的對等物件的 mono 參考圖形。 

2.  執行 Java GC。

3.  確認哪些物件是真的無作用。 

這個複雜的程序可讓類別的子`Java.Lang.Object`自由參考任何物件，並移除的 Java 物件可以繫結至任何限制C#。 由於這種複雜性，橋接器處理程序可以是非常耗費資源，它可能會造成明顯的暫停應用程式中。 如果應用程式發生重大暫停，值得調查下列三個 GC 橋接器實作的其中一個： 

-   **Tarjan** -GC 橋接器全新設計基礎[Robert Tarjan 演算法，並向後參考傳播](http://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm)。
    它可提供最佳效能，在我們的模擬工作負載，但它也具有較大的實驗性的程式碼共用。 

-   **新**-大變革的原始的程式碼，修正的二次方的行為的兩個執行個體，但保留核心演算法 (根據[Kosaraju 的演算法](http://en.wikipedia.org/wiki/Kosaraju's_algorithm)的強式尋找連接元件)。 

-   **舊**-原始實作 （被視為最穩定的三個）。 這是應用程式應該使用橋接器`GC_BRIDGE`暫停可接受。 


最能找出哪些 GC 橋接的運作的唯一方式是藉由在應用程式中進行實驗及分析輸出。 有兩種方式可收集的效能評定資料： 

-   **啟用記錄**-啟用記錄 (在中所述[組態](~/android/internals/garbage-collection.md)區段) 的每一個 GC Bridge 選項，然後擷取並比較每個設定的記錄檔輸出。 檢查`GC`訊息，每個選項，尤其是，`GC_BRIDGE`訊息。 非互動式應用程式是可容忍，但暫停 60 毫秒，非常易於互動的應用程式 （例如遊戲） 上面會有問題，請暫停最多 150ms年。 

-   **啟用 bridge 會計**-橋接器計量會顯示橋接器的程序所涉及的每個物件所指物件的平均成本。 依大小排序這項資訊會提供有關什麼保留額外物件的最大數量的提示。 


若要指定的`GC_BRIDGE`選項的應用程式應該使用，則會傳遞`bridge-implementation=old`，`bridge-implementation=new`或是`bridge-implementation=tarjan`到`MONO_GC_PARAMS`環境變數，例如： 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

預設值是**Tarjan**。 如果您發現的迴歸，您可能會發現需要將此選項設定為**舊**。 此外，您可以選擇使用更高的穩定性**舊**選項時如果**Tarjan**不會產生的效能改進。 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>協助 GC

有多種方式協助降低記憶體使用與收集時間的 GC。



### <a name="disposing-of-peer-instances"></a>處置的對等執行個體

GC 具有不完整的檢視程序，以及可能無法執行時記憶體不足因為 GC 並不知道該記憶體不足。 

例如，執行個體[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)型別或衍生型別是至少 20 個位元組大小 (有變更恕不另行通知等等等。)。 
[Managed 可呼叫包裝函式](~/android/internals/architecture.md)因此無法新增額外的執行個體成員，當您擁有[Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) 10 MB 的記憶體，blob 參考執行個體 Xamarin.Android 的 GC 不會知道&ndash;GC將會看到 20 個位元組的物件，因此無法判斷它連結，而導致 10 MB 的記憶體運作 Android 執行階段配置的物件。 

經常必須協助 GC。 不幸的是， *GC。AddMemoryPressure()* 和*GC。RemoveMemoryPressure()* 不支援，因此，如果您*知道*只釋放大型 Java 端配置的物件圖形，您可能需要手動呼叫[GC。Collect （)](xref:System.GC.Collect)提示 GC 釋放 Java 端的記憶體，或者您可以明確處置*Java.Lang.Object*子類別，請中斷受管理的可呼叫包裝函式和 Java 執行個體之間的對應。 例如，請參閱[Bug 1084](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6)。 


> [!NOTE]
> 您必須是*極*處置時請小心`Java.Lang.Object`子類別的執行個體。

若要降低記憶體損毀的可能性，觀察下列指導方針時呼叫`Dispose()`。


#### <a name="sharing-between-multiple-threads"></a>多個執行緒之間共用

如果*Java 或受控*多個執行緒之間可以共用執行個體*不應`Dispose()`d*，**曾經**。 比方說， [`Typeface.Create()`](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle)) 
可能會傳回*快取執行個體*。 如果多個執行緒提供相同的引數，它們將會取得*相同*執行個體。 因此，`Dispose()`的 ing`Typeface`從一個執行緒的執行個體可能會使其他執行緒，可能會導致`ArgumentException`從`JNIEnv.CallVoidMethod()`（還有其他） 因為從另一個執行緒已處置的執行個體。 


#### <a name="disposing-bound-java-types"></a>正在處置的繫結的 Java 類型

繫結的 Java 型別的執行個體時，執行個體可以處置 *，只要*執行個體不會重複使用從 managed 程式碼*和*Java 執行個體無法在執行緒 （請參閱上一個之間共用`Typeface.Create()`討論)。 （做出這個決定可能很困難）。在 Java 執行個體進入下一次的 managed 程式碼*新*包裝函式會建立它。 

說到可繪製資源來與其他耗用大量資源的執行個體，這是通常很有用：

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

以上是安全因為對等的[Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) Framework 對等，將參考傳回*不*使用者對等。 `Dispose()`呼叫的結尾`using`區塊將會中斷 managed 之間的關聯性[Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/)和 framework [Drawable](http://developer.android.com/reference/android/graphics/drawable/Drawable.html)情況下，允許在 Java 執行個體當 Android 執行階段必須收集。 這時候*未*安全，如果使用者對等的參考對等執行個體，這裡我們使用 「 外部 」 的資訊來*知道*，`Drawable`使用者對等，不能參考，因此`Dispose()`呼叫是安全的。 


#### <a name="disposing-other-types"></a>處置其他類型 

如果執行個體是指未繫結 Java 型別的型別 (例如自訂`Activity`)， **DO NOT**呼叫`Dispose()`除非您*知道*，Java 程式碼不會呼叫覆寫的方法，執行個體。 若要這樣做的失敗會造成[ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls)。 

例如，如果您有自訂按一下接聽程式：

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

您*不應該*處置這個執行個體，因為 Java 會嘗試將在未來叫用它的方法：

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>使用明確的檢查，以避免例外狀況

如果您已實作[Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/)方法多載，避免觸碰牽涉到的 JNI 的物件。 如此一來可能會建立*雙精度浮點數 dispose*可讓您的程式碼 （嚴重） 的情況下嘗試存取基礎 Java 物件已經過記憶體回收。 如此一來，就會產生類似下面的例外狀況： 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

當第一個物件的 dispose 會導致成員，才能變成 null，並接著後續存取嘗試這個 null 的成員會導致擲回例外狀況時，就會發生這種情況通常。 具體而言，物件的`Handle`（這會連結到其基礎的 Java 執行個體的受管理的執行個體） 上第一個 dispose 中，已失效，但 managed 程式碼仍會嘗試存取此基礎的 Java 執行個體，即使它已不再可用 （請參閱[受管理的可呼叫包裝函式](~/android/internals/architecture.md#Managed_Callable_Wrappers)如需詳細資訊的 Java 執行個體與受管理的執行個體之間的對應)。 

若要避免這個例外狀況的好方法是明確確認在您`Dispose`方法的 managed 執行個體與基礎 Java 執行個體之間的對應仍然有效的; 也就是說，以查看是否核取的物件`Handle`為 null (`IntPtr.Zero`)才能存取其成員。 例如，下列`Dispose`方法存取`childViews`物件： 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);
        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```

如果初始的 dispose 傳遞的原因`childViews`具有無效`Handle`，則`for`迴圈存取將會擲回`ArgumentException`。 藉由新增明確`Handle`null 檢查之前，先`childViews`存取，下列`Dispose`方法可防止發生例外狀況： 

```csharp
class MyClass : Java.Lang.Object, ISomeInterface 
{
    protected override void Dispose (bool disposing)
    {
        base.Dispose (disposing);

        // Check for a null handle:
        if (this.childViews.Handle == IntPtr.Zero)
            return;

        for (int i = 0; i < this.childViews.Count; ++i)
        {
            // ...
        }
    }
}
```


### <a name="reduce-referenced-instances"></a>減少受參考的執行個體

每當執行個體`Java.Lang.Object`型別或子類別會掃描期間的 GC，整個*物件圖形*必須掃描的執行個體參考。 物件圖形是物件執行個體的 「 根執行個體 」 指的是，一組*加上*哪些根執行個體所參考的所有項目是指以遞迴方式。 

請考慮下列類別：

```csharp
class BadActivity : Activity {

    private List<string> strings;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```

當`BadActivity`是建構，物件圖形會包含 10004 的執行個體 (1 x `BadActivity`，1x `strings`，1 x`string[]`所持有`strings`，10000 倍的字串執行個體)，*所有*的這必須是每次掃描`BadActivity`掃描執行個體。 

這可能會有不利的影響您集合的時間，在導致增加 GC 暫停時間。 

您可以協助的 GC*減少*物件圖形的根項目是否由使用者對等執行個體的大小。 在上述範例中，這可以藉由移動`BadActivity.strings`成個別的類別不繼承自 Java.Lang.Object: 

```csharp
class HiddenReference<T> {

    static Dictionary<int, T> table = new Dictionary<int, T> ();
    static int idgen = 0;

    int id;

    public HiddenReference ()
    {
        lock (table) {
            id = idgen ++;
        }
    }

    ~HiddenReference ()
    {
        lock (table) {
            table.Remove (id);
        }
    }

    public T Value {
        get { lock (table) { return table [id]; } }
        set { lock (table) { table [id] = value; } }
    }
}

class BetterActivity : Activity {

    HiddenReference<List<string>> strings = new HiddenReference<List<string>>();

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        strings.Value = new List<string> (
                Enumerable.Range (0, 10000)
                .Select(v => new string ('x', v % 1000)));
    }
}
```


## <a name="minor-collections"></a>次要的集合

次要的集合可能會藉由呼叫手動執行[GC。Collect(0)](xref:System.GC.Collect)。 次要的集合是便宜 （相較於主要集合），但不要有重大的固定成本，因此您不希望太頻繁，觸發程序，而且應該有幾毫秒的暫停時間。 

如果您的應用程式 」 工作週期 」 反覆完成同一件事是，它可能建議您以手動方式執行次要的集合，一旦工作週期已結束。 範例 duty 包括： 

-  單一的遊戲畫面格的轉譯週期。
-  整個互動，以指定應用程式對話方塊 （開啟、 填滿關閉） 
-  網路要求，以重新整理/同步處理應用程式資料的群組。



## <a name="major-collections"></a>主要的集合

主要的集合可能會藉由呼叫手動執行[GC。Collect （)](xref:System.GC.Collect)或`GC.Collect(GC.MaxGeneration)`。 

他們應該很少執行，且可能是有一秒的暫停時間樣式的 Android 裝置上，收集 512MB 堆積時。 

主要的集合應該只能以手動方式叫用，如果有的話： 

-   結尾的冗長的待命週期和長時間暫停時不會向使用者顯示問題。 

-   在 覆寫[Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/)方法。 



## <a name="diagnostics"></a>診斷

若要追蹤時建立和終結全域的參考，您可以設定[debug.mono.log](~/android/troubleshooting/index.md)系統屬性，以包含[ *grf* ](~/android/troubleshooting/index.md)及/或[ *gc*](~/android/troubleshooting/index.md)。 



## <a name="configuration"></a>組態

可以透過設定來設定 Xamarin.Android 記憶體回收行程`MONO_GC_PARAMS`環境變數。 可能的建置動作設定環境變數[AndroidEnvironment](~/android/deploy-test/environment.md)。

`MONO_GC_PARAMS`環境變數是以逗號分隔清單的下列參數： 

-   `nursery-size` = *大小*： 設定托兒所的大小。 大小以位元組為單位指定，而且必須是 2 的乘冪。 尾碼`k`，`m`和`g`可用來分別指定 kb、 百萬-和 gb。 托兒所是第一代 （兩個）。 較大的托兒所通常會加速計劃，但會很明顯地使用更多的記憶體。 預設托兒所大小 512 kb。 

-   `soft-heap-limit` = *大小*： 的目標最大受管理的應用程式的記憶體耗用量。 當記憶體使用量低於指定的值時，則會將 GC 最適合執行時間 （較少的集合）。 
    超過此限制，GC 最適合的記憶體使用量 （更多的集合）。 

-   `evacuation-threshold` = *閾值*： 設定疏散臨界值百分比。 值必須是範圍 0 到 100 的整數。 預設為 66。 如果集合的掃掠階段發現的特定堆積區塊類型的佔用量低於這個百分比，它會執行複製的集合，該區塊型別在下一步 的主要集合中，藉此還原到接近 100%的佔用量。 0 的值會撤出關閉。 

-   `bridge-implementation` = *橋接實作*： 這會將 GC 橋接器選項，以協助解決 GC 效能問題。 有三個可能的值：*舊*，*新*， *tarjan*。

-   `bridge-require-precise-merge`： 橋接器則包含這些可能，在少數情況下，會使物件的最佳化 Tarjan 收集一個 GC 之後，第一次記憶體回收。 包括這個選項會停用最佳化，使 Gc 可能比較緩慢，但更容易預測。

比方說，若要設定 GC 的堆積大小限制為 128 MB，將新檔案新增到您的專案**建置動作**的`AndroidEnvironment`內容： 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```

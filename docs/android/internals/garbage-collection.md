---
title: "記憶體回收"
ms.topic: article
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: db277f20e63a59690ffaa8a8544ff9540578d3f5
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="garbage-collection"></a>記憶體回收

Xamarin.Android 使用單聲道的[層代簡單式記憶體回收行程](http://www.mono-project.com/Compacting_GC)。 這是具有兩個層代標記掃掠記憶體回收行程和*大型物件空間*，有兩種類型的集合： 

-   次要的集合 （收集 Gen0 堆積） 
-   主要 （收集 Gen1 和大型物件空間堆積） 的集合。 

> [!NOTE]
> 如果沒有明確的集合，透過[GC。Collect()](xref:System.GC.Collect)集合*視*、 根據堆積配置。 *這不是參考計數系統*; 物件*將不會收集因為沒有未完成參考*，或當範圍已結束。 次要的堆積的新配置的記憶體不足時，將會執行 GC。 如果有任何配置，不會執行它。


次要集合廉價頻繁，，用來收集最近配置並無作用物件。 次要集合每幾 MB 的配置的物件之後執行。 次要的集合可能會以手動方式執行藉由呼叫[GC。收集 (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

主要集合昂貴且較不頻繁，，用來回收無作用的所有物件。 一旦目前的堆積大小 （在之前調整大小的堆積） 耗盡記憶體，則會執行主要的集合。 主要的集合可能會以手動方式執行藉由呼叫[GC。收集 （）](xref:System.GC.Collect)或藉由呼叫[GC。收集 (int)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_)與引數[GC。MaxGeneration](xref:System.GC.MaxGeneration)。 



## <a name="cross-vm-object-collections"></a>跨 VM 物件的集合

有三個類別的物件類型。

-   **受管理物件**： 類型完成這件事*不*繼承自[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/) ，例如[System.String](xref:System.String)。 
    這些會正常收集 GC。 

-   **Java 物件**: Android 的執行階段 VM 內出現，但不會公開至 Mono VM 的 Java 類型。 這些無趣，且不會進一步討論。 這些會正常收集 Android 的執行階段 VM。 

-   **對等物件**： 型別實作哪些[IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/) ，例如所有[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)和[Java.Lang.Throwable](https://developer.xamarin.com/api/type/Java.Lang.Throwable/)子類別。 這些類型的執行個體有兩個 「 halfs"*受管理的對等*和*原生對等*。 受管理的對等是 C# 類別的執行個體。 原生的對等是 VM，在 Android 執行階段內的 Java 類別和 C# 的執行個體[IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/)屬性包含原生的對等的 JNI 全域參考。 


有兩種類型的原生對等體：

-   **Framework 對等體**： 知道 nothing Xamarin.Android，例如"Normal"Java 類型[android.content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)。

-   **使用者對等體**: [Android 可呼叫包裝函式](~/android/platform/java-integration/working-with-jni.md)產生在建置階段針對每個應用程式內出現的 Java.Lang.Object 子類別。


如有兩個 Vm Xamarin.Android 處理序內，有兩種類型的記憶體回收：

-   Android 的執行階段集合 
-   單聲道集合 

Android 的執行階段集合運作正常，但要注意： JNI 全域的參考會被視為 GC 根。 因此，如果沒有 JNI 全域參考 Android 的執行階段保留 VM 物件，物件*無法*收集，即使您因可進行記憶體回收。

單聲道集合是有趣的發生位置。 通常會回收 managed 的物件。 對等物件會收集藉由執行下列程序：

1.  所有對等物件可供單聲道回收有取代 JNI 弱式全域參考其 JNI 全域參考。 

2.  Android 的執行階段 VM 的 GC 會叫用。 可能會收集任何原生對等執行個體。 

3.  會檢查 JNI 弱式通用參考 (1) 中建立。 如果已經收集弱式參考，會收集對等物件。 如果具有弱式參考*不*已收集，然後弱式參考取代 JNI 全域的參考並不會收集對等物件。 注意： 在應用程式開發介面 14+，這表示，從傳回的值`IJavaObject.Handle`GC 之後可能會變更。 

最終結果，這是會即時對等物件的執行個體，只要它正由所有的 managed 程式碼 (例如儲存在`static`變數) 或 Java 程式碼所參考。 此外，原生的對等的存留期會擴充到什麼一樣否則 live、 原生的對等可回收之前無法用原生的對等和受管理的對等電腦都是可收集。


## <a name="object-cycles"></a>物件循環

對等物件是 Android 的執行階段和 Mono VM 內以邏輯方式出現。 例如， [Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)受管理的對等執行個體將會有對應[android.app.Activity](http://developer.android.com/reference/android/app/Activity.html) framework 對等 Java 執行個體。 所有物件繼承自[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)應該有兩個 Vm 內表示法。 

表示在這兩個 Vm 的所有物件都必須擴充相較於只在單一 VM 內存在的物件的存留期 (例如[ `System.Collections.Generic.List<int>` ](xref:System.Collections.Generic.List%601))。 呼叫[GC。收集](xref:System.GC.Collect)Xamarin.Android GC 需要以確保，任一個 VM 未參考的物件之前收集時一定不會收集這些物件。 

若要縮短物件存留期， [Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)應叫用。 這將會手動"斷絕"兩個 Vm 藉由釋放全域的參考，如此可讓要收集更快的物件之間的物件上的連線。 


## <a name="automatic-collections"></a>自動集合

開頭為[發行 4.1.0](https://developer.xamarin.com/releases/android/mono_for_android_4/mono_for_android_4.1.0)，Xamarin.Android 會自動執行完整的 GC 跨越 gref 臨界值時。 此臨界值為 90%的已知的最大 grefs 平台： 1800 grefs 模擬器 (2000 max)、 上的和 46800 grefs 硬體 (最大 52000) 上的。 *注意：* Xamarin.Android 只會計算由 grefs [Android.Runtime.JNIEnv](https://developer.xamarin.com/api/type/Android.Runtime.JNIEnv/)，並不會知道任何其他 grefs 程序中建立。 這是啟發學習法*只*。 

執行自動的集合時，偵錯記錄檔將會顯示類似下面的訊息：

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

這個項目不具決定性，並可能會發生在 （例如中間圖形轉譯）、 時間。 如果您看到此訊息，您可能想要執行明確的集合，以在其他地方，或您可能想要嘗試[縮短對等物件的存留期](#Helping_the_GC)。 

## <a name="gc-bridge-options"></a>GC 橋接器選項

Xamarin.Android 提供透明的記憶體管理的 Android 和 Android 的執行階段。 它會實作為單聲道的記憶體回收行程呼叫延伸*GC 橋接器*。 

GC 橋接的運作期間單聲道回收和出對等物件需要使用 Android 的執行階段堆積確認其"liveness 」 的數據。 GC 橋接器會判斷執行下列步驟 （依順序）：

1.  到 Java 物件所代表，誘使無法連線到對等物件的單聲道參考圖形。 

2.  執行 Java GC。

3.  確認哪些物件是真的無作用。 

這個複雜的程序可讓子類別的`Java.Lang.Object`自由地參考任何物件，並移除任何在哪一個 Java 物件可以繫結至 C# 的限制。 由於這種複雜性，橋接器處理序可能會很耗費資源，因此它可能會造成顯著的暫停應用程式中。 如果應用程式發生重大暫停，值得調查下列三個 GC 橋接器實作的其中一個： 

-   **Tarjan** -GC 橋接器全新設計基礎[Robert Tarjan 演算法並回溯參考傳播](http://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm)。
    它有最佳效能，在我們的模擬工作負載，但它也會有較大的共用的實驗性的程式碼。 

-   **新**-主要檢查原始的程式碼，修正二次方的行為的兩個執行個體，但保留的核心演算法 (根據[Kosaraju 的演算法](http://en.wikipedia.org/wiki/Kosaraju's_algorithm)強尋找已連接的元件)。 

-   **舊**-的原始實作 （被視為最穩定，這三個）。 這是應用程式應該使用橋接器`GC_BRIDGE`是可接受的暫停時間。 


最能找出哪些 GC 橋接的運作的唯一方式是藉由應用程式中進行實驗和分析輸出。 有兩種方式可以收集的效能資料： 

-   **啟用記錄**-啟用記錄功能 (如中所描述[組態](~/android/internals/garbage-collection.md)> 一節) 針對每一個 GC 橋接器選項，然後擷取並比較每個設定的記錄檔輸出。 檢查`GC`訊息每個選項，尤其是，`GC_BRIDGE`訊息。 暫停最多 150ms年，非互動式應用程式是在容忍範圍內，但暫停 60 毫秒非常互動式應用程式 （例如遊戲） 上方會有問題。 

-   **啟用橋接器會計**-橋接器計量會顯示由橋接器程序中每個物件所指物件的平均成本。 依大小排序這項資訊會提供提示項目所保留的最大數量的額外物件。 


若要指定的`GC_BRIDGE`選項應用程式應該使用，請將傳遞`bridge-implementation=old`，`bridge-implementation=new`或`bridge-implementation=tarjan`至`MONO_GC_PARAMS`環境變數，例如： 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

預設值是**Tarjan**。 如果您發現的迴歸，您可能會發現需要將此選項設定為**舊**。 此外，您可以選擇使用更高的穩定性**舊**選項如果**Tarjan**不會產生的效能改進。 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>協助 GC

有多個方式來幫助減少記憶體使用與收集時間的 GC。



### <a name="disposing-of-peer-instances"></a>處置的對等執行個體

GC 有不完整的處理程序，可能無法執行時記憶體不足因為 GC 不知道的記憶體不足。 

例如，執行個體[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)型別或衍生型別是至少 20 個位元組大小 (恕不另行通知，以此類推，等。)。 
[管理可呼叫包裝函式](~/android/internals/architecture.md)因此未加入其他執行個體成員，當您有[Android.Graphics.Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) 10 MB 的記憶體，blob 參考執行個體 Xamarin.Android 的 GC 不會知道&ndash;GC將會看到 20 位元組物件，且將無法判斷其連結，而導致 10 MB 的記憶體運作 Android runtime 配置的物件。 

若要協助 GC 經常必須。 不幸的是， *GC。AddMemoryPressure()*和*GC。RemoveMemoryPressure()*不支援，因此，如果您*知道*只釋放大型 Java 配置的物件圖形，您可能需要手動呼叫[GC。Collect()](xref:System.GC.Collect)提示 GC 釋放 Java 端的記憶體，或者您可以明確處置*Java.Lang.Object*子類別，請中斷受管理的可呼叫包裝函式和 Java 執行個體之間的對應。 例如，請參閱[Bug 1084](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6)。 


> [!NOTE]
> 您必須是*極*處置時請小心`Java.Lang.Object`子類別的執行個體。

記憶體損毀的可能性降到最低，觀察呼叫時的下列指導方針`Dispose()`。


#### <a name="sharing-between-multiple-threads"></a>多個執行緒之間共用

如果*Java 或 managed*多個執行緒，可以共用執行個體*不應該`Dispose()`d*，**曾經**。 例如， [ `Typeface.Create()` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/(System.String%2cAndroid.Graphics.TypefaceStyle))可能會傳回*快取執行個體*。 如果多個執行緒會提供相同的引數，將取得*相同*執行個體。 因此， `Dispose()`ing 的`Typeface`從一個執行緒的執行個體可能會使其他的執行緒，可能會導致`ArgumentException`從`JNIEnv.CallVoidMethod()`（和其他項目） 因為從另一個執行緒已處置的執行個體。 


#### <a name="disposing-bound-java-types"></a>處置繫結的 Java 類型

如果繫結的 Java 類型的執行個體，執行個體可以處置*只要*將不會從 managed 程式碼重複使用執行個體*和*Java 執行個體不能共用 （請參閱先前執行緒之間`Typeface.Create()`討論)。 （做出這個決定可能會難以。）下一次 Java 執行個體進入 managed 程式碼，*新*會為它建立包裝函式。 

當談到 Drawables 和其他資源大量執行個體時，這是通常很有用：

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

上述是安全因為對等的[Drawable.CreateFromPath()](https://developer.xamarin.com/api/member/Android.Graphics.Drawables.Drawable.CreateFromPath/) Framework 對等體，將參考傳回*不*使用者對等。 `Dispose()`結尾處呼叫`using`區塊將會中斷 managed 之間的關聯性[Drawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.Drawable/)和 framework [Drawable](http://developer.android.com/reference/android/graphics/drawable/Drawable.html)執行個體，可讓要讓 Java 執行個體Android 的執行階段必須為收集。 這會*不*安全，如果使用者對等的參考對等執行個體; 這裡我們使用 「 外部 」 資訊*知道*，`Drawable`無法參考使用者的對等體，因此`Dispose()`呼叫是安全的。 


#### <a name="disposing-other-types"></a>處置其他類型 

如果執行個體參考不是 Java 類型的繫結的型別 (例如自訂`Activity`)，**不**呼叫`Dispose()`除非您*知道*，Java 程式碼不會呼叫覆寫的方法，執行個體。 若要這樣做導致[ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls)。 

例如，如果您有自訂按一下接聽程式：

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

您*不應該*處置這個執行個體，因為 Java 會嘗試在未來叫用它的方法：

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>使用明確的檢查，以避免例外狀況

如果您已實作[Java.Lang.Object.Dispose](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose(System.Boolean)/)方法多載，避免接觸 JNI 牽涉到的物件。 如此一來可能會建立*雙 dispose*可讓您的程式碼 （受的傷足以） 的情況下嘗試存取已回收的基礎 Java 物件。 這樣做會產生與下列相似的例外狀況： 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

第一個物件的 dispose 會導致成員，才能成為 null 和空值，這個成員上的後續存取嘗試造成擲回例外狀況時，就會發生這種情況通常。 具體而言，此物件的`Handle`（這會連結到其基礎的 Java 執行個體的受管理的執行個體） 上第一個 dispose，失效，但 managed 程式碼仍會嘗試存取此基礎的 Java 執行個體，即使它已不再使用 （請參閱[受管理的可呼叫包裝函式](~/android/internals/architecture.md#Managed_Callable_Wrappers)如需詳細資訊的 Java 執行個體與受管理的執行個體之間的對應)。 

若要避免這個例外狀況的好方法是在明確驗證您`Dispose`方法的 managed 執行個體和基礎 Java 執行個體之間的對應仍然有效的; 也就是說，如果檢查物件的`Handle`為 null (`IntPtr.Zero`)之前存取它的成員。 例如，下列`Dispose`方法存取`childViews`物件： 

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

如果初始的 dispose 傳遞原因`childViews`有無效的`Handle`、`for`迴圈存取將會擲回`ArgumentException`。 藉由新增明確`Handle`null 檢查，第一個再`childViews`存取，下列`Dispose`方法可避免發生例外狀況： 

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

每當執行個體`Java.Lang.Object`類型或子類別會掃描期間 GC，整個*物件圖形*必須掃描的執行個體參考。 物件圖形是 「 根執行個體 」 指的是，物件執行個體的集合*加上*哪些根執行個體所參考的所有項目是指以遞迴方式。 

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

當`BadActivity`是建構，物件圖形包含 10004 執行個體 (1 x `BadActivity`，1 x `strings`，1 x`string[]`所持有`strings`，10000 x 字串執行個體)，*所有*的這必須是每當掃描`BadActivity`掃描執行個體。 

這可能會有不利的影響會在您收集時間，導致增加 GC 暫停時間。 

您可以協助的 GC*減少*物件圖形的根項目由使用者對等執行個體的大小。 在上述範例中，這可藉由來移動`BadActivity.strings`成個別的類別不會繼承自 Java.Lang.Object: 

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

次要的集合可能會以手動方式執行藉由呼叫[GC。Collect(0)](xref:System.GC.Collect)。 次要的集合是廉價 （當相較於主要集合），但沒有重大固定成本，所以您不想要常常，觸發，且必須有幾毫秒的暫停時間。 

如果您的應用程式 」 工作週期"反覆完成同一件事是，它可能會建議手動執行次要集合的情況，一旦工作週期已經結束。 範例待命週期包括： 

-  單一的遊戲框架轉譯循環。
-  整個互動與給定應用程式對話方塊 （開啟、 填滿，關閉） 
-  此群組的網路要求，以重新整理/同步處理應用程式資料。



## <a name="major-collections"></a>主要的集合

主要的集合可能會以手動方式執行藉由呼叫[GC。Collect()](xref:System.GC.Collect)或`GC.Collect(GC.MaxGeneration)`。 

它們應該很少執行而可能的第二個暫停的時間樣式的 Android 裝置上收集 512 MB 堆積時。 

主要的集合應該只能手動叫用，如果有的話： 

-   結尾的執行時間較長的待命週期和長時間的暫停時不會向使用者呈現問題。 

-   透過覆寫[Android.App.Activity.OnLowMemory()](https://developer.xamarin.com/api/member/Android.App.Activity.OnLowMemory/)方法。 



## <a name="diagnostics"></a>診斷

若要追蹤時建立和終結全域的參考，您可以設定[debug.mono.log](~/android/troubleshooting/index.md)系統屬性，以包含[ *gref* ](~/android/troubleshooting/index.md)及/或[ *gc*](~/android/troubleshooting/index.md)。 



## <a name="configuration"></a>組態

設定也可以設定 Xamarin.Android 記憶體回收行程`MONO_GC_PARAMS`環境變數。 可能的建置動作設定環境變數[AndroidEnvironment](~/android/deploy-test/environment.md)。

`MONO_GC_PARAMS`環境變數是以逗號分隔清單的下列參數： 

-   `nursery-size` = *大小*： 設定 nursery 的大小。 大小以位元組為單位指定，而且必須是 2 的乘冪。 尾碼`k`，`m`和`g`可以用來分別指定輪替、 百萬-和 gb。 Nursery 是第一個層代 （兩個）。 較大 nursery 通常會加速程式，但會很明顯地使用更多記憶體。 預設 nursery 大小 512 kb。 

-   `soft-heap-limit` = *大小*： 的目標最大值受管理的應用程式的記憶體耗用量。 當記憶體使用量低於指定的值時，GC 適合用於執行時間 （較少的集合）。 
    超過此限制，已最佳化記憶體使用量 （更多的集合） 的 GC。 

-   `evacuation-threshold` = *閾值*： 設定撤出臨界值百分比。 值必須是範圍 0 到 100 的整數。 預設為 66。 如果集合的掃掠階段找到特定堆積區塊類型的佔用量低於這個百分比時，它會執行該區塊型別中的下一個主要的集合，藉此還原到接近 100%的佔用量的複製集合。 值為 0 會關閉撤出。 

-   `bridge-implementation` = *橋接實作*： 這會將 GC 橋接器選項，以協助解決 GC 效能問題。 有三個可能值：*舊*，*新*， *tarjan*。

-   `bridge-require-precise-merge`： 橋接器會包含可能，在少數情況下，會造成物件的最佳化 Tarjan 收集一個 GC 後第一次記憶體回收。 包括這個選項會停用該最佳化，讓 Gc 更容易預測，但可能會變慢。

例如，若要設定在 GC 堆積大小限制為 128 MB，專案中加入新的檔案與**建置動作**的`AndroidEnvironment`的內容： 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```

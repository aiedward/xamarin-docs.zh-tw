---
title: 記憶體回收
ms.prod: xamarin
ms.assetid: 298139E2-194F-4A58-BC2D-1D22231066C4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 3b89f63d42b01140d73cd1551c75211d2fea8e27
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510719"
---
# <a name="garbage-collection"></a>記憶體回收

Xamarin 會使用 Mono 的[簡單代垃圾收集](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)行程。 這是具有兩個層代和*大型物件空間*的標記和清除行程, 其中包含兩種集合: 

-   次要集合 (收集 Gen0 堆積) 
-   主要集合 (收集 Gen1 和大型物件空間堆積)。 

> [!NOTE]
> 如果沒有透過 GC 的明確集合, 則為[。收集 ()](xref:System.GC.Collect)集合視*需要*而定, 視堆積配置而定。 *這不是參考計數系統*;一旦沒有未處理的參考, 或範圍結束時,*就不會收集*物件。 當次要堆積已用完新配置的記憶體不足時, 就會執行 GC。 如果沒有任何配置, 就不會執行。


次要集合既便宜又頻繁, 而且可用來收集最近配置和失效的物件。 次要集合會在每隔幾 MB 的設定物件之後執行。 次要集合可以藉由呼叫 GC 來手動執行[。Collect (0)](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_) 

主要集合既昂貴又不頻繁, 而且可用來回收所有不正確物件。 當記憶體耗盡目前堆積大小 (調整堆積的大小之前) 時, 會執行主要集合。 藉由呼叫 GC, 可以手動執行主要集合[。](xref:System.GC.Collect)藉由呼叫 GC 來收集 () 或[。](/dotnet/api/system.gc.collect#System_GC_Collect_System_Int32_)使用引數 GC 來收集 (int) [。MaxGeneration](xref:System.GC.MaxGeneration)。 



## <a name="cross-vm-object-collections"></a>跨 VM 物件集合

有三種類別的物件類型。

-   **Managed 物件**:*不會*繼承自[.java. Object](xref:Java.Lang.Object)的類型, 例如[system.string](xref:System.String)。 
    這些是由 GC 正常收集的。 

-   **JAVA 物件**:存在於 Android 執行時間 VM 中, 但不會公開至 Mono VM 的 JAVA 類型。 這些是乏味的, 不會進一步討論。 這些通常是由 Android 執行時間 VM 所收集。 

-   **對等物件**: 用來執行[IJAVAObject](xref:Android.Runtime.IJavaObject)的類型, 例如所有的[Java.Lang.Object](xref:Java.Lang.Object) 和 [Java.Lang.Throwable](xref:Java.Lang.Throwable)類別。 這些類型的實例有兩個「halfs」*受控對等*體和*原生對等*體。 受管理的對等是C#類別的實例。 原生對等是 Android 執行時間 VM 中 JAVA 類別的實例, 而C# [IJAVAObject](xref:Android.Runtime.IJavaObject.Handle)屬性包含原生對等的 JNI 全域參考。 


原生對等的類型有兩種:

-   **架構對等**:「一般」 JAVA 類型, 其不知道任何的 Xamarin, 例如[android. 內容](xref:Android.Content.Context)。

-   **使用者對等**:[Android](~/android/platform/java-integration/working-with-jni.md)可呼叫包裝函式, 會在組建期間針對應用程式內的每個 JAVA. lang.ini 物件子類別產生。


因為在 Xamarin Android 程式中有兩個 Vm, 所以有兩種類型的垃圾收集:

-   Android 執行時間集合 
-   Mono 集合 

Android 執行時間集合會正常運作, 但有一點要注意: JNI 全域參考會被視為 GC 根。 因此, 如果有一個 JNI 的全域參考保存在 Android 執行時間 VM 物件上, 則*無法*收集物件, 即使它也適用于集合亦然。

Mono 集合是一種有趣的地方。 系統會正常收集受管理物件。 對等物件是藉由執行下列進程來收集:

1.  所有適用于 Mono 集合的對等物件, 其 JNI 全域參考都會取代為 JNI 弱式全域參考。 

2.  已叫用 Android 執行時間 VM GC。 可能會收集任何原生對等實例。 

3.  系統會檢查在 (1) 中建立的 JNI 弱式全域參考。 如果已收集弱式參考, 則會收集對等物件。 如果*未*收集弱式參考, 則會以 JNI 全域參考取代弱式參考, 且不會收集對等物件。 注意: 在 API 14 + 上, 這表示從`IJavaObject.Handle`傳回的值可能會在 GC 之後變更。 

最後的結果就是對等物件的實例而言, 只要 managed 程式碼 (例如, 儲存在`static`變數中) 或 JAVA 程式碼所參考, 就會存留。 此外, 原生對等的存留期將會延伸到不會發生的情況, 因為原生對等將不會被可回收, 直到原生對等和 Managed 對等都可回收為止。


## <a name="object-cycles"></a>物件週期

對等物件會以邏輯方式出現在 Android 執行時間和 Mono VM 中。 例如, [android. 應用程式](xref:Android.App.Activity)管理的對等實例會有對應的[android 應用程式。活動](https://developer.android.com/reference/android/app/Activity.html)架構對等 JAVA 實例。 所有繼承自[.java. Object](xref:Java.Lang.Object)的物件都可以在這兩個 vm 內具有標記法。 

所有在這兩個 Vm 中都有表示的物件, 其存留期會與只存在於單一 VM (例如[`System.Collections.Generic.List<int>`](xref:System.Collections.Generic.List%601)) 中的物件相比較。 呼叫[GC。Collect](xref:System.GC.Collect)不一定會收集這些物件, 因為 Xamarin. ANDROID GC 必須確保該物件在收集之前不會被這兩個 VM 參考。 

為了縮短物件存留期, 應叫用[JAVA. lang.ini ()](xref:Java.Lang.Object.Dispose) 。 這會藉由釋放全域參考, 以手動方式在兩個 Vm 之間的物件上「伺服器」連線, 因此可更快速地收集物件。 


## <a name="automatic-collections"></a>自動集合

從[Release 4.1.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/mono_for_android_4/mono_for_android_4.1.0/index.md)開始, Xamarin 會在超過 grf 閾值時自動執行完整 GC。 此臨界值是平臺已知最大 grefs 的 90%:1800模擬器上的 grefs (2000 max), 以及 46800 grefs on 硬體 (最大 52000)。 *注意：* Xamarin 只會計算[JNIEnv](xref:Android.Runtime.JNIEnv)所建立的 grefs, 而且不會知道在進程中建立的任何其他 grefs。 這*只*是啟發學習法。 

執行自動收集時, 會將類似下列的訊息列印到 debug 記錄檔:

```shell
I/monodroid-gc(PID): 46800 outstanding GREFs. Performing a full GC!
```

發生這種情況並不具決定性, 而且可能會在不適當時間發生 (例如, 在圖形轉譯的中間)。 如果您看到此訊息, 您可能會想要在別處執行明確的集合, 或者您可能想要嘗試[減少對等物件的存留期](#Helping_the_GC)。 

## <a name="gc-bridge-options"></a>GC 橋接器選項

Xamarin 提供與 Android 和 Android 執行時間透明的記憶體管理。 它會實作為 Mono 垃圾收集行程的延伸模組, 稱為*GC Bridge*。 

GC 橋接器會在 Mono 垃圾收集期間運作, 並指出哪些對等物件需要使用 Android 執行時間堆積來驗證其「活動」。 GC 橋接器會執行下列步驟 (依序) 來進行這項判斷:

1.  將無法連線之對等物件的 mono 參考圖形, 引發到它們所代表的 JAVA 物件中。 

2.  執行 JAVA GC。

3.  確認哪些物件真正失效。 

這個複雜的程式可讓的`Java.Lang.Object`子類別自由地參考任何物件, 而不會移除任何可系結至C#JAVA 物件的限制。 基於此複雜性, 橋接器程式可能非常昂貴, 而且可能會在應用程式中造成明顯的暫停。 如果應用程式發生重大暫停, 值得調查下列三個 GC 橋接器的其中一個: 

-   **Tarjan** -以[Robert Tarjan 的演算法和回溯參考傳播](https://en.wikipedia.org/wiki/Tarjan's_strongly_connected_components_algorithm)為基礎之 GC Bridge 的全新設計。
    它在模擬的工作負載下具有最佳效能, 但也有更大的實驗性程式碼共用。 

-   **新**的-原始程式碼的主要修復, 它會修正兩個二次方行為實例, 但會保留核心演算法 (根據[Kosaraju 的演算法](https://en.wikipedia.org/wiki/Kosaraju's_algorithm)來尋找強式連接的元件)。 

-   **Old** -原始的實作為 (視為最穩定的三種)。 這是當`GC_BRIDGE`可以接受暫停時, 應用程式應該使用的橋接器。 


要找出哪一個 GC 橋接器的最大效果, 唯一的方法就是在應用程式中實驗並分析輸出。 有兩種方式可收集資料以進行效能評定: 

-   針對每個 GC 橋接器選項**啟用記錄**功能 (如[設定](~/android/internals/garbage-collection.md)一節中所述), 然後捕獲並比較每個設定的記錄輸出。 檢查每`GC`個選項的訊息, 特別是`GC_BRIDGE`訊息。 最多可容忍非互動式應用程式的 150ms, 但針對非常互動的應用程式 (例如遊戲), 暫停上述60毫秒是個問題。 

-   **啟用橋接器帳戶**處理-橋接器帳戶會顯示橋接器程式中每個物件所指向之物件的平均成本。 依大小排序這項資訊, 將會提供有關保留最大數量之額外物件的提示。 


若要指定`GC_BRIDGE`應用程式應該使用的選項、 `bridge-implementation=old` `bridge-implementation=new`傳遞或`bridge-implementation=tarjan` `MONO_GC_PARAMS`環境變數, 例如: 

```shell
MONO_GC_PARAMS=bridge-implementation=tarjan
```

預設設定為**Tarjan**。 如果您發現回歸, 您可能會發現需要將此選項設定為 [**舊**]。 此外, 如果**Tarjan**不會改善效能, 您可以選擇使用較穩定的**舊**選項。 

<a name="Helping_the_GC" />

## <a name="helping-the-gc"></a>協助 GC

有多種方式可協助 GC 減少記憶體使用和收集時間。



### <a name="disposing-of-peer-instances"></a>處置對等實例

GC 具有不完整的進程視圖, 而且在記憶體不足時可能不會執行, 因為 GC 不知道記憶體不足。 

例如, [JAVA](xref:Java.Lang.Object)的實例類型或衍生類型的大小至少為20個位元組 (如有變更, 恕不另行通知等等)。 
[Managed](~/android/internals/architecture.md)可呼叫包裝函式不會加入額外的實例成員, 因此當您的[Android. Bitmap](xref:Android.Graphics.Bitmap)實例參考到 10 mb 的記憶體 blob 時, Xamarin。 Android 的 gc &ndash;不知道 gc 會看到20個位元組的物件, 而將無法判斷它是否連結至 Android 執行時間配置的物件, 而使記憶體維持10MB 的狀態。 

經常需要協助 GC。 不幸的是, *GC。AddMemoryPressure ()* 和*GC。不支援 RemoveMemoryPressure ()* , 因此, 如果您*知道*您剛釋放過大型 JAVA 配置的物件圖形, 您可能需要手動呼叫[GC。Collect ()](xref:System.GC.Collect)以提示 GC 釋放 java 端記憶體, 或者您可以明確處置*JAVA 的物件*子類別, 並中斷受控可呼叫包裝函式與 JAVA 實例之間的對應。 例如, 請參閱[Bug 1084](http://bugzilla.xamarin.com/show_bug.cgi?id=1084#c6)。 


> [!NOTE]
> 處置`Java.Lang.Object`子類別實例時, 您必須*非常*小心。

若要將記憶體損毀的可能性降到最低, 請在`Dispose()`呼叫時觀察下列指導方針。


#### <a name="sharing-between-multiple-threads"></a>在多個執行緒之間共用

如果*JAVA 或受控*實例可能會在多個執行緒之間共用,*則它應該`Dispose()`不是 d*。 例如,[`Typeface.Create()`](xref:Android.Graphics.Typeface.Create*) 
可能會傳回快取的*實例*。 如果多個執行緒提供相同的引數, 則會取得*相同*的實例。 因此, `Dispose()`來自某個執行緒`Typeface`的實例的 ing 可能會使其他執行緒無效, 而這`ArgumentException` `JNIEnv.CallVoidMethod()`會導致中的彼此 (而不是其他執行緒), 因為實例已從另一個執行緒處置。 


#### <a name="disposing-bound-java-types"></a>處置系結的 JAVA 類型

如果實例是系結的 JAVA 類型,*只要*實例不會從 managed 程式碼重複使用 *, 而且*JAVA 實例無法線上程之間共用 (請參閱先前`Typeface.Create()`的討論), 就可以處置實例。 (進行這項判斷可能會很棘手)。下次 JAVA 實例進入 managed 程式碼時, 就會為它建立*新*的包裝函式。 

這通常適用于可繪製資源和其他資源繁重的實例:

```csharp
using (var d = Drawable.CreateFromPath ("path/to/filename"))
    imageView.SetImageDrawable (d);
```

上述是安全的, 因為[CreateFromPath ()](xref:Android.Graphics.Drawables.Drawable.CreateFromPath*)傳回的對等會參考架構對等,*而不*是使用者對等。 在`Dispose()` `using`區塊結尾的呼叫將會中斷受控可[繪製](xref:Android.Graphics.Drawables.Drawable)和架構可[繪製](https://developer.android.com/reference/android/graphics/drawable/Drawable.html)實例之間的關聯性, 讓 JAVA 實例能夠在 Android 執行時間需要時立即收集。 如果對等實例參考的是使用者對等, 這就*不*安全。在這裡, 我們會使用「外部」資訊來得知`Drawable`不能參考使用者對等, 因此呼叫是`Dispose()`安全的。 


#### <a name="disposing-other-types"></a>處置其他類型 

如果實例參考的類型不是 java 類型 (例如自訂`Activity`) 的系結, 除非您*知道*java程式碼不`Dispose()`會在該實例上呼叫覆寫的方法, 否則請不要呼叫。 若未這麼做, 會導致[ `NotSupportedException`s](~/android/internals/architecture.md#Premature_Dispose_Calls)。 

例如, 如果您有自訂的 click 接聽程式:

```csharp
partial class MyClickListener : Java.Lang.Object, View.IOnClickListener {
    // ...
}
```

您*不應*處置此實例, 因為 JAVA 會在未來嘗試對其叫用方法:

```csharp
// BAD CODE; DO NOT USE
Button b = FindViewById<Button> (Resource.Id.myButton);
using (var listener = new MyClickListener ())
    b.SetOnClickListener (listener);
```


#### <a name="using-explicit-checks-to-avoid-exceptions"></a>使用明確檢查來避免例外狀況

如果您已執行了[Java.Lang.Object.Dispose](xref:Java.Lang.Object.Dispose*) JNI, 請避免觸及牽涉到的物件。 這麼做可能會建立一種*雙重處置*情況, 讓您的程式碼可以 (嚴重) 嘗試存取已進行垃圾收集的基礎 JAVA 物件。 這樣做會產生類似下列的例外狀況: 

```shell
System.ArgumentException: 'jobject' must not be IntPtr.Zero.
Parameter name: jobject
at Android.Runtime.JNIEnv.CallVoidMethod
```

當第一次處置物件導致成員變成 null, 然後在這個 null 成員上進行後續的存取嘗試導致擲回例外狀況時, 通常就會發生這種情況。 具體而言, 物件的`Handle` (連結受控實例至其基礎 JAVA 實例) 在第一次處置時失效, 但 managed 程式碼仍會嘗試存取此基礎 java 實例, 即使它已無法再使用也一樣 (請參閱[受控](~/android/internals/architecture.md#Managed_Callable_Wrappers)可呼叫包裝函式, 以深入瞭解 JAVA 實例和受控實例之間的對應。 

避免這個例外狀況的好方法, 是在您`Dispose`的方法中明確驗證受控實例與基礎 JAVA 實例之間的對應是否仍然有效; 也就是檢查物件的`Handle`是否為 null (`IntPtr.Zero`)存取其成員之前。 例如, 下列`Dispose`方法會`childViews`存取物件: 

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

如果初始處置階段`childViews`造成無效`Handle`, `for`迴圈存取將會擲`ArgumentException`回。 藉由在第`Handle`一次`childViews`存取之前新增明確的 null 檢查`Dispose` , 下列方法可防止發生例外狀況: 

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


### <a name="reduce-referenced-instances"></a>減少參考的實例

每當在 GC 期間掃描`Java.Lang.Object`類型或子類別的實例時, 也必須掃描實例所參考的整個*物件圖形*。 物件圖形是「根實例」所指的一組物件實例,*再加上*根實例所參考的所有專案 (以遞迴方式參考)。 

請考慮下列類別:

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

當`BadActivity`進行結構化時, 物件圖形會包含10004實例`BadActivity`(1、 `strings`1、 `string[]` 1x `strings`、10000x 字串實例),*所有*這些都必須在每次`BadActivity`實例已掃描。 

這可能會對您的收集時間造成不利的影響, 因而導致 GC 暫停時間增加。 

您可以藉由*減少*以使用者對等實例為根的物件圖形大小, 來協助 GC。 在上述範例中, 您可以藉由移`BadActivity.strings`至不會繼承自 .java 的個別類別來完成這項作業: 

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


## <a name="minor-collections"></a>次要集合

次要集合可以藉由呼叫 GC 來手動執行[。收集 (0)](xref:System.GC.Collect)。 次要集合的成本較低 (相較于主要集合), 但確實具有相當的固定成本, 因此您不想要經常觸發它們, 而且應該有幾毫秒的暫停時間。 

如果您的應用程式有一項「工作週期」, 在此過程中會進行相同的作業, 建議您在工作週期結束後手動執行次要集合。 範例的責任週期包括: 

-  單一遊戲框架的轉譯迴圈。
-  與指定應用程式對話方塊的整體互動 (開啟、填滿、關閉) 
-  重新整理/同步應用程式資料的一組網路要求。



## <a name="major-collections"></a>主要集合

藉由呼叫 GC, 可以手動執行主要集合[。Collect ()](xref:System.GC.Collect)或`GC.Collect(GC.MaxGeneration)`。 

這應該很少執行, 而且在收集512MB 堆積時, Android 樣式的裝置上可能會有一秒的暫停時間。 

主要集合應僅以手動方式叫用 (如果有的話): 

-   在漫長的工作週期結束後, 如果長時間暫停, 使用者就不會有任何問題。 

-   在覆寫的[OnLowMemory ()](xref:Android.App.Activity.OnLowMemory)方法內。 



## <a name="diagnostics"></a>診斷

若要追蹤全域參考的建立和終結時間, 您可以將 [ [debug .log](~/android/troubleshooting/index.md)系統] 屬性設定為包含[*grf*](~/android/troubleshooting/index.md)和/或[*gc*](~/android/troubleshooting/index.md)。 



## <a name="configuration"></a>組態

您可以藉由設定`MONO_GC_PARAMS`環境變數來設定 Xamarin 垃圾收集行程。 您可以使用[AndroidEnvironment](~/android/deploy-test/environment.md)的組建動作來設定環境變數。

`MONO_GC_PARAMS`環境變數是以逗號分隔的下列參數清單: 

-   `nursery-size` = *大小*:設定托兒所的大小。 大小是以位元組為單位指定, 而且必須是2的乘冪。 尾碼`k` `m`和可以分別用來指定千位、百萬位元和gb。`g` 托兒所是第一代 (共二個)。 較大的托兒所通常會加速程式, 但明顯會使用較多的記憶體。 預設的托兒所大小 512 kb。 

-   `soft-heap-limit` = *大小*:應用程式的目標最大受控記憶體耗用量。 當記憶體使用量低於指定的值時, GC 就會針對執行時間 (較少的集合) 進行優化。 
    超過此限制, GC 已針對記憶體使用量優化 (更多集合)。 

-   `evacuation-threshold` = *閾值*:設定撤離臨界值 (以百分比表示)。 值必須是介於0到100之間的整數。 預設值為66。 如果集合的清除階段發現特定堆積區塊類型的佔用量小於此百分比, 則會在下一個主要集合中為該區塊類型執行複製集合, 藉此將佔用的空間還原至接近 100%。 值為0會關閉撤離。 

-   `bridge-implementation` = *橋接器*執行:這會設定 GC Bridge 選項, 以協助解決 GC 效能問題。 有三個可能的值: [*舊*]、[*新增*]、[ *tarjan*]。

-   `bridge-require-precise-merge`：Tarjan 橋接器包含優化, 在少數情況下, 可能會在第一次變成垃圾之後, 將物件收集到一個 GC。 包括這個選項會停用該優化, 使 Gc 更容易預測, 但可能較慢。

例如, 若要將 GC 設定為具有128mb 的堆積大小限制, 請使用的**組建動作** `AndroidEnvironment`將新檔案新增至您的專案, 其內容如下: 

```shell
MONO_GC_PARAMS=soft-heap-limit=128m
```

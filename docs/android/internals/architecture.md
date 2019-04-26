---
title: 架構
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: ea66cda0e2a1935a430c064c9cebd4134d295729
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954244"
---
# <a name="architecture"></a>架構

在 Mono 執行環境中，執行 Xamarin.Android 應用程式。
此執行環境執行的並行與 Android 執行階段 （藝術） 虛擬機器。 這兩個執行階段環境執行於 Linux 核心之上，並公開 （expose） 給使用者程式碼，可讓開發人員能夠存取基礎系統的各種 Api。 Mono 執行階段是以 C 語言撰寫。

您可以使用[系統](xref:System)， [System.IO](xref:System.IO)， [System.Net](xref:System.Net)和其餘的.NET 類別庫來存取基礎的 Linux 作業系統功能。

在 Android 上，大部分的系統功能，例如音訊、 圖形、 OpenGL 和電話語音的不是直接提供給原生應用程式，它們只透過在其中一個 Android 執行階段 Java Api 公開[Java](https://developer.xamarin.com/api/namespace/Java.Lang/)。 *命名空間或[Android](https://developer.xamarin.com/api/namespace/Android/)。 * 命名空間。 架構大約是像這樣：

[![Mono 和圖案的圖表是核心上方和下方.NET/Java + 繫結](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Xamarin.Android 開發人員存取作業系統透過他們的專業知識的.NET Api 呼叫 （適用於低層級的存取），或使用 Android 命名空間提供橋接器所公開的 Java api 中公開的類別中的各種功能Android 執行階段。

如需 Android 類別與 Android 執行階段類別的通訊方式的詳細資訊請參閱 < [API 設計](~/android/internals/api-design.md)文件。


## <a name="application-packages"></a>應用程式套件

Android 應用程式封裝是使用 ZIP 容器 *.apk*副檔名。 Xamarin.Android 應用程式套件會有相同的結構和配置為一般 Android 套件的詳細資訊，加上下列各項：

-   （包含 IL） 的應用程式組件*預存*內未壓縮*組件*資料夾。 程序期間在版本中啟動組建 *.apk*是*mmap()* ed，到處理程序和組件會從記憶體載入。 這可讓應用程式啟動較快，因為組件不需要在執行之前要擷取。  
-   *注意：* 組件位置資訊，例如[Assembly.Location](xref:System.Reflection.Assembly.Location)並[Assembly.CodeBase](xref:System.Reflection.Assembly.CodeBase)
    *不能指望*發行組建中。 沒有為不同的檔案系統項目，而且有任何可用的位置。


-   內有包含 Mono 執行階段的原生程式庫 *.apk* 。 Xamarin.Android 應用程式必須包含原生程式庫所需/目標 Android 架構，例如*armeabi* ， *armeabi-v7a* ， *x86* 。 Xamarin.Android 應用程式無法在平台上執行，除非它包含適當的執行階段程式庫。


Xamarin.Android 應用程式也包含*Android 可呼叫包裝函式*以允許 Android 呼叫 managed 程式碼。



## <a name="android-callable-wrappers"></a>Android 可呼叫包裝函式

- **Android 可呼叫包裝函式**都[JNI](https://en.wikipedia.org/wiki/Java_Native_Interface)橋接器會使用任何 Android 執行階段需要叫用 managed 程式碼的時間。 Android 可呼叫包裝函式是虛擬的方法可以覆寫，而且可以實作的 Java 介面。 請參閱[Java 整合概觀](~/android/platform/java-integration/index.md)文件，如需詳細資訊。


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>受管理的可呼叫包裝函式

受管理的可呼叫包裝函式是 JNI 橋接器可用來叫用 Android 程式碼，並提供覆寫虛擬方法和實作的 Java 介面支援的 managed 程式碼需要的任何時間。 將整個[Android](https://developer.xamarin.com/api/namespace/Android/)。 * 和相關命名空間是受管理的可呼叫包裝函式透過產生[.jar 繫結](~/android/platform/binding-java-library/index.md)。
受管理的可呼叫包裝函式是負責受管理和 Android 的類型之間轉換，並叫用透過 JNI 基礎的 Android 平台方法。

每個 Java 全域參考，可透過存取建立受管理的可呼叫包裝函式保留[Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/)屬性。 全域的參考用來提供 Java 執行個體與受管理的執行個體之間的對應。 全域參考是有限的資源： 模擬器允許只 2000年存在一次，而大部分的硬體可以一次存在超過 52,000 全域參考的全域參考。

若要追蹤時建立和終結全域的參考，您可以設定[debug.mono.log](~/android/troubleshooting/index.md)系統屬性，以包含[grf](~/android/troubleshooting/index.md)。

全域參考可以明確地呼叫釋放[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)上受管理的可呼叫包裝函式。 這會移除 Java 執行個體與受管理的執行個體之間的對應，並允許要收集的 Java 執行個體。 從 managed 程式碼重新存取 Java 執行個體時，如果新 managed 可呼叫包裝函式會建立它。

執行時必須小心時處置的受管理的可呼叫包裝函式，如果執行個體可以在不小心執行緒之間共用，做為處置執行個體將會影響任何其他執行緒的參考。 最大值，僅限 safety`Dispose()`的執行個體已透過配置`new`*或*方法您*知道*一律配置新的執行個體和其可能未快取執行個體會導致意外的執行個體執行緒之間共用。



## <a name="managed-callable-wrapper-subclasses"></a>管理可呼叫包裝函式的子類別

受管理的可呼叫包裝函式的子類別是可能的所在的 「 有趣 」 的應用程式特定邏輯。 這些包括自訂[Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)子類別 (例如[Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13)預設專案範本中的型別)。 (具體而言，這些是任何*Java.Lang.Object*子類別來完成此動作*不*包含[RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)自訂屬性或[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)已*false*，這是預設值。)

像是 managed 可呼叫包裝函式，管理可呼叫包裝函式的子類別也包含全域參考，可透過存取[Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/)屬性。 就像 managed 可呼叫包裝函式，全域參考可以明確地釋放藉由呼叫[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)。
不同於受管理的可呼叫包裝函式，*小心*應該再處置這類情況下，做為進入*dispose （)*-ing 的執行個體將會中斷 Java 執行個體之間的對應 (的執行個體Android 可呼叫包裝函式） 和受管理的執行個體。


### <a name="java-activation"></a>Java 啟用

當[Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)(ACW) 從 Java 建立、 ACW 建構函式會導致對應 C# 建構函式會叫用。 例如，針對 ACW *MainActivity*會包含預設建構函式會叫用*MainActivity*的預設建構函式。 (這是透過*TypeManager.Activate()* ACW 建構函式中呼叫。)

沒有結果的一個其他建構函式簽章： *（IntPtr，JniHandleOwnership）* 建構函式。 *（IntPtr，JniHandleOwnership）* Java 物件公開給 managed 程式碼和管理可呼叫包裝函式就需要建構管理 JNI 控制代碼時，會叫用建構函式。 這通常是自動完成。

有兩種案例所在 *（IntPtr，JniHandleOwnership）* 建構函式必須以手動方式提供受管理的可呼叫包裝函式的子類別上：

1. [來取代](https://developer.xamarin.com/api/type/Android.App.Application/)子類別化。 *應用程式*是特殊; 預設值*應用程式*建構函式會*永遠不會*叫用，而[（IntPtr，JniHandleOwnership） 必須改為提供的建構函式](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. 用來自基底類別建構函式的虛擬方法引動過程。


請注意，(2) 是溢位的抽象概念。 在 Java 中，和在 C# 中，從建構函式呼叫虛擬方法一律會叫用最具衍生性的方法實作。 例如， [TextView (內容，AttributeSet，int) 建構函式](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/)叫用虛擬方法[TextView.getDefaultMovementMethod()](https://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod())，這會繫結為[TextView.DefaultMovementMethod 屬性](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/)。
因此，如果型別[LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) (1) 要[子類別 TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26)、 (2)[覆寫 TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)，以及 (3)[啟動的執行個體透過 XML 類別](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29)覆寫*DefaultMovementMethod*之前 ACW 建構函式有機會執行，而且它會發生之前，會叫用屬性C#建構函式有機會執行。

這支援具現化執行個體 LogTextBox 透過[LogTextView （IntPtr，JniHandleOwnership）](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28)建構函式時 ACW LogTextBox 執行個體第一次進入 managed 程式碼，然後叫用[(內容，IAttributeSet，int) LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41)建構函式*相同的執行個體上*ACW 建構函式在執行時。

事件順序：

1.  載入 XML 的版面配置[ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41)。

2.  Android 配置物件圖形中，會具現化，和具現化的執行個體*monodroid.apidemo.LogTextBox* ，如 ACW *LogTextBox* 。

3.  *Monodroid.apidemo.LogTextBox*建構函式執行[android.widget.TextView](https://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29)建構函式。

4.  *TextView*建構函式會叫用*monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* 。

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* 叫用*LogTextBox.n_getDefaultMovementMethod()* ，這會叫用*TextView.n_GetDefaultMovementMethod()* ，這會叫用[Java.Lang.Object.GetObject&lt;TextView&gt; （處理 JniHandleOwnership.DoNotTransfer）](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) 。

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;（)* 檢查是否有對應的 C# 執行個體*處理*。 如果沒有，它會傳回它。 在此案例中，沒有，因此*Object.GetObject&lt;T&gt;（)* 必須建立一個。

7.  *Object.GetObject&lt;T&gt;（)* 會尋找*LogTextBox （IntPtr，JniHandleOwneship）* 建構函式，會叫用它，則會建立之間的對應*處理*和建立的執行個體，並傳回建立的執行個體。

8.  *TextView.n_GetDefaultMovementMethod()* 叫用*LogTextBox.DefaultMovementMethod*屬性 getter。

9.  控制權會返回*android.widget.TextView*建構函式執行完成。

10. *Monodroid.apidemo.LogTextBox*建構函式執行時，叫用*TypeManager.Activate()* 。

11. *LogTextBox (內容，IAttributeSet，int)* 建構函式執行 *(7) 中建立相同的執行個體上*。

12. 如果 （IntPtr，JniHandleOwnership） 找不到建構函式，則會擲回 System.MissingMethodException](xref:System.MissingMethodException)。

<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>過早的 dispose （） 呼叫

沒有 JNI 控制代碼與對應的 C# 執行個體之間的對應。 Java.Lang.Object.Dispose() 會中斷此對應。 如果 JNI 控制代碼對應已中斷後，進入 managed 程式碼，它看起來像 Java 啟動過程中，而 *（IntPtr，JniHandleOwnership）* 建構函式會檢查並加以叫用。 如果不存在的建構函式，將會擲回例外狀況。

例如，假設下列的受管理的可呼叫 Wraper 子類別：

```csharp
class ManagedValue : Java.Lang.Object {

    public string Value {get; private set;}

    public ManagedValue (string value)
    {
        Value = value;
    }

    public override string ToString ()
    {
        return string.Format ("[Managed: Value={0}]", Value);
    }
}
```

如果我們建立執行個體，dispose （），並會造成管理可呼叫包裝函式以重新建立：

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

程式會隨之中止：

```shell
E/mono    ( 2906): Unhandled Exception: System.NotSupportedException: Unable to activate instance of type Scratch.PrematureDispose.ManagedValue from native handle 4051c8c8 --->
System.MissingMethodException: No constructor found for Scratch.PrematureDispose.ManagedValue::.ctor(System.IntPtr, Android.Runtime.JniHandleOwnership)
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateProxy (System.Type type, IntPtr handle, JniHandleOwnership transfer) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   --- End of inner exception stack trace ---
E/mono    ( 2906):   at Java.Interop.TypeManager.CreateInstance (IntPtr handle, JniHandleOwnership transfer, System.Type targetType) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object.GetObject (IntPtr handle, JniHandleOwnership transfer, System.Type type) [0x00000] in <filename unknown>:0
E/mono    ( 2906):   at Java.Lang.Object._GetObject[IJavaObject] (IntPtr handle, JniHandleOwnership transfer) [0x00000
```

如果包含子類別 *（IntPtr，JniHandleOwnership）* 建構函式，則會顯示*新*將建立型別的執行個體。 如此一來，執行個體似乎會 「 遺失 」 的所有執行個體資料，因為它是新的執行個體。 （請注意，此值是 null）。

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

只有*dispose （)* 的管理可呼叫包裝函式的子類別，當您知道不會使用 Java 物件，或子類別未包含任何執行個體資料，以及一 *（IntPtr，JniHandleOwnership）* 提供的建構函式。



## <a name="application-startup"></a>應用程式啟動

當活動、 服務時，等啟動時，Android 會先檢查以查看是否有執行裝載活動/service/等等的程序。如果沒有這類處理序存在，則會建立新的處理序， [AndroidManifest.xml](https://developer.android.com/guide/topics/manifest/manifest-intro.html)是讀中指定的型別[ /manifest/application/@android:name ](https://developer.android.com/guide/topics/manifest/application-element.html#nm)屬性會載入並具現化。 下一步，所指定的所有型別[ /manifest/application/provider/@android:name ](https://developer.android.com/guide/topics/manifest/provider-element.html#nm)屬性值會具現化，並且具有其[ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/)叫用方法。 藉由新增的 Xamarin.Android 攔截到這個*mono。MonoRuntimeProvider* *ContentProvider*來建置程序期間的 AndroidManifest.xml。 *Mono。MonoRuntimeProvider.attachInfo()* 方法會負責 Mono 執行階段載入處理序。
若要使用 Mono，此點之前的任何嘗試將會失敗。 (*注意*:這就是為什麼類型的子類別[來取代](https://developer.xamarin.com/api/type/Android.App.Application/)需要提供[（IntPtr，JniHandleOwnership） 建構函式](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103)、 建立應用程式執行個體時才可以初始化 Mono。)

一旦處理程序初始化完成之後，`AndroidManifest.xml`查閱來尋找活動/service/等啟動的類別名稱。 例如， [ /manifest/application/activity/@android:name屬性](https://developer.android.com/guide/topics/manifest/activity-element.html#nm)用來判斷用來載入的活動名稱。 活動，此類型必須繼承[android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)。
透過載入指定的型別[Class.forName()](https://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (這需要的類型為 Java 類型，因此 Android 可呼叫包裝函式)，然後具現化。 建立 Android 可呼叫包裝函式執行個體將會觸發建立對應的 C# 類型的執行個體。 Android 會接著叫用[Activity.onCreate(Bundle)](https://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) ，這會對應[Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)要叫用，以及您要關閉的競爭情況。

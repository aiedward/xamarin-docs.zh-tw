---
title: 架構
ms.prod: xamarin
ms.assetid: 7DC22A08-808A-DC0C-B331-2794DD1F9229
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: f4be88a1eabb3fa3cca733690a3f097a03516272
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="architecture"></a>架構

單聲道執行環境中，執行 Xamarin.Android 應用程式。
此執行環境執行的並行與 Android 執行階段 （圖案） 虛擬機器。 這兩個執行階段環境 Linux 核心上執行，並公開各種 Api，可讓開發人員能夠存取的基礎系統的使用者程式碼。 單聲道的執行階段是以 C 語言撰寫。

您可以使用[系統](http://msdn.microsoft.com/en-us/library/system.aspx)， [System.IO](http://msdn.microsoft.com/en-us/library/system.io.aspx)， [System.Net](http://msdn.microsoft.com/en-us/library/system.net.aspx)和其餘的.NET 類別程式庫來存取基礎的 Linux 作業系統設備。

在 Android 上，大部分系統設備，像是音訊、 圖形、 OpenGL 和電話語音的不是直接適用於原生應用程式，只能透過位於其中一個 Android 執行階段 Java Api [Java](https://developer.xamarin.com/api/namespace/Java.Lang/)。 *命名空間或[Android](https://developer.xamarin.com/api/namespace/Android/)。 * 命名空間。 架構是大致如下：

[![單聲道和圖表封面核心上方和下方.NET/Java + 繫結](architecture-images/architecture1.png)](architecture-images/architecture1.png#lightbox)

Xamarin.Android 開發人員存取作業系統藉由呼叫.NET Api 時，才會知道 （適用於低層級的存取），或使用 Android 的命名空間提供 Java api 所公開的橋接器中公開的類別中的各種功能Android 的執行階段。

如需 Android 類別如何與 Android 的執行階段類別進行通訊的詳細資訊請參閱[API 設計](~/android/internals/api-design.md)文件。


## <a name="application-packages"></a>應用程式套件

Android 應用程式封裝是以 ZIP 容器*.apk*檔案副檔名。 Xamarin.Android 應用程式封裝擁有相同的結構與配置一般 Android 套件的詳細資訊，具有下列功能：

-   應用程式組件 （包含 IL）*儲存*內未壓縮*組件*資料夾。 版本中的啟動建立程序期間*.apk*是*mmap()* ed 到處理程序和組件是從記憶體載入。 這可讓更快速的應用程式啟動，如此不需要在執行前要擷取的組件。 - *注意：*組件位置資訊，例如[Assembly.Location](https://developer.xamarin.com/api/property/System.Reflection.Assembly.Location/)和[Assembly.CodeBase](https://developer.xamarin.com/api/property/System.Reflection.Assembly.CodeBase/)
    *無法依賴*版本中組建。 為不同的檔案系統項目，它們不存在，而且必須沒有可用的位置。


-   包含單聲道的執行階段原生程式庫內有*.apk* 。 Xamarin.Android 應用程式必須包含原生程式庫預期/目標 Android 架構，例如*armeabi* ， *armeabi v7a* ， *x86* 。 Xamarin.Android 應用程式無法在平台上執行，除非它包含適當的執行階段程式庫。


Xamarin.Android 應用程式也包含*Android 可呼叫包裝函式*允許 Android 呼叫 managed 程式碼。



## <a name="android-callable-wrappers"></a>Android 的可呼叫包裝函式

- **Android 的可呼叫包裝函式**是[JNI](http://en.wikipedia.org/wiki/Java_Native_Interface)橋接器會用來叫用 managed 程式碼需要 Android 的執行階段。 Android 的可呼叫包裝函式會如何虛擬方法會覆寫和 Java 介面可以實作。 請參閱[Java 整合概觀](~/android/platform/java-integration/index.md)文件，如需詳細資訊。


<a name="Managed_Callable_Wrappers" />

## <a name="managed-callable-wrappers"></a>受管理的可呼叫包裝函式

受管理的可呼叫包裝函式是 JNI 橋接器會使用 managed 程式碼需要 Android 程式碼叫用，並覆寫虛擬方法和實作的 Java 介面提供支援的任何時間。 整個[Android](https://developer.xamarin.com/api/namespace/Android/)。 * 和相關命名空間是受管理的可呼叫包裝函式來產生[d 繫結](~/android/platform/binding-java-library/index.md)。
受管理的可呼叫包裝函式負責管理和 Android 類型之間轉換以及叫用 JNI 透過基礎的 Android 平台方法。

每個 Java 全域參考，可透過存取建立受管理的可呼叫包裝函式保留[Android.Runtime.IJavaObject.Handle](https://developer.xamarin.com/api/property/Android.Runtime.IJavaObject.Handle/)屬性。 全域參考用來提供 Java 執行個體與受管理的執行個體之間的對應。 全域參考是有限的資源： 模擬器允許只 2000年全域的參考，大部分的硬體允許一次存在超過 52,000 全域參考時，一次存在。

若要追蹤時建立和終結全域的參考，您可以設定[debug.mono.log](~/android/troubleshooting/index.md)系統屬性，以包含[gref](~/android/troubleshooting/index.md)。

全域參考可以明確地釋放藉由呼叫[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)上受管理的可呼叫包裝函式。 這將會移除 Java 執行個體與受管理的執行個體之間的對應，並允許收集的 Java 執行個體。 從 managed 程式碼重新存取 Java 執行個體時，如果新 managed 可呼叫包裝函式會建立它。

執行時必須小心時處置的受管理的可呼叫包裝函式，如果執行個體可以在不小心執行緒之間共用，做為處理這些執行個體將會影響任何其他執行緒的參考。 最大值，僅限 safety`Dispose()`的執行個體已透過配置`new`*或*方法從您*知道*一律配置新的執行個體和其可能未快取執行個體造成意外執行緒之間共用的執行個體。



## <a name="managed-callable-wrapper-subclasses"></a>受管理的子類別可呼叫包裝函式

受管理的可呼叫包裝函式的子類別是可能居住的 「 有趣 」 的特定應用程式邏輯。 這些包括自訂[Android.App.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)子類別 (例如[Activity1](https://github.com/xamarin/monodroid-samples/blob/master/HelloM4A/Activity1.cs#L13)預設專案範本中的型別)。 (具體而言，這些是任何*Java.Lang.Object*子類別完成這件事*不*包含[RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)自訂屬性或[RegisterAttribute.DoNotGenerateAcw](https://developer.xamarin.com/api/property/Android.Runtime.RegisterAttribute.DoNotGenerateAcw/)是*false*，預設值。)

Like managed 可呼叫包裝函式，管理可呼叫包裝函式的子類別也包含全域參考，可透過存取[Java.Lang.Object.Handle](https://developer.xamarin.com/api/property/Java.Lang.Object.Handle/)屬性。 如同 managed 可呼叫包裝函式，以參考全域可以明確地釋放藉由呼叫[Java.Lang.Object.Dispose()](https://developer.xamarin.com/api/member/Java.Lang.Object.Dispose/)。
不同於受管理的可呼叫包裝函式，*十分小心*之前處置做為這類情況下，應該採取*dispose （)*正執行之執行個體將會中斷 Java 執行個體之間的對應 (執行個體Android 的可呼叫包裝函式） 和受管理的執行個體。


### <a name="java-activation"></a>Java 啟用

當[Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)(ACW) 會建立從 Java，ACW 建構函式會導致對應 C# 建構函式會叫用。 例如，針對 ACW *MainActivity*會包含將會叫用預設建構函式*MainActivity*的預設建構函式。 (這透過完成*TypeManager.Activate()*內 ACW 建構函式呼叫。)

沒有結果的一個其他建構函式簽章： *（IntPtr、 JniHandleOwnership）*建構函式。 *（IntPtr、 JniHandleOwnership）* Java 物件公開給 managed 程式碼和管理可呼叫包裝函式就需要建構來管理 JNI 控制代碼時，會叫用建構函式。 這通常會自動完成。

有兩種案例中的*（IntPtr、 JniHandleOwnership）*建構函式必須以手動方式提供受管理的可呼叫包裝函式的子類別上：

1. [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/)子類別化。 *應用程式*是特殊; 預設*應用程式*建構函式會*永遠不會*叫用，而[（IntPtr、 JniHandleOwnership） 必須改為提供建構函式](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/SanityTests/Hello.cs#L105).

2. 基底類別建構函式從虛擬方法引動過程。


請注意，(2) 是抽象的溢位。 在 Java 中，和在 C# 中，從建構函式呼叫虛擬方法一定會叫用最常衍生的方法實作。 例如， [TextView (內容中，AttributeSet，int) 建構函式](https://developer.xamarin.com/api/constructor/Android.Widget.TextView.TextView/p/Android.Content.Context/Android.Util.IAttributeSet/System.Int32/)叫用虛擬方法[TextView.getDefaultMovementMethod()](http://developer.android.com/reference/android/widget/TextView.html#getDefaultMovementMethod())，做為繫結[TextView.DefaultMovementMethod 屬性](https://developer.xamarin.com/api/property/Android.Widget.TextView.DefaultMovementMethod/)。
因此，如果型別[LogTextBox](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs) (1) 要[子類別 TextView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L26)、 (2)[覆寫 TextView.DefaultMovementMethod](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L45)，和 (3)[啟動該執行個體類別透過 XML、](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Resources/layout/log_text_box_1.xml#L29)覆寫*DefaultMovementMethod* ACW 建構函式有機會執行，就會發生之前的 C# 建構函式有機會執行之前，會叫用屬性。

藉由執行個體化 LogTextBox 執行個體支援這種透過[LogTextView （IntPtr、 JniHandleOwnership）](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L28)建構函式時 ACW LogTextBox 執行個體第一次進入 managed 程式碼，以及然後叫用[LogTextBox (內容中，IAttributeSet，int)](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox.cs#L41)建構函式*相同的執行個體上*ACW 建構函式時執行。

事件順序：

1.  版面配置 XML 載入到[ContentView](https://github.com/xamarin/monodroid-samples/blob/f01b5c31/ApiDemo/Text/LogTextBox1.cs#L41)。

2.  Android 配置物件圖形中，會具現化，和具現化的執行個體*monodroid.apidemo.LogTextBox* ，如 ACW *LogTextBox* 。

3.  *Monodroid.apidemo.LogTextBox*建構函式執行[android.widget.TextView](http://developer.android.com/reference/android/widget/TextView.html#TextView%28android.content.Context,%20android.util.AttributeSet%29)建構函式。

4.  *TextView*建構函式會叫用*monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* 。

5.  *monodroid.apidemo.LogTextBox.getDefaultMovementMethod()* invokes *LogTextBox.n_getDefaultMovementMethod()* , which invokes *TextView.n_GetDefaultMovementMethod()* , which invokes [Java.Lang.Object.GetObject&lt;TextView&gt; (handle, JniHandleOwnership.DoNotTransfer)](https://developer.xamarin.com/api/member/Java.Lang.Object.GetObject%7BT%7D/p/System.IntPtr/Android.Runtime.JniHandleOwnership/) .

6.  *Java.Lang.Object.GetObject&lt;TextView&gt;（)*檢查，以查看是否有已對應的 C# 執行個體*處理*。 如果沒有，它會傳回。 在此案例中，沒有，因此*Object.GetObject&lt;T&gt;（)*必須建立一個。

7.  *Object.GetObject&lt;T&gt;（)*尋找*LogTextBox （IntPtr、 JniHandleOwneship）*建構函式，會叫用它，然後建立之間的對應*處理*和建立的執行個體，並傳回建立的執行個體。

8.  *TextView.n_GetDefaultMovementMethod()*叫用*LogTextBox.DefaultMovementMethod*屬性 getter。

9.  控制權會傳回*android.widget.TextView*建構函式，完成執行。

10. *Monodroid.apidemo.LogTextBox*建構函式執行時，叫用*TypeManager.Activate()* 。

11. *LogTextBox (內容中，IAttributeSet，int)*建構函式執行*相同 (7) 中建立的執行個體上*。

12. ...


如果 （IntPtr、 JniHandleOwnership） 找不到建構函式，則[System.MissingMethodException](https://developer.xamarin.com/api/type/System.MissingMethodException/)就會擲回。


<a name="Premature_Dispose_Calls" />

### <a name="premature-dispose-calls"></a>過早 dispose （） 呼叫

沒有 JNI 控制代碼與對應的 C# 執行個體之間的對應。 Java.Lang.Object.Dispose() 中斷此對應。 若對應已中斷後，JNI 控點會進入 managed 程式碼，它看起來像 Java 啟用和*（IntPtr、 JniHandleOwnership）*建構函式會檢查並加以叫用。 如果建構函式不存在，將會擲回例外狀況。

例如，假設下列管理可呼叫 Wraper 子類別：

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

如果我們建立的執行個體，其中的 dispose （），而且會管理可呼叫包裝函式來重新建立：

```csharp
var list = new JavaList<IJavaObject>();
list.Add (new ManagedValue ("value"));
list [0].Dispose ();
Console.WriteLine (list [0].ToString ());
```

將死程式：

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

如果子類別並包含*（IntPtr、 JniHandleOwnership）*建構函式，則*新*將建立之類型的執行個體。 如此一來，執行個體將會出現 「 遺失 」 的所有執行個體資料，因為它是的新執行個體。 （請注意此值為 null）。

```shell
I/mono-stdout( 2993): [Managed: Value=]
```

只有*dispose （)*的 managed 可呼叫包裝函式的子類別，當您知道不會再，使用 Java 物件，或子類別未包含任何執行個體資料和*（IntPtr、 JniHandleOwnership）*已提供建構函式。



## <a name="application-startup"></a>應用程式啟動

當活動、 服務時，等啟動時，會先檢查 Android 以查看是否有執行裝載活動/service/等等的程序。如果沒有這類處理序存在，則會建立新的處理序， [AndroidManifest.xml](http://developer.android.com/guide/topics/manifest/manifest-intro.html)讀中指定的類型是[ /manifest/application/@android:name ](http://developer.android.com/guide/topics/manifest/application-element.html#nm)屬性載入，並具現化。 下一步，指定的所有型別[ /manifest/application/provider/@android:name ](http://developer.android.com/guide/topics/manifest/provider-element.html#nm)屬性值會具現化，而且具有其[ContentProvider.attachInfo%28)](https://developer.xamarin.com/api/member/Android.Content.ContentProvider.AttachInfo/p/Android.Content.Context/Android.Content.PM.ProviderInfo/)叫用方法。 藉由新增的 Xamarin.Android 攔截到這個*單聲道。MonoRuntimeProvider* *ContentProvider*至 AndroidManifest.xml 建置程序期間。 *單聲道。MonoRuntimeProvider.attachInfo()*方法負責將單聲道的執行階段載入處理序。
若要在這個點之前使用單聲道任何嘗試會失敗。 (*注意*： 這是為什麼類型的子類別[Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/)需要提供[（IntPtr、 JniHandleOwnership） 建構函式](https://github.com/xamarin/monodroid-samples/blob/a9e8ef23/SanityTests/Hello.cs#L103)，如應用程式執行個體建立可以初始化 Mono 之前）。

處理初始化完成後，請`AndroidManifest.xml`諮詢找不到活動/service/等等。 若要啟動的類別名稱。 例如， [ /manifest/application/activity/@android:name屬性](http://developer.android.com/guide/topics/manifest/activity-element.html#nm)用來判斷用來載入的活動名稱。 活動，此類型必須繼承[android.app.Activity](https://developer.xamarin.com/api/type/Android.App.Activity/)。
透過載入指定的型別[Class.forName()](http://developer.android.com/reference/java/lang/Class.html#forName(java.lang.String)) (這需要型別是 Java 類型，因此 Android 可呼叫包裝函式)，然後具現化。 建立 Android 可呼叫包裝函式執行個體將會觸發建立對應的 C# 類型的執行個體。 Android 將會再叫用[Activity.onCreate(Bundle)](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) ，這將會導致對應[Activity.OnCreate(Bundle)](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)要叫用，而您關閉到競爭情況。
